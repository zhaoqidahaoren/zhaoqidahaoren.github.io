---
title: SDK Reference
category: CocoApp
order: 1
---

# CocoAPP API

## 快速入门

### 简介

CocoAPP API 是一套设计用于实现第三方应用（即CocoAPP）与CocoCat平台之间通信的接口。这套API支持从基础数据交换到执行复杂操作的各种功能调用。根据功能的不同，API接口被分为不同的等级，每个等级的接口访问都需要相应等级的官方授权。



### 安装

要开始使用CocoAPP API，首先需要集成 `MessageHandler.js` 库到您的项目中。这个库可以从官方网站下载。一旦下载，您就可以将它包含在您的项目中，并开始使用。以下是在 Vue 中引用的示例：

```javascript
import { MessageHandler } from "@/utils/MessageHandler";
```

然后，您可以全局初始化一个 `MessageHandler` 对象，以便在整个应用中使用。

```javascript
let messageHandler = new MessageHandler();
```

`MessageHandler` 对象包含一个 `sendMessageToParent` 方法，用于调用 CocoAPP API 方法。此方法的参数包括：

- `cmd`：CocoAPP API 接口方法的名称。
- `payload`：传递给 CocoAPP API 接口方法的参数对象。
- `callback`：回调函数，用于在 CocoAPP 完成操作后执行相应的业务逻辑。

示例：

```javascript
let cmd = "onload";
let payload = {
    "data": {
        "name": "Test1",
        "version": "v1.0.0"
    },
    "messageId": new Date().getTime()
};
let callback = (response) => {
    // 处理来自 CocoAPP API 的响应
    console.log("Received response from CocoAPP API:", response);
};

messageHandler.sendMessageToParent(cmd, payload, callback);
```



## API接口说明

我们的API接口根据不同的等级划分为Level 0到Level 9，不同等级的用户可以调用不同的功能。请注意，除了Level 0外，其他等级的访问都需要具备相应等级的官方授权签名认证。

### 调用说明

#### 请求参数

请求参数采用JSON对象的格式，包括以下字段：

| 字段      | 类型   | 必填 | 描述                                                         |
| --------- | ------ | ---- | ------------------------------------------------------------ |
| data      | String | 是   | 用于调用API的参数数据，具体数据结构根据API接口的`cmd`不同而变化。 |
| sign      | Object | 否   | 证书数据，用于特定消息的证书认证。包含以下字段：<br/>- `content`：签名原始数据，包括子站地址、有效期、级别、版本号等信息，采用JSON字符串格式。<br/>- `signature`：签名数据，以Base64编码格式表示。<br/>对于Level 0，请求参数payload里不会对sign做校验，不需要传入sign参数。 |
| messageId | Int    | 否   | 消息ID，以时间戳（毫秒级）表示。用于重复调用API，对应回调方法。 |

完整请求结构如下：

```json
{
    "data": {
        // 参数数据...
    },
    "sign": {
        "content": "",
        "signature": ""
    },
    "messageId": 0
}
```

#### 返回参数

返回参数采用JSON对象的格式，包括以下字段：

| 字段      | 类型   | 描述                                                |
| --------- | ------ | --------------------------------------------------- |
| code      | Int    | 响应的状态码，请参阅下面的状态码表以获取详细信息。  |
| cmd       | String | 调用的API接口方法名称。                             |
| result    | Object | 接收回调的数据，不同的`cmd`返回不同的数据格式。     |
| messageId | Int    | 消息ID，以毫秒级表示，对应请求参数中的`messageId`。 |

完整响应结构如下：

```json
{
    "code": 200,
    "cmd": "",
    "result": 具体数据...,
    "messageId": 0
}
```

#### 状态码表

以下是状态码表，用于解释响应中的状态码：

| code  | 描述                     |
| ----- | ------------------------ |
| 200   | 请求成功                 |
| 30001 | 请求格式不正确或参数无效 |
| 30002 | 未提供证书               |
| 30003 | 证书校验异常             |
| 30004 | API方法不存在            |
| 30005 | 获取资源不存在           |
| 30006 | chainId异常              |
| 30007 | 下载失败                 |
| 30100 | 异常错误                 |



## 接口详情

### onload

用于在 CocoAPP 页面的 `window.onload` 事件触发后执行必要的操作，主要目的是装载和更新 CocoAPP 的数据。

> **重要提示：为了保证 CocoAPP 在 CocoCat 上的正常显示，启动 CocoAPP 时必须调用一次 `onload` 方法。未调用此方法可能导致 CocoAPP 显示异常。**

#### 等级

