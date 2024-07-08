---
title: SDK Reference
category: CocoApp
order: 2
permalink: /SDKReference/
ulOrder: 1
clickable: true
---

# CocoAPP API

## Quick Start

### Introduction

CocoAPP API is a set of interfaces designed to facilitate communication between third-party applications (i.e., CocoAPP) and the CocoCat platform. This API supports various functions from basic data exchange to executing complex operations. Depending on the functionality, the API interfaces are categorized into different levels, and access to each level requires the corresponding official authorization.



### Installation

To start using the CocoAPP API, you first need to integrate the MessageHandler.js library into your project. This library can be downloaded from the official website. Once downloaded, you can include it in your project and start using it. Here is an example of how to import it in Vue:

```javascript
import { MessageHandler } from "@/utils/MessageHandler";
```

Next, you can initialize a  `MessageHandler` object globally to use it throughout your application.

```javascript
let messageHandler = new MessageHandler();
```

The `MessageHandler` object contains a  `sendMessageToParent` method used to call CocoAPP API methods. The parameters for this method include:

- `cmd`：The name of the CocoAPP API interface method.
- `payload`：The parameters object to be passed to the CocoAPP API interface method.
- `callback`：A callback function to execute the corresponding business logic after CocoAPP completes the operation.

Example：

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
     // Handle the response from the CocoAPP API
    console.log("Received response from CocoAPP API:", response);
};

messageHandler.sendMessageToParent(cmd, payload, callback);
```



## API Interface Description

Our API interfaces are categorized from Level 0 to Level 9, and users with different levels can call different functions. Please Note that, except for Level 0, access to other levels requires corresponding level official authorization signature certification.

### Call Description

#### Request Parameters

Request parameters are in JSON object format, including the following fields:

| Field     | Type   | Required | Description                                                  |
| --------- | ------ | -------- | ------------------------------------------------------------ |
| data      | String | Yes      | The parameter data for calling the API; the specific data structure varies depending on the `cmd` of the API interface. |
| sign      | Object | No       | Certificate data for specific message authentication. It includes the following fields:<br/>- `content`: The original data for the signature, including substation address, validity period, level, version number, etc., in JSON string format.<br/>- `signature`: The signature data, encoded in Base64 format.<br/>For Level 0, the `sign` parameter in the Request Parameters payload will Not be validated, so the `sign` parameter is Not required. |
| messageId | Int    | No       | Message ID, represented as a timestamp (in milliseconds). Used for repeated API calls and corresponds to the callback method. |


Complete request structure：

```json
{
    "data": {
        // Parameter data...
    },
    "sign": {
        "content": "",
        "signature": ""
    },
    "messageId": 0
}
```

#### Return Parameters

Response parameters are in JSON object format, including the following fields:

| Field     | Type   | Description                                                  |
| --------- | ------ | ------------------------------------------------------------ |
| code      | Int    | Response status code. Refer to the status code table below for more details. |
| cmd       | String | Name of the called API method.                               |
| result    | Object | Data received in the callback; the format varies depending on the `cmd`. |
| messageId | Int    | Message ID, represented in milliseconds, corresponds to the `messageId` in the Request Parameters. |


Complete response structure：

```json
{
    "code": 200,
    "cmd": "",
    "result": Specific data...,
    "messageId": 0
}
```

#### Status Codes Table

Here is the status codes table to explain the status codes in the response:

| Code  | Description                                    |
| ----- | ---------------------------------------------- |
| 200   | Request successful                             |
| 30001 | Incorrect request format or invalid parameters |
| 30002 | Certificate Not provided                       |
| 30003 | Certificate verification error                 |
| 30004 | API method does Not exist                      |
| 30005 | Requested resource does Not exist              |
| 30006 | chainId error                                  |
| 30007 | Download failed                                |
| 30100 | Exception error                                |




## Interface Details

### onload

Used to execute necessary operations after the `window.onload` event of the CocoAPP page is triggered. The main purpose is to load and update the data of CocoAPP.

> **Important Note：To ensure the Normal display of CocoAPP on CocoCat, you must call the  `onload` method once when launching CocoAPP. Failure to call this method may result in abNormal display of CocoAPP.**

#### Level

Level 0

#### Request Parameters

| Field   | Type   | Required | Description                                                  |
| ------- | ------ | -------- | ------------------------------------------------------------ |
| name    | String | true     | The specified CocoAPP name. Used to identify the currently loading or updating application. |
| version | String | true     | The current application version of CocoAPP. This parameter helps to verify whether the loaded version is the latest after the CocoAPP upgrade. |

#### Return Parameters

None

#### Example

```javascript
javascriptCopy code
let payload = {
  "data": {
    "name": "Test1",
    "version": "v1.0.0"
  }
};

