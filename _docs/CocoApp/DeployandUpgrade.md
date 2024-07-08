---
title: Deployed and Upgrade
category: CocoApp
order: 3
permalink: /DeployedUpgrade/
ulOrder: 1
clickable: true
---

# CocoApp Development Guide

CocoApp supports front-end technology stacks such as HTML5, Vue, and React, allowing you to interact with CocoCat using familiar methods. To accelerate the development process, we provide a Vue3 template to help you quickly build, develop, and maintain CocoApp.

## Quick Start

This section will guide you through the basic steps of preparing for CocoApp development, creating a project, creating a CocoApp identity, local development testing, and packaging.

### Preparation

Before starting, make sure your development environment is ready：

- **Git**：Used to clone the official template repository.
- **Node.js** (version ≥ 16)：Provides JavaScript runtime and project dependency management.
- **NPM** or **Yarn**：Manages project dependencies.
- **CocoAPI**：Familiarize yourself with CocoCat's API interfaces, see "CocoApp API Interface Documentation" for details.

### Create Project

1. **Clone Template**：Start by cloning the project from the official CocoApp template repository to your local machine：

   ```bash
   git clone https://github.com/CocoCatWeb3-Official/cocoapp-template-interface.git
   cd cocoapp-template-interface
   ···
   ```

2. **Install Dependencies**：Run the following command in the project directory to install the necessary dependencies：

   ```bash
   npm install # or yarn install
   ```

### Creating a CocoApp Identity

+ **Initiate the Identity Creation Process**: Each CocoApp requires a unique identity address. This address not only identifies the CocoApp but is also used for the CocoApp's signature and subsequent update download process:

  ```bash
  npm run create <CocoAppTypeCode> # or use yarn run create <CocoAppTypeCode>
  ```

  When the command is executed correctly and completed, the console will output: `CocoApp created successfully`, indicating that the CocoApp identity has been successfully created.

### Development and Testing

- **Start the Local Server**: Use the following command to start the local development server and access your CocoApp in the CocoCat debugging tool:

  ```bash
  npm run start # or yarn run start
  ```

  The default port is 8080, which can be configured. Visit [http://localhost:8080] to preview your CocoApp. Ensure your network settings allow the CocoCat debugging tool to access this address.

- **Live Preview**: During the development process, any code changes will be instantly reflected in the preview within the CocoAPP debugging tool.

### Packaging

- **Build for Production**: After development is complete, use the following command to package your CocoApp for release:

  ```bash
  npm run build # or yarn run build
  ```

  The packaged files will be placed in the `dist/` directory. Next, follow the detailed release guide to publish your CocoApp.

## Project Overview

### Project Structure

This project is built using the Vue-cli tool, with a structure similar to standard Vue-cli projects. It includes an additional `cocoapp_setup` folder specifically for the CocoApp release process.

### Development Focus

#### CocoCat Communication

The `message_utils.ts` file in the project serves as a communication bridge between the CocoApp and CocoCat, facilitating interaction between the two. This utility is located in the `src/utils` directory. For detailed API usage, refer to the "CocoApp API Documentation."

#### Application Icon Settings

- Each CocoApp should have a unique icon, recommended to be 192x192 pixels, and stored in the `public/` directory.
- When packaging the project, the `icon.png` should be correctly placed in the root of the `dist/` directory to ensure proper display of the application icon.

#### Application Load Method

CocoApp needs to implement an `onload` method as part of the CocoAPI to ensure the application interacts with CocoCat as expected. For specific implementation details, please refer to the "CocoApp API Documentation."

### Packaging Guide

To package your application, execute the following command:

```bash
npm run build # or yarn run build
```

After packaging is complete, the `dist/` directory will contain the following files:

- **index.html**: The main entry file of the application, which is mandatory.
- **icon.png**: The icon representing the CocoApp, which must be present to avoid display issues.

#### Notes

- Ensure that each packaged file (including resource files) is less than 1MB to avoid impacting the  application's release and hot update capabilities.
- Each packaged file name can only contain letters (a-z, A-Z) and numbers (0-9). Adhering to this - naming convention is crucial.
- When referencing local resources in the project, use relative paths, as using absolute paths may lead to resources not loading correctly.


#### Identity Creation Steps

1. **Execute Identity Creation**:

   Creating a CocoAPP identity requires specifying a type code to define the category of the CocoAPP:

   ```bash
   npm run create <CocoAPPTypeCode> # or use yarn run create <CocoAPPTypeCode>
   ```

   Where <CocoAPPTypeCode> is the corresponding value chosen based on the functionality of the CocoAPP. For example, for a book-related application, use `0001`:

   ```bash
   npm run create 0001
   ```

   The parameter `0001` represents the type of the CocoAPP. Choose the appropriate type code for your application from the detailed type list below:

   | Type          | Code   |
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

   After the successful execution, the `cocoapp_setup` folder will have the following structure:

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

2. **onfiguration of `account.json` and Description of the `data` Folder**:

+ **`account.json`**: This file contains the signing account information, including the `privateKey` (BTC's WIF format private key) and the `address` (BTC address generated from the `privateKey`). If you choose to manually create and configure this file, running `init` again will create a folder in the `data` directory for the corresponding `address` and generate a `content.json` file. During initialization, the correctness of the `privateKey` and `address` will also be verified.

+ **`data`**: This is the main folder where CocoAPP content is stored. Subdirectories are named after the `address`, and these directories include all files migrated from the `dist` directory, as well as the newly created `content.json` file.

**Important Note**: The `privateKey` is highly sensitive information. Be sure to store it securely to avoid losing control over the CocoAPP.

#### Signing Steps

After completing identity creation, execute the following command to sign:

```bash
npm run sign # or use yarn run sign
```

After successful execution, the console will display: `CocoApp successfully signed`, indicating the signing process is complete. At this point, a new `publishfiles.json` file will be generated in the `cocoapp_setup/data/<address>` directory, containing all the necessary information and ready for publishing.

**Important Note**:

- Currently, the digital signing feature is only supported in the Windows environment. We plan to extend support to other operating systems in future versions.
- Please ensure the total size of all files in the `<address>` directory does not exceed the 15MB limit. This measure is to ensure the smooth release of CocoApp and the effectiveness of its hot update feature. Exceeding this limit may affect the application publishing process and user experience.

#### Preparation for Publishing and Updating

After completing the signing operation, the directory structure is as follows, containing all the necessary publishing files:

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

Package the files in the `<address>` directory into a `.zip` file, named `<address>.zip`, and then prepare for publishing.

#### Publishing and Updating Operations

**Publishing**: Contact official customer service through Developer Mode in CocoAPP settings and submit the `.zip` package to complete the publishing.

**Updating**: The update process is the same as the publishing process.

The above steps outline the complete process from initialization to publishing CocoAPP, ensuring each step is executed smoothly to guarantee the successful release of CocoAPP.

## Acknowledgements

Thanks to all contributors for their support and cooperation, enabling CocoApp to continuously improve!