Level 0

#### 请求参数

| 字段    | 类型   | Required | 描述                                                         |
| ------- | ------ | -------- | ------------------------------------------------------------ |
| name    | String | true     | 指定的 CocoAPP 名称。用于标识当前正在加载或更新的应用。      |
| version | String | true     | CocoAPP 的当前应用版本号。此参数有助于在 CocoAPP 升级后验证加载的版本是否为最新。 |

#### 返回参数

无

#### 示例

```javascript
javascriptCopy code
let payload = {
  "data": {
    "name": "Test1",
    "version": "v1.0.0"
  }
};

messageHandler.sendMessageToParent("onload", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "onload",
  //   "messageId": 0
  // }
});
```



### connectCocoPay

用于连接 CocoPay 钱包，主要支持以太系公链。连接采用 `Trust Wallet`，一款支持以太坊和多链的钱包应用程序。

**注意：**只有在调用此接口并成功返回后，才视为连接成功。链接成功后，根据不同的链，对 `window.ethereum` 或 `window.tronWeb` 进行赋值：

- 对于 Ethereum 系的链：

  ```javascript
  window.ethereum = window.parent.top.ethereum;
  ```

- 对于 Tron 链：

  ```javascript
  window.tronWeb = window.parent.top.tronWeb;
  ```

完成连接后，可进行常规 DAPP 操作，如使用 `web3.js`、`ethers.js` 等库。

#### 等级

Level 0

#### 支持的公链

| **公链全称**     | 公链缩写符号 |
| ---------------- | ------------ |
| Ethereum         | ETH          |
| BNB Chain        | BSC          |
| Polygon          | POLYGON      |
| Avalanche-C      | AVAXC        |
| TRON             | TRON         |
| Arbitrum One     | ARBITRUM     |
| Poly Smart Chain | PSC          |
| Viction          | VIC          |

#### 请求参数

| 字段      | 类型  | Required | 描述                                                         |
| --------- | ----- | -------- | ------------------------------------------------------------ |
| chainList | Array | true     | 传入链的缩写符号，参考支持的公链表。传入多个链，CocoPay 会显示多个公链选择。 |

#### 返回参数

| 字段      | 类型   | 描述               |
| --------- | ------ | ------------------ |
| chainType | String | 返回链接的链缩写   |
| account   | String | 返回链接的账户地址 |

#### 示例

```javascript
javascriptCopy code
let payload = {
  "data": {
    "chainList": ["ETH", "BSC"],
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("connectCocoPay", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "connectCocoPay",
  //   "result": {
  //			"chainType": "ETH",
  //      "account": "0xbF579fe8539D45....."
	//	  },
  //   "messageId": 1703143355158
  // }
})
```



### getSafeAreaInsets

用于返回移动设备顶部和底部的安全边界尺寸。

#### 等级

Level 0

#### 请求参数

无

#### 返回参数

| 字段           | 类型 | 描述                 |
| -------------- | ---- | -------------------- |
| safeAreaTop    | Int  | 顶部安全区域的高度值 |
| safeAreaBottom | Int  | 底部安全区域的高度值 |

#### 使用示例

```javascript
messageHandler.sendMessageToParent("getSafeAreaInsets", {}, function (res) {
  // 返回示例
  // {
  //   "code": 200,
  //   "cmd": "getSafeAreaInsets",
  //   "result": {
  //     "safeAreaTop": 80,
  //     "safeAreaBottom": 40  
  //   }
  // }
});
```



### getLanguage

用于获取 CocoCat 的当前语言设置。

#### 等级

Level 0

#### 请求参数

无

#### 返回参数

| 字段   | 类型   | 描述             |
| ------ | ------ | ---------------- |
| result | String | 当前的语言代码。 |

#### 支持的语言

| 语言代码 | 语言名称 |
| -------- | -------- |
| en       | 英语     |
| zh-cn    | 简体中文 |
| zh-tw    | 繁體中文 |
| th       | 泰语     |
| es       | 西班牙语 |
| ko       | 韩语     |
| ja       | 日语     |
| ar       | 阿拉伯语 |

#### 示例

```javascript
messageHandler.sendMessageToParent("getLanguage", {}, function (res) {
  // 返回示例
  // {
  //   "code": 200,
  //   "cmd": "getLanguage",
  //   "result": "en"
  // }
});
```



### readFile

用于从 CocoAPP 下读取所有资源文件。

> 注意：读取资源文件太大会直接影响到 CocoAPP 的性能。

#### 等级

Level 0

#### 请求参数

