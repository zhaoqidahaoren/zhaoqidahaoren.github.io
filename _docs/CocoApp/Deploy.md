---
title: Deploy and Upgrade
order: 2
---

# CocoApp开发指南

CocoApp支持HTML5、Vue、和React等前端技术栈，让你可以使用熟悉的方法与CocoCat进行交互。为了加速开发过程，我们提供Vue 3模板，帮助你快速地搭建、开发、和维护CocoApp。

## 快速开始

这一节会带你快速通过CocoApp开发的准备、创建项目、创建CocoApp身份、本地开发测试、以及打包的基本步骤。

### 准备工作

开始前，请确保你的开发环境准备妥当：

- **Git**：用于克隆官方模板仓库。
- **Node.js**（版本 ≥ 16）：提供JavaScript运行时和项目依赖管理。
- **NPM**或**Yarn**：管理项目依赖。
- **CocoAPI**：了解CocoCat的API接口，详见《CocoApp API接口说明文档》。

### 创建项目

1. **克隆模板**：从CocoApp官方模板仓库开始，克隆项目到本地：

   ```bash
   git clone https://github.com/CocoCatWeb3-Official/cocoapp-template-interface.git
   cd cocoapp-template-interface
   ```

2. **安装依赖**：在项目目录内，运行以下命令安装必要的依赖：

   ```bash
   npm install # 或 yarn install
   ```

### 创建CocoApp身份

+ **启动身份创建过程**：每个CocoApp都需要一个独特的身份地址，这个地址不仅标识了CocoApp，还用于CocoApp的签名和后续的更新下载过程：

  ```bash
  npm run create <CocoApp类型代码> # 或使用 yarn run create <CocoApp类型代码>
  ```

  当命令正确执行并完成后，控制台将输出：`CocoApp created successfully`，表示CocoApp身份已成功创建。

### 开发与测试