messageHandler.sendMessageToParent("onload", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "onload",
  //   "messageId": 0
  // }
});
```



### connectCocoPay

Used to connect to the CocoPay wallet, mainly supporting Ethereum chains. The connection uses  `Trust Wallet`，an application that supports Ethereum and multi-chain.

**Note：**The connection is considered successful only after calling this interface and returning successfully. After successful connection, assign to `window.ethereum` or `indoww.tronWeb` based on different chains:

- For Ethereum chains:

  ```javascript
  window.ethereum = window.parent.top.ethereum;
  ```

- For Tron chains:

  ```javascript
  window.tronWeb = window.parent.top.tronWeb;
  ```

After the connection is complete, you can perform regular DAPP operations, such as using libraries like  `web3.js`、`ethers.js` .

#### Level

Level 0

#### Supported Chains

| Full name of the blockchain | Abbreviation of the blockchain symbol |
| --------------------------- | ------------------------------------- |
| Ethereum                    | ETH                                   |
| BNB Chain                   | BSC                                   |
| Polygon                     | POLYGON                               |
| Avalanche-C                 | AVAXC                                 |
| TRON                        | TRON                                  |
| Arbitrum One                | ARBITRUM                              |
| Poly Smart Chain            | PSC                                   |
| Viction                     | VIC                                   |


#### Request Parameters:

| Field     | Type  | Required | Description                                                  |
| --------- | ----- | -------- | ------------------------------------------------------------ |
| chainList | Array | true     | The abbreviation symbols of the chains passed in, refer to the supported blockchains table. When passing multiple chains, CocoPay will display multiple blockchain options. |

#### Return Parameters:

| Field      | Type  | Description
| --------- | ------ | ------------------ |
| chainType | String | Returns the linked chain abbreviation   |
| account   | String | Returns the linked account address |


#### Example:

```javascript
javascriptCopy code
let payload = {
  "data": {
    "chainList": ["ETH", "BSC"],
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("connectCocoPay", payload, function (res) {
  // Return result
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

Used to return the safe area dimensions at the top and bottom of mobile devices.

#### Level

Level 0

#### Request Parameters:

None

#### Return Parameters:

| Field          | Type | Description                              |
| -------------- | ---- | ---------------------------------------- |
| safeAreaTop    | Int  | The height value of the top safe area    |
| safeAreaBottom | Int  | The height value of the bottom safe area |

#### Usage Example:

```javascript
messageHandler.sendMessageToParent("getSafeAreaInsets", {}, function (res) {
  // Return example
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

Used to get the current language setting of CocoCat.

#### Level

Level 0

#### Request Parameters

None

#### Return Parameters

| Field  | Type   | Description            |
| ------ | ------ | ---------------------- |
| result | String | Current language code. |

#### Supported Languages

| Language code | Language name       |
| ------------- | ------------------- |
| en            | English             |
| zh-cn         | Simplified Chinese  |
| zh-tw         | Traditional Chinese |
| th            | Thai                |
| es            | Spanish             |
| ko            | Korean              |
| ja            | Japanese            |
| ar            | Arabic              |

#### Example

```javascript
messageHandler.sendMessageToParent("getLanguage", {}, function (res) {
  // Return example
  // {
  //   "code": 200,
  //   "cmd": "getLanguage",
  //   "result": "en"
  // }
  // }
});
```



### readFile

Used to read all resource files under CocoAPP.

> Note：Reading too large resource files will directly affect the performance of CocoAPP.

#### Level

Level 0

#### Request Parameters:

#### Request Parameters

| Field | Type   | Required | Description                                                  |
| ----- | ------ | -------- | ------------------------------------------------------------ |
| path  | String | true     | The relative path to the resource file, starting with the CocoAPP address, followed by the file name. Do Not prepend a "/" to the path. CanNot be used to read folders. Examples:<br/>- Reading a CocoAPP resource: `1BX9T97qS47zN1Wqqv2dfGiR3ahgUqiGRX/test.txt`<br/>- Reading a resource from a CocoAPP folder: `1BX9T97qS47zN1Wqqv2dfGiR3ahgUqiGRX/common/test.txt` |
| type  | String | false    | The type of the file. For text files, UTF-8 encoding is assumed by default, and this parameter is Not necessary. For files that need to be returned in Base64 format (such as images, videos, etc.), this parameter should be set to `Base64`. |

#### Return Parameters

| Field  | Type   | Description                                                  |
| ------ | ------ | ------------------------------------------------------------ |
| result | String | The content of the read resource file. Depending on the `type` parameter, this could be the raw file content (for text files) or the Base64 encoded string of the file (for images, videos, etc.). |

#### Example

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
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "readFile",
  //   "result": "iVBORw0KGgoAAAANSUhEUgAAA9gAAABYCAY....",
  //   "messageId": 1703143355158
  // }
});
```



### openURL

Used to open a specified third-party URL, achieving a jump to an external HTTPS website. For security reasons, only URLs using the HTTPS protocol are supported.

#### Level

Level 0

#### Request Parameters

#### Request Parameters

| Field | Type   | Required | Description                                            |
| ----- | ------ | -------- | ------------------------------------------------------ |
| url   | String | true     | The HTTPS URL link of the third-party website to open. |

#### Return Parameters

None

#### Example

```javascript
let payload = {
  "data": {
    "url": "https://www.google.com"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("openURL", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "openURL",
  //   "messageId": 1703143355158
  // }
});
```


### scanQRCode

Used to start the QR code scanning function.

#### Level

Level 0

#### Request Parameters

None

#### Return Parameters

| Field  | Type   | Description                        |
| ------ | ------ | ---------------------------------- |
| result | String | The value returned after scanning. |

#### example

```javascript
messageHandler.sendMessageToParent("scanQRCode", {}, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "scanQRCode",
  //   "result": "Hello World!"
  // }
})
```


### copyToClipboard

Used to copy text to the clipboard.

#### Level

Level 0

#### Request Parameters:

| Field | Type   | Required | Description              |
| ----- | ------ | -------- | ------------------------ |
| text  | String | true     | The text content copied. |

#### Return Parameters:

None

#### Example

```javascript
let messageHandler = new MessageHandler();
let payload = {
  "data": {
    "text": "Hello World!",
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("copyToClipboard", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "copyToClipboard",
  //   "messageId": 1703143355158
  // }
})
```


### generateQRCode

Used to generate a QR code, allowing you to create a corresponding QR code image through the specified text content.

#### Level

Level 0

#### Request Parameters

| Field | Type   | Required | Description                                      |
| ----- | ------ | -------- | ------------------------------------------------ |
| text  | String | true     | The text content to be converted into a QR code. |

#### Return Parameters

| Field  | Type   | Description                                                  |
| ------ | ------ | ------------------------------------------------------------ |
| result | String | The Base64 encoded string of the generated QR code image, including the data type and encoding prefix, such as  `data:image/jpeg;base64,`，followed by the Base64 encoded string of the QR code image. |

#### Example

```javascript
let payload = {
  "data": {
    "text": "Hello World!"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("generateQRCode", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "generateQRCode",
  //   "result": "data:image/jpeg;base64,iVBORw0KGgoAAAANSUhEUgAAA9gAAABYCAY....",
  //   "messageId": 1703143355158
  // }
});
```


### saveImage

Used to save images to the album.

#### Level

Level 0

#### Request Parameters

| Field | Type   | Required | Description                                                  |
| ----- | ------ | -------- | ------------------------------------------------------------ |
| type  | String | Yes      | Data type, optional values: <ul><li>**base64**: The image is in Base64 format and must be accompanied by a URL prefix in the format `data:[<mediatype>][;base64],<data>`.</li><li>**path**: The path of the local image or video. After downloading the file using the `downloadFile` method, read the image from the default download directory using a relative path to save it to the album.</li></ul> |
| image | String | Yes      | The content of the image. Supported image formats include PNG, GIF, JPEG. |

> **Note**: When `type` is `path`, the path should be a relative path pointing to the file in the default download directory. For example, if the downloaded image is named `coco.png`, the path should be `coco.png`. If the downloaded resource is a zip file and unzipped, the image path might be `image/coco.png`.


#### Return Parameters


None

#### example

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
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "saveImage",
  //   "messageId": 1703143355158
  // }
});
```



### saveVideo

Used to save videos to the album.

#### Level

Level 0

#### Request Parameters

| Field | Type   | Required | Description                                                  |
| ----- | ------ | -------- | ------------------------------------------------------------ |
| type  | String | Yes      | Data type, optional values:<br/>- `base64`: The video is in Base64 format, must be prefixed with a URL scheme, formatted as `data:[<mediatype>][;base64],<data>`.<br/>- `path`: The path to the local video file. After downloading a file using the `downloadFile` method, the video can be read from the default download directory using a relative path to save it to the album. |
| video | String | Yes      | The content of the video. Supported video formats are MP4, MOV. |

> **Note**：When`type`is`path`he path should be a relative path pointing to the file in the default download directory. For example, if the downloaded video is named`coco.mp4`，the path should be `coco.mp4`。

#### Return Parameters

None

#### Example

```javascript
let payload = {
  "data": {
    "type": "base64",
    "video": "data:video/mp4;base64,AAAAIGZ0eXBpc29tAAACAGlzb21..."
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("saveVideo", payload, (res) => {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "saveVideo",
  //   "messageId": 1703143355158
  // }
});
```



### getAccount

Used to obtain the account address under this CocoAPP, ensuring the uniqueness of each user's identity under the CocoAPP. Different CocoAPPs will have different account addresses, reflecting the independent identity of users in each application.

> Note: If a CocoAPP is uninstalled and then reinstalled after more than 7 days, its account address will be updated to a new one; otherwise, the account address remains unchanged.

#### Level

Level 0

#### Request Parameters

| Field | Type | Required | Description                                                  |
| ----- | ---- | -------- | ------------------------------------------------------------ |
| type  | Int  | false    | Type of request. Returns BTC address by default; if 1 is passed, returns ECC public key (Base64 encoded string). |

#### Return Parameters

| Field  | Type   | Description                             |
| ------ | ------ | --------------------------------------- |
| result | String | Returned account address or public key. |

#### Example

```javascript
messageHandler.sendMessageToParent("getAccount", {}, function (res) {
  // Result returned
  // {
  //   "code": 200,
  //   "cmd": "getAccount",
  //   "result": "1Cg7aGLnLAjXJiYCGZpbqGEz8SLD2v3Qij"
  // }
});

```



### setExtendedData

This interface allows for storing extended data for each CocoAPP, providing up to 1MB of storage space for each CocoAPP. Please Note that each storage operation will replace the previous data.

#### Level

Level 0

#### Request Parameters

| Field  | Type   | Required | Description                         |
| ------ | ------ | -------- | ----------------------------------- |
| extend | Object | true     | Any type of extended data to store. |

#### Return Parameters

None

#### Example

```javascript
// Example code will be here


​```javascript
let payload = {
  "data": {
    "extend": "{\"address\":\"1Cg7aGLnLAjXJiYCGZpbqGEz8SLD2v3Qij\"}"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("setExtendedData", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "setExtendedData",
  //   "messageId": 1703143355158
  // }
});
```



### setExtendedData

This interface allows for storing extended data for each CocoAPP, providing up to 1MB of storage space for each CocoAPP. Please Note that each storage operation will replace the previous data.

#### Level

Level 0

#### Request Parameters

| Field  | Type   | Required | Description                         |
| ------ | ------ | -------- | ----------------------------------- |
| extend | Object | true     | Any type of extended data to store. |

#### Return Parameters

None

#### Example

```javascript
// Example code will be here

#### Return Parameters

| Field  | Type    | Description                                        |
|--------|---------|----------------------------------------------------|
| result | Boolean | Verification result, `true` indicates a valid signature, `false` indicates an invalid signature. |


#### example

​```javascript
let payload = {
  "data": {
    "message": "Hello World!",
    "signatureData": "3045022100aaabbbcccdddee...."
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("verifySignature", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "verifySignature",
  //   "result": true,
  //   "messageId": 1703143355158
  // }    
});
```



### encrypt

This interface provides a text message encryption feature using the Elliptic Curve Integrated Encryption Scheme (ECIES) to encrypt text messages. The encryption operation ensures the security of the message content and is suitable for confidential information transmission. The encryption result is returned as a Base64-encoded string, facilitating storage or further transmission.

#### Level

Level 0

#### Request Parameters

| Field   | Type   | Required | Description                  |
| ------- | ------ | -------- | ---------------------------- |
| message | String | Yes      | The text message to encrypt. |

#### Return Parameters

| Field  | Type   | Description                                         |
| ------ | ------ | --------------------------------------------------- |
| result | String | The encrypted message, returned as a Base64 string. |


#### example

```javascript
let payload = {
  "data": {
    "message": "Hello World!"
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("encrypt", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "encrypt",
  //   "result": "roZskbq2AKNzRitvIh0a....",
  //   "messageId": 1703143355158
  // }    
});
```



### decrypt

This interface is used to decrypt text encrypted using the `encrypt` interface, ensuring the security and reversibility of encrypted data. By providing the encrypted ciphertext (a Base64-encoded string), the interface will restore and return the original plaintext.

#### Level

Level 0

#### Request Parameters

| Field         | Type   | Required | Description                                                |
| ------------- | ------ | -------- | ---------------------------------------------------------- |
| encryptedData | String | Yes      | The text to be decrypted, must be a Base64-encoded string. |

#### Return Parameters

| Field  | Type   | Description                       |
| ------ | ------ | --------------------------------- |
| result | String | The decrypted original plaintext. |


#### example

```javascript
let payload = {
  "data": {
    "encryptedData": "roZskbq2AKNzRitvIh0a...."
  },
  "messageId": new Date().getTime()
};

messageHandler.sendMessageToParent("decrypt", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "decrypt",
  //   "result": "Hello World!",
  //   "messageId": 1703143355158
  // }    
});
```





### registerService

This method is used to register self-service for CocoApp. It should be used in conjunction with the `checkServiceStatus` method to verify the registration status.

#### Level

Level 8

#### Request Parameters

| Field      | Type   | Required | Description                                                  |
| ---------- | ------ | -------- | ------------------------------------------------------------ |
| serviceKey | String | Yes      | The identifier used for registering the self-service.        |
| signKey    | String | Yes      | A BTC signature of `serviceKey` made using the private key of the CocoApp deployment account. |

#### Response Parameters

None


#### example

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
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "registerService",
  //   "messageId": 1703143355158
  // }    
});
```



### checkServiceStatus

This method is used to check the status of a registered self-service.

#### Level

Level 8

#### Request Parameters

None

#### Response Parameters

| Field  | Type    | Description                                                  |
| ------ | ------- | ------------------------------------------------------------ |
| result | Boolean | Indicates the registration status: `true` for success, `false` for failure. |


#### example

```javascript
let payload = {
  "sign": {
        "content": "xxxxxxxx",
        "signature": "xxxxxxx"
  },
  "messageId": new Date().getTime(),
};
messageHandler.sendMessageToParent("checkServiceStatus", payload, function (res) {
  // Return result
  // {
  //   "code": 200,
  //   "cmd": "checkServiceStatus",
  //   "result": true
  // }    
});
```



### sendServiceMessage

This method is used to send self-service messages via P2P communication, facilitating various service calls and data transfers.

#### Level

Level 8

#### Request Parameters

| Field   | Type   | Required | Description                                                  |
| ------- | ------ | -------- | ------------------------------------------------------------ |
| type    | String | Yes      | The protocol type of the request, currently supports only `HTTP`. |
| content | Object | Yes      | The specific request data structure defined by the request type. |

When `type` is `HTTP`, the data structure of `content` is as follows:

| Field   | Type   | Required | Description                                                  |
| ------- | ------ | -------- | ------------------------------------------------------------ |
| method  | String | Yes      | HTTP method type, options include: `POST`, `GET`, `PUT`, `DELETE`. |
| url     | String | Yes      | The URL path for the request.                                |
| headers | Object | No       | The headers for the request, should be a JSON key-value pair format. |
| data    | Object | No       | The data in the request body, in JSON format.                |


#### example

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

#### Response Parameters

| Field  | Type   | Description                                                  |
| ------ | ------ | ------------------------------------------------------------ |
| result | Object | The result of the operation, content may vary based on the actual response. |


#### example

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
  // Return result
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