| 字段 | 类型   | Required | 描述                                                         |
| ---- | ------ | -------- | ------------------------------------------------------------ |
| path | String | true     | 资源文件的相对路径，以 CocoAPP 地址开始，文件名跟随，路径开头不应加上 "/"。无法用于读取文件夹。示例：<br/>- 读取 CocoAPP 资源：`1BX9T97qS47zN1Wqqv2dfGiR3ahgUqiGRX/test.txt`<br/>- 读取 CocoAPP 文件夹资源：`1BX9T97qS47zN1Wqqv2dfGiR3ahgUqiGRX/common/test.txt` |
| type | String | false    | 文件的类型。对于文本文件，默认为 UTF-8 编码，此项不必填写。对于需要以 Base64 格式返回的文件（如图片、视频等），此参数应设置为 `Base64`。 |

#### 返回参数

| 字段   | 类型   | 描述                   |
| ------ | ------ | ---------------------- |
| result | String | 读取到的资源文件内容。 |

#### 示例

```javascript
javascriptCopy code
let payload = {
  "data": {
    "path": "1BX9T97qS47zN1Wqqv2dfGiR3ahgUqiGRX/icon.png",
    "type": "Base64"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("readFile", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "readFile",
  //   "result": "iVBORw0KGgoAAAANSUhEUgAAA9gAAABYCAY....",
  //   "messageId": 1703143355158
  // }
});
```



### openURL

用于打开指定的第三方网址，实现对外部 HTTPS 网站的跳转。为保证安全性，仅支持打开使用 HTTPS 协议的网址。

#### 等级

Level 0

#### 请求传参

| 字段 | 类型   | Required | 描述                            |
| ---- | ------ | -------- | ------------------------------- |
| url  | String | true     | 要打开的第三方 HTTPS 网址链接。 |

#### 返回参数

无

#### 示例

```javascript
let payload = {
  "data": {
    "url": "https://www.google.com"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("openURL", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "openURL",
  //   "messageId": 1703143355158
  // }
});
```



### scanQRCode

用于启动扫描二维码功能。

#### 等级

Level 0

#### 请求参数

无

#### 返回参数

| 字段   | 类型   | 描述                 |
| ------ | ------ | -------------------- |
| result | String | 扫码完成后返回的值。 |

#### 示例

```javascript
messageHandler.sendMessageToParent("scanQRCode", {}, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "scanQRCode",
  //   "result": "Hello World!"
  // }
})
```



### copyToClipboard

用于复制文字到剪贴板。

#### 等级

Level 0

#### 请求参数

| 字段 | 类型   | Required | 描述             |
| ---- | ------ | -------- | ---------------- |
| text | String | true     | 复制的文本内容。 |

#### 返回参数

无

#### 示例

```javascript
let messageHandler = new MessageHandler();
let payload = {
  "data": {
    "text": "Hello World!",
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("copyToClipboard", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "copyToClipboard",
  //   "messageId": 1703143355158
  // }
})
```



### generateQRCode

用于生成二维码，允许通过指定文本内容创建相应的二维码图像。

#### 等级

Level 0

#### 请求参数

| 字段 | 类型   | Required | 描述                         |
| ---- | ------ | -------- | ---------------------------- |
| text | String | true     | 需要转换成二维码的文本内容。 |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | String | 生成的二维码图像的 Base64 编码字符串，包括数据类型和编码前缀，例如 `data:image/jpeg;base64,`，后跟二维码图像的 Base64 编码字符串。 |

#### 示例

```javascript
javascriptCopy code
let payload = {
  "data": {
    "text": "Hello World!"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("generateQRCode", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "generateQRCode",
  //   "result": "data:image/jpeg;base64,iVBORw0KGgoAAAANSUhEUgAAA9gAAABYCAY....",
  //   "messageId": 1703143355158
  // }
});
```



### saveImage

用于将图片保存到相册。

#### 等级

Level 0

#### 请求参数

| 字段  | 类型   | 必填 | 描述                                                         |
| ----- | ------ | ---- | ------------------------------------------------------------ |
| type  | String | 是   | 数据类型，可选值：<br/>- `base64`：图片为Base64格式，必须附加URL前缀，格式为`data:[<mediatype>][;base64],<data>`。<br/>- `path`：本地图片或视频的路径。使用`downloadFile`方法下载文件后，通过相对路径从默认下载目录中读取图片以保存到相册。 |
| image | String | 是   | 图片的内容。支持的图片格式包括PNG，GIF，JPEG。               |

