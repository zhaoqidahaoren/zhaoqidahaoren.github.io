---
title: SDK Reference
category: CocoApp
order: 2
permalink: /SDKReference/
clickable: true
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

**注意：**只有在调用此接口并成功返回后，才视为连接成功。链接成功后，根据不同的链，对 `window.ethereum` 或 `indoww.tronWeb` 进行赋值：

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

用于过对指定的文本消息生成 ECDSA 数字签名。签名程基于 `secp256k1` 椭圆曲线，并利用从 `getAccount` 方法获取的 CocoAPP 账户进行签名，确保了消息的完整性和来源验证，同时与 CocoAPP 账户保持一致性。

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