- **启动本地服务器**：使用下面的命令启动本地开发服务器，并在CocoCat调试工具中访问你的CocoApp：

  ```bash
  npm run start # 或 yarn run start
  ```

  端口默认为8080，可配置，访问 [http://localhost:8080](http://localhost:8080/) 来预览你的CocoApp。确保你的网络设置允许CocoCat调试工具访问此地址。

- **实时预览**：在开发过程中，任何代码更改都会即时反映在CocoAPP调试工具的预览中。

### 打包

- **构建生产版本**：完成开发后，使用以下命令打包你的CocoApp，为发布做好准备：

  ```bash
  npm run build # 或 yarn run build
  ```

  打包后的文件将被放置在`dist/`目录中，接下来就是发布CocoAPP，详见详细发布指南。

## 项目概述

### 项目结构说明

本项目采用Vue-cli工具构建，其目录结构与标准的Vue-cli项目相同，仅额外包含了一个`cocoapp_setup`文件夹，专用于CocoApp的发布流程。

### 开发重点

#### CocoCat通信

项目中的`message_utils.ts`文件充当了CocoApp与CocoCat之间的通讯桥梁，实现双方的交互。这个工具位于`src/utils`目录下。详细的API使用方法，请参见《CocoApp API接口说明文档》。

#### 应用图标设置

- 每个CocoApp都应具备一个唯一的图标，推荐的尺寸为192x192像素，存放于`public/`目录。
- 在进行项目打包时，`icon.png`应被正确放置于`dist/`目录根部以保证应用图标的正常显示。

#### 应用加载方法

- CocoApp需要实现一个`onload`方法作为CocoAPI的一部分，以确保应用按预期与CocoCat交互，具体实现细节请参阅《CocoAPP API接口说明文档》。

### 打包指南

要打包您的应用，请执行以下命令：

```bash
npm run build # 或 yarn run build
```

打包完成后，`dist/`目录将包含以下文件：

- **index.html**：作为应用的主入口文件，这是必需的。
- **icon.png**：代表CocoApp的图标，必须存在以避免显示异常。

#### 注意事项

- 确保打包后的每个文件（包括资源文件）大小必须小于1M，以免影响应用的发布和热更新能力。
- 打包后的每个文件命名只能包含字母（a-z, A-Z）、数字（0-9），务必遵循这一命名约定。
- 在项目中引用本地资源时，请使用相对路径，因为使用绝对路径可能会导致资源无法正确加载。

## 发布指南

完成CocoAPP的打包之后，接下来需要进行创建身份、签名，并完成最终的发布操作。

#### 创建身份步骤

1. **执行创建身份**：

   创建CocoAPP身份需要指定一个类型代码，来定义CocoAPP的类别：

   ```bash
   npm run create <CocoAPP类型代码> # 或使用 yarn run create <CocoAPP类型代码>
   ```

   其中`<CocoAPP类型代码>`是根据CocoAPP的功能选择的对应值。例如，对于图书类应用，使用`0001`：

   ```bash
   npm run create 0001
   ```

   参数`0001`代表CocoAPP的类型。根据您的应用选择相应的类型代码，详细类型列表如下：

   | 类型          | 对应值 |
      | ------------- | ------ |
   | Books         | 0001   |
   | Busniess      | 0002   |
   | DevTools      | 0003   |
   | Education     | 0004   |
   | Entertainment | 0005   |
   | Finance       | 0006   |
   | Food          | 0007   |
   | Design        | 0008   |
   | Health        | 0009   |
   | Kids          | 0010   |
   | Lifestyle     | 0011   |
   | Medical       | 0012   |
   | Music         | 0013   |
   | Newspaper     | 0014   |
   | Video         | 0015   |
   | Shopping      | 0016   |
   | Sports        | 0017   |
   | Social        | 0018   |
   | Travel        | 0019   |
   | Utilities     | 0020   |
   | Weather       | 0021   |
   | Other         | 0022   |

   执行成功后，`cocoapp_setup`文件夹目录如下：

   ```css
   cocoapp_setup/
     ├─ account.json
     └─ data/
         └─ <address>/
              ├─ icon.png
              ├─ ......
   					 ├─ index.html
              └─ content.json
   ```

2. **`account.json`配置及`data`文件夹说明**：

+ **`account.json`**：签名账户信息，内含`privateKey`（BTC的WIF格式私钥）和`address`（`privateKey`生成的BTC地址）。如选择手动创建并配置此文件，再次执行`init`时，会在`data`目录下为相应`address`创建文件夹，同时生成`content.json`。初始化过程中还会校验`privateKey`和`address`的正确性。

+ **`data`**：存放CocoAPP内容的主要文件夹，下级目录以`address`命名，目录下包括从`dist`目录迁移的所有文件，并新增了`content.json`文件。

**重要提示**：`privateKey`是极为敏感的信息，务必妥善保管，以避免丢失导致失去对CocoAPP的控制。

#### 签名步骤

完成身份创建之后，执行以下命令进行签名：

```bash
npm run sign # 或使用 yarn run sign
```

成功执行后，控制台将显示：`CocoApp successfully signed`，表示签名过程完成。此时，`cocoapp_setup/data/<address>`目录下将生成一个新的`publishfiles.json`文件，包含了所有必要的信息，准备好进行发布。

**重要提示**：

- 目前，数字签名功能仅在Windows环境下提供支持。我们计划在后续版本中扩展对其他操作系统的支持。
- 请确保`<address>`所指目录下所有文件的总体积不超过15MB限制。此措施旨在保障CocoApp的顺畅发布及其热更新功能的有效性。超出此限制可能会影响应用的发布流程和用户体验。

#### 发布与更新准备

完成签名操作后，目录结构如下所示，包含所有必需的发布文件：

```css
cocoapp_setup/
  ├─ account.json
  └─ data/
      └─ <address>/
           ├─ publishfiles.json
           ├─ icon.png
           ├─ ......
           └─ index.html
```

将`<address>`目录下的文件打包成`.zip`格式，文件名为`<address>.zip`，然后准备发布。

#### 发布和更新操作

**发布**：通过CocoAPP设置中的开发者模式联系官方客服，并提交`.zip`包完成发布。

**更新**：更新的流程与发布一致。

以上步骤概述了从初始化到发布CocoAPP的完整流程，确保每一步骤都能顺利执行，以保障CocoAPP的成功发布。

## 致谢

感谢所有贡献者的支持与合作，让CocoApp不断进步！





+ 

  