> **注意**：当`type`为`path`时，路径应为相对路径，指向默认下载目录中的文件。例如，下载的图片名为`coco.png`，则路径应为`coco.png`；若下载资源为zip文件并解压后，图片路径可能为`image/coco.png`。

#### 返回参数

无

#### 示例

```javascript
javascript
let payload = {
  "data": {
    "type": "base64",
    "image": "data:image/jpeg;base64,iVBORw0KGgoAAAANSUhEUgAAA9gAAABYCAY...."
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("saveImage", payload, (res) => {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "saveImage",
  //   "messageId": 1703143355158
  // }
});
```



### saveVideo

用于将视频保存到相册。

#### 等级

Level 0

#### 请求参数

| 字段  | 类型   | 必填 | 描述                                                         |
| ----- | ------ | ---- | ------------------------------------------------------------ |
| type  | String | 是   | 数据类型，可选值：<br/>- `base64`：视频为Base64格式，必须附加URL前缀，格式为`data:[<mediatype>][;base64],<data>`。<br/>- `path`：本地视频的路径。使用`downloadFile`方法下载文件后，通过相对路径从默认下载目录中读取视频以保存到相册。 |
| video | String | 是   | 视频的内容。支持的视频格式为MP4、MOV。                       |

> **注意**：当`type`为`path`时，路径应为相对路径，指向默认下载目录中的文件。例如，下载的视频名为`coco.mp4`，则路径应为`coco.mp4`。

#### 返回参数

无

#### 示例

```javascript
let payload = {
  "data": {
    "type": "base64",
    "video": "data:video/mp4;base64,AAAAIGZ0eXBpc29tAAACAGlzb21..."
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("saveVideo", payload, (res) => {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "saveVideo",
  //   "messageId": 1703143355158
  // }
});
```



### getAccount

用于获取该 CocoAPP 下的账户地址，确保每个 CocoAPP 下的用户身份唯一。不同的 CocoAPP 会有不同的账户地址，反映了用户在每个应用中的独立身份。

> 注意：若卸载某个 CocoAPP 超过7天后再次下载，其账户地址会更新为新的；否则，账户地址保持不变。

#### 等级

Level 0

#### 请求参数

| 字段 | 类型 | Required | 描述                                                         |
| ---- | ---- | -------- | ------------------------------------------------------------ |
| type | Int  | false    | 请求类型。默认返回 BTC 地址；若传入1，则返回 ECC 公钥（Base64 编码字符串）。 |

#### 返回参数

| 字段   | 类型   | 描述                   |
| ------ | ------ | ---------------------- |
| result | String | 返回的账户地址或公钥。 |

#### 示例

```javascript
messageHandler.sendMessageToParent("getAccount", {}, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "getAccount",
  //   "result": "1Cg7aGLnLAjXJiYCGZpbqGEz8SLD2v3Qij"
  // }
});
```



### setExtendedData

此接口允许存储 CocoAPP 的扩展数据，为每个 CocoAPP 提供了最多1M的存储空间。请注意，每次存储操作都会替换之前的数据。

#### 等级

Level 0

#### 请求参数

| 字段   | 类型   | Required | 描述                         |
| ------ | ------ | -------- | ---------------------------- |
| extend | Object | true     | 需要存储的任意类型扩展数据。 |

#### 返回参数

无

#### 示例

```javascript
let payload = {
  "data": {
    "extend": "{\"address\":\"1Cg7aGLnLAjXJiYCGZpbqGEz8SLD2v3Qij\"}"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("setExtendedData", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "setExtendedData",
  //   "messageId": 1703143355158
  // }
});
```



### getExtendedData

用于从 CocoAPP 中获取先前存储的扩展数据。

#### 等级

Level 0

#### 请求参数

无

#### 返回参数

| 字段   | 类型   | 描述                 |
| ------ | ------ | -------------------- |
| result | Object | 获取存储的扩展数据。 |

#### 示例

```javascript
messageHandler.sendMessageToParent("getExtendedData", {}, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "getExtendedData",
  //   "result": "{\"address\":\"1Cg7aGLnLAjXJiYCGZpbqGEz8SLD2v3Qij\"}"
  // }    
})
```



### generateSignature

用于对指定的文本消息生成 ECDSA 数字签名。签名过程基于 `secp256k1` 椭圆曲线，并利用从 `getAccount` 方法获取的 CocoAPP 账户进行签名，确保了消息的完整性和来源验证，同时与 CocoAPP 账户保持一致性。

#### 等级

Level 0

#### 请求参数

| 字段    | 类型   | 必填 | 描述               |
| ------- | ------ | ---- | ------------------ |
| message | String | 是   | 待签名的文本消息。 |

#### 返回参数

| 字段   | 类型   | 描述                                 |
| ------ | ------ | ------------------------------------ |
| result | String | 生成的数字签名，以 Base64 形式编码。 |

#### 示例

```javascript
let payload = {
  "data": {
    "message": "Hello World!"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("generateSignature", payload, (res) => {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "generateSignature",
  //   "result": "3045022100aaabbbcccdddee....",
  //   "messageId": 1703143355158
  // }    
});
```



### verifySignature

用于验证由 `generateSignature` 方法生成的数字签名的有效性。它根据提供的原始消息和签名数据来确认签名是否有效，确保了数据完整性和来源验证的一致性。

#### 等级

Level 0

#### 请求参数

| 字段          | 类型   | 必填 | 描述                               |
| ------------- | ------ | ---- | ---------------------------------- |
| message       | String | 是   | 需要验证签名的原始文本消息。       |
| signatureData | String | 是   | 签名数据，以 Base64 编码的字符串。 |

#### 返回参数

| 字段   | 类型    | 描述                                                  |
| ------ | ------- | ----------------------------------------------------- |
| result | Boolean | 验证结果，`true` 表示签名有效，`false` 表示签名无效。 |

#### 示例

```javascript
let payload = {
  "data": {
    "message": "Hello World!",
    "signatureData": "3045022100aaabbbcccdddee...."
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("verifySignature", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "verifySignature",
  //   "result": true,
  //   "messageId": 1703143355158
  // }    
});
```



### encrypt

用于提供文本消息的加密功能，采用椭圆曲线集成加密方案（ECIES）对文本消息进行加密。加密操作保障了消息内容的安全性，适用于需要保密传输的信息。加密结果将以 Base64 编码的字符串形式返回，便于存储或进一步的传输。

#### 等级

Level 0

#### 请求参数

| 字段    | 类型   | 必填 | 描述                 |
| ------- | ------ | ---- | -------------------- |
| message | String | 是   | 需要加密的文本消息。 |

#### 返回参数

| 字段   | 类型   | 描述                                 |
| ------ | ------ | ------------------------------------ |
| result | String | 加密后的消息，返回为 Base64 字符串。 |

#### 示例

```javascript
let payload = {
  "data": {
    "message": "Hello World!"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("encrypt", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "encrypt",
  //   "result": "roZskbq2AKNzRitvIh0a....",
  //   "messageId": 1703143355158
  // }    
});
```



### decrypt

用于解密使用 `encrypt` 接口加密的文本，确保加密数据的安全性和可逆性。通过提供加密后的密文（Base64 编码字符串），接口将还原并返回原始的明文信息。

#### 等级

Level 0

#### 请求参数

| 字段          | 类型   | 必填 | 描述                                       |
| ------------- | ------ | ---- | ------------------------------------------ |
| encryptedData | String | 是   | 待解密的文本，必须是 Base64 编码的字符串。 |

#### 返回参数

| 字段   | 类型   | 描述               |
| ------ | ------ | ------------------ |
| result | String | 解密后的原始明文。 |

#### 示例

```javascript
let payload = {
  "data": {
    "encryptedData": "roZskbq2AKNzRitvIh0a...."
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("decrypt", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "decrypt",
  //   "result": "Hello World!",
  //   "messageId": 1703143355158
  // }    
});
```



### downloadFile（功能未实现）

用于通过 HTTPS GET 请求向 CocoAPP 下载资源文件。重要的是，CocoCat 一次只能处理一个下载任务。如果在一个下载操作正在进行时启动另一个下载（无论是在同一个或不同的 CocoAPP 中），系统将自动中止当前的下载任务，并将其标记为失败。因此，确保在任何时刻只有一个下载任务在进行是至关重要的。

请记住，下载过程能够在 CocoAPP 后台静默运行，不会因为应用被关闭或切换到后台而中断。但如果应用被系统强制停止或因其他原因长时间未能运行，这可能会影响下载任务的继续执行，最终导致下载失败。此外，一旦 CocoCat 被重启，所有未完成的下载任务将不会自动恢复，需要重新发起下载。

新的下载请求会取消并清除任何正在进行的下载，这可能导致下载失败。网络连接的稳定性也是成功下载的一个关键因素。

> 注意事项：
>
> - 该接口仅接受 GET 方法发起的下载请求。
> - 下载过程中不会验证文件的完整性，下载成功的唯一标准是文件传输的完成。
> - 尽管没有设置文件大小限制，较大的文件可能会增加下载失败的概率。
> - 完成下载的文件可以通过 CocoAPP 内置的本地服务器 `http://127.0.0.1:55502/` 访问，访问时需要精确的文件路径和名称。

#### 等级

Level 0

#### 请求参数

| 字段           | 类型        | 必填 | 描述                                                         |
| -------------- | ----------- | ---- | ------------------------------------------------------------ |
| url            | String      | 是   | 指定资源文件的 HTTPS URL。                                   |
| fileName       | String      | 是   | 文件的完整名称，包括其扩展名。                               |
| authorizations | ArrayString | 否   | 如果需要，提供下载鉴权信息的数组，每项包含请求头的 `key` 和对应的 `value`。 |

#### 返回参数

无

#### 示例

```javascript
let payload = {
  "data": {
    "url": "https://example.com/resource.zip",
    "fileName": "resource.zip",
    "authorizations": [
      { "key": "Authorization", "value": "Bearer YOUR_TOKEN" }
    ]
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("downloadFile", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "downloadFile",
  //   "messageId": 1703143355158
  // }
});
```



### checkDownloadStatus（功能未实现）

此接口用于检查指定资源文件的下载状态，作为 `downloadFile` 功能的补充。它允许开发者查询某个特定文件是否已经成功下载到 CocoAPP。

#### 等级

Level 0

#### 请求参数

| 字段     | 类型   | 必填 | 描述                     |
| -------- | ------ | ---- | ------------------------ |
| fileName | String | 是   | 要检查下载状态的文件名。 |

#### 返回参数

| 字段   | 类型    | 描述                                                         |
| ------ | ------- | ------------------------------------------------------------ |
| result | Boolean | 表示下载状态：`true` 代表文件已成功下载；`false` 代表文件尚未下载或下载未完成。 |

#### 示例

```javascript
let payload = {
  "data": {
    "fileName": "example.zip"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("checkDownloadStatus", payload, (res) => {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "checkDownloadStatus",
  //   "result": true,
  //   "messageId": 1703143355158
  // }
});
```



### extractFile（功能未实现）

允许对通过 `downloadFile` 接口下载完成的 ZIP 格式压缩包进行解压操作。解压过程会将压缩包内的文件解压到与压缩包同目录下的文件夹中，文件夹名称与压缩包名称相同。

此功能支持处理 ZIP 格式的文件，为应用提供了一种方便的方式来处理下载后的压缩内容。

#### 等级

Level 0

#### 请求参数

| 字段        | 类型   | 必填 | 描述                                                         |
| ----------- | ------ | ---- | ------------------------------------------------------------ |
| archivePath | String | 是   | 指向需解压的 ZIP 压缩包的相对路径，该路径相对于应用的默认下载目录。例如，若文件位于下载目录下，则路径可能为 `myArchive.zip`。 |

#### 返回参数

无

#### 示例

```javascript
javascriptCopy code
let payload = {
  "data": {
    "archivePath": "example.zip"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("extractFile", payload, (res) => {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "extractFile",
  //   "messageId": 1703143355158
  // }
});
```



### registerService

用于注册CocoApp的自服务。该方法需配合 `checkServiceStatus` 方法使用以验证注册状态。

#### 等级

Level 8

#### 请求参数

| 字段       | 类型   | 必填 | 描述                                                   |
| ---------- | ------ | ---- | ------------------------------------------------------ |
| serviceKey | String | 是   | 用于注册自服务的标识符。                               |
| signKey    | String | 是   | 使用CocoApp部署账户私钥对 `serviceKey` 进行的BTC签名。 |

#### 响应参数

无

#### 示例

```javascript
let payload = {
  "data": {
    "serviceKey": "eyJhdXRoc2lnbiI6Ik1FVUNJUURxR....",
    "signKey": "xxxxxxxxx"
  },
  "sign": {
        "content": "xxxxxxxx",
        "signature": "xxxxxxx"
  },
  "messageId": new Date().getTime(),
  
};

messageHandler.sendMessageToParent("registerService", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "registerService",
  //   "messageId": 1703143355158
  // }    
});
```



### checkServiceStatus

用于查询已注册自服务的状态。

#### 等级

Level 8

#### 请求参数

无

#### 响应参数

| 字段   | 类型    | 描述                                                        |
| ------ | ------- | ----------------------------------------------------------- |
| result | Boolean | 表示自服务注册状态，`true` 为注册成功，`false` 为注册失败。 |

#### 示例

```javascript
let payload = {
  "sign": {
        "content": "xxxxxxxx",
        "signature": "xxxxxxx"
  },
  "messageId": new Date().getTime(),
};
messageHandler.sendMessageToParent("checkServiceStatus", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "checkServiceStatus",
  //   "result": true
  // }    
});
```



### sendServiceMessage

用于通过P2P通信发送自服务的消息，便于进行各种服务调用和数据传输。

#### 等级

Level 8

#### 请求参数

| 字段    | 类型   | 必填 | 描述                                 |
| ------- | ------ | ---- | ------------------------------------ |
| type    | String | 是   | 请求的协议类型，目前仅支持 `HTTP`。  |
| content | Object | 是   | 根据请求类型定义的具体请求数据结构。 |

当`type`为`HTTP`时，`content`的数据结构如下：

| 字段    | 类型   | 必填 | 描述                                                   |
| ------- | ------ | ---- | ------------------------------------------------------ |
| method  | String | 是   | HTTP方法类型，可选值：`POST`、`GET`、`PUT`、`DELETE`。 |
| url     | String | 是   | 请求的URL路径。                                        |
| headers | Object | 否   | 请求头部，应为JSON格式键值对。                         |
| data    | Object | 否   | 请求体中的数据，为JSON格式。                           |

#### 示例

```json
{
    "type": "HTTP",
    "content": {
        "method": "POST",
        "url": "/all",
        "headers": {
            "Content-Type": "application/json"
        },
        "data": {
            "id": 1
        }
    }
}
```

#### 响应参数

| 字段   | 类型   | 描述                                     |
| ------ | ------ | ---------------------------------------- |
| result | Object | 操作的返回结果，内容会根据实际响应变化。 |

#### 示例

```javascript
let payload = {
    "data": {
        "type": "HTTP",
        "content": {
            "method": "POST",
            "url": "/add",
            "headers": {
                "Content-Type": "application/json"
            },
            "data": {
                "id": "1",
                "name": "Alice"
            }
        }
    },
  	"sign": {
        "content": "xxxxxxxx",
        "signature": "xxxxxxx"
  	},
    "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("sendServiceMessage", payload, function (res) {
  // 返回结果
  // {
  //   "code": 200,
  //   "cmd": "sendServiceMessage",
  //   "result": {
  //				"code": 200,
  //        "message": "Success",  
  //        "data": "" 
	//		},
  //   "messageId": 1703143355158
  // }    
});
```



### PP接口，不会对外公布

### postdata

**postdata** 接口用于像服务器发送数据

#### 等级

Level 9（**这里需要确认**）

#### 请求参数

| 字段 | 类型   | 描述                          |
| ---- | ------ | ----------------------------- |
| data | String | 要发送的数据内容(json string) |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:'ok'} err是null,res是'ok'表示发送成功，err不是null表示没有发送成功 |



### gethostsession

**gethostsession** 接口用于获取服务器session

#### 等级

Level 9（**这里需要确认**）

#### 请求参数

| 字段 | 类型   | 描述                 |
| ---- | ------ | -------------------- |
| data | String | 服务器域名或者ip地址 |

#### 返回参数

| 字段        | 类型   | 描述                                                         |
| ----------- | ------ | ------------------------------------------------------------ |
| result      | Object | {err:null,res:''} err是null表示获取成功,res返回的session对象，err不是null表示没有获取成功 |
| res:cookie  | String | 服务器返回的cookie                                           |
| res:session | String | 服务器返回的session                                          |
| res:sid     | Int    | sid                                                          |



### writeFile

**writeFile** 接口用于修改安卓ppsetting配置文件

#### 等级

Level 9

#### 请求参数

| 字段 | 类型   | 描述                                                         |
| ---- | ------ | ------------------------------------------------------------ |
| data | String | {dns:"",thrift:"",game:0,local:1}该对象的json字符串，dns固定是8.8.8.8,thrift是端口一般是1080,game是模式，0是全局模式，1是专属模式，local固定是1。 |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:'ok'} err是null,res是'ok'表示修改成功，err不是null表示没有修改成功 |



### ppgethosts

**ppgethosts** 用于获取安卓pp节点

#### 等级

Level 9

#### 请求参数

无

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:''} err是null,表示获取成功，res返回的是节点信息列表，以逗号分割，err不是null表示没有获取成功 |



### ppsethost

**ppsethost** 接口用于设置安卓pp节点

#### 等级

Level 9

#### 请求参数

| 字段  | 类型   | 描述       |
| ----- | ------ | ---------- |
| index | String | 节点的索引 |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:'ok'} err是null,res是'ok'表示切换成功，err不是null表示没有切换成功 |



### ppnewuser

**ppnewuser** 接口用户创建pp btc账号

#### 等级

Level 9

#### 请求参数

无

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:'ok'} err是null,res是'ok'表示创建成功，err不是null表示没有创建成功 |



### openiospp

**openiospp** 接口用于开启iospp

#### 等级

Level 9

#### 请求参数

| 字段       | 描述   | 描述                |
| ---------- | ------ | ------------------- |
| DomainName | String | 开启iospp的节点名称 |
| port       | String | 开启iospp的端口     |
| password   | String | 开启iospp的密码     |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:'ok'} err是null,res是'ok'表示开启成功，err不是null表示没有开启成功 |




### getiosppstatus

**getiosppstatus** 接口用获取iospp开启状态

#### 等级

Level 9

#### 请求参数

无

#### 返回参数

| 字段   | 类型   | 描述                                                      |
| ------ | ------ | --------------------------------------------------------- |
| result | Object | {err:null,res:1} err是null,res是1表示开启，res是0表示关闭 |



### closeiospp

**closeiospp** 接口用于关闭iospp

#### 等级

Level 9

#### 请求参数

无

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:'ok'} err是null,res是'ok'表示关闭成功，err不是null表示没有关闭成功 |



### ppgetsetting

**ppgetsetting** 接口用获取pp证书信息

#### 等级

Level 9

#### 请求参数

无

#### 返回参数

| 字段           | 类型   | 描述                                                         |
| -------------- | ------ | ------------------------------------------------------------ |
| result         | Object | {err:null,res:''} err是null表示获取成功，res返回的是证书信息 |
| res:addrkey    | String | 账户BTC地址                                                  |
| res:expired    | Int    | 账户过期时间                                                 |
| res:ladder     | Int    | 账户购买套餐 0:没有购买 1:30天 2:180天 3:366天               |
| res:chargedate | Int    | 账户充值时间                                                 |
| res:active     | Int    | 账户是否成功激活                                             |



### ppstop

**ppstop** 接口用于关闭安卓pp

#### 等级

Level 9

#### 请求参数

无

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:'ok'} err是null,res是'ok'表示关闭成功，err不是null表示没有关闭成功 |



### childsign

**childsign** 接口用于pp签名(ecc签名)

#### 等级

Level 9

#### 请求参数

| 字段 | 类型   | 描述                             |
| ---- | ------ | -------------------------------- |
| data | String | 签名的内容（base64之后的字符串） |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:''} err是null表示签名成功，res返回的是签名的结果，err不是null表示签名失败 |



### pptempsign

**pptempsign** 接口用于pp签名（btc签名）

#### 等级

Level 9

#### 请求参数

| 字段 | 类型   | 描述                             |
| ---- | ------ | -------------------------------- |
| data | String | 签名的内容（base64之后的字符串） |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:''} err是null表示签名成功，res返回的是签名的结果，err不是null表示签名失败 |



### ppstart

**ppstart** 接口用于开启安卓pp前配置参数

#### 等级

Level 9

#### 请求参数

| 字段 | 类型   | 描述                                                         |
| ---- | ------ | ------------------------------------------------------------ |
| data | String | 参数字符串（data:proxylist.conf路径 , thrift端口1080, game:0全局 1专属, localmoving:1） |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:''} err是null，res是"ok"表示参数配置成功，err不是null表示参数配置失败 |



### startpp

**startpp** 接口用于开启安卓pp

#### 等级

Level 9

#### 请求参数

| 字段       | 类型   | 描述                            |
| ---------- | ------ | ------------------------------- |
| _dns       | String | dns                             |
| _proxyAPKs | String | 要代理apk列表，字符串以逗号分割 |
| _thrift    | Int    | 端口 一般来说是1080             |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:''} err是null，res是"ok"表示参数开启成功，err不是null表示开启失败 |



### pprecharge

**pprecharge** 用于充值或者查询充值状态之后修改本地证书

#### 等级

Level 9

#### 请求参数

| 字段 | 类型   | 描述                                                         |
| ---- | ------ | ------------------------------------------------------------ |
| data | String | {domains:"",ladder:1,time:0,active:0}参数字字符串（domains服务器信息,ladder:套餐挡位,time:充值时间,active:0是否激活） |

#### 返回参数

| 字段   | 类型   | 描述                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | {err:null,res:''} err是null，res是"ok"表示参数修改成功，err不是null表示修改失败 |



### proxycertificate

**proxycertificate** 用于主站发送证书信息给子站

#### 等级

Level 9

#### 请求参数

| 字段 | 类型   | 描述                 |
| ---- | ------ | -------------------- |
| data | String | 证书信息的json字符串 |

#### 返回参数

无






