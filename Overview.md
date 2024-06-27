## Introduction
TDOS (Trusted Data Operation System) is the world’s first operating system-level blockchain service jointly developed by the Financial Technology Research Institute of Shanghai University of Finance and Economics and Changzhou Yongyang Information Technology Co., Ltd., including a complete set of The technical framework for the development and operation and maintenance of the underlying system of the blockchain, smart contract applications and its derivative services.

 TDOS takes fast deployment as its core feature, and aims to enable general technical personnel and users to quickly develop, freely configure, and easily use the blockchain business system, and break through the technical and cognitive barriers between the blockchain and general technical personnel and the public. , To realize the accelerated landing of blockchain applications.


## Product roadmap

The version iteration plan of TDOS products is as follows:

1）v2.0

 release time：2020/12

TDOS preliminary integrated version: TDOS basic technology stack and ecological tools, one-click deployment and installation of the underlying blockchain system.

2）v3.0

ETA：2021/6

TDOS privacy computing version: TDOS enhances privacy protection functions, such as homomorphic hiding, multi-party computing, data recasting, etc.

3）v4.0

ETA：2021/12

TDOS network version: support different network cross-chain and network merger based on TDOS deployment.

4）v5.0

ETA：2022/6

TDOS full ecological tool version: completely integrated with the operating system, software and hardware integration, to achieve a trusted computing environment.

5）v6.0

ETA：2022/12

TDOS industry integration version: According to different business environments, such as finance, traceability, games, etc., provide customized versions.

## Installation Guide

### Deployment environment requirements 

（1）Hardware requirements

 &nbsp;&nbsp; CPU i5 or +

 &nbsp;&nbsp; RAM：8G or +

 &nbsp;&nbsp; HD capacity：100G or +

 (2) Software requirements

  &nbsp;&nbsp; Initial Operation System: Mac OS 10.15 or +/ Window 10 or +

  &nbsp;&nbsp; This system takes installation in a virtual machine environment as an example, and it can also be installed in a physical machine; 

  &nbsp;&nbsp; For the specific configuration requirements of VMWare, see Annex 1; 

  &nbsp;&nbsp; If you don’t have a virtual machine installed, you can go to the official website to download: https://www.vmware.com) 

###  The installation of TDOS

(1) Install [TDOS.iso](https://tdos-store.oss-cn-beijing.aliyuncs.com/TDOS.iso) image in the virtual machine; 

(2) Steps of installation:

 &nbsp;&nbsp; step 1: open the virtual machine 

![picture1](img/install/picture1.png)

 &nbsp;&nbsp;Step 2: Select Boot system installer to run TDOS virtual machine mirroring system 

![picture2](img/install/picture2.png)

 &nbsp;&nbsp;Step 3: Enter the default user account (yongyang), enter the password 123456 (default), unlock the system and click Sign In to reset the account; 

![picture3](img/install/picture3.png)

 &nbsp;&nbsp;Step 4: Fill in your name, new user login name, user password (make sure you remember it, it will be used in subsequent operations), Root account password (can be ignored), and host name (numbers in English are optional, no word requirements). When finished, click to go to the next step. 
![picture4](img/install/picture4.png)

 &nbsp;&nbsp;Step 5: Delete the original partition! 
[Picture5](img/install/picture5.png) 

 &nbsp;&nbsp;Step 6: Select the second partition and click the arrow (confirm) 

![Picure6](img/install/picture6.png)

 &nbsp;&nbsp;Step 7: Check the box content, and then select / at the red arrow. When finished, click Next; 
![Picure7](img/install/picture7.png)

 &nbsp;&nbsp;Step 8: Click start and wait for completion
![Pic 8](img/install/picture8.png)

![Picture9](img/install/picture9.png)

 &nbsp;&nbsp;Step 9: Click reboot; 

![Picture10](img/install/picture10.png)

 &nbsp;&nbsp;Step 10: If you haven't entered this interface for a long time, click Enter; 
![Picture11](img/install/picture11.png)

 &nbsp;&nbsp;Step 11: Enter the new account password and log in again 
![Picture12](img/install/picture12.png)

 &nbsp;&nbsp;Step 12: After the TDOS mirroring system is installed, this interface will be displayed. 
![Picture13](img/install/picture13.png)

###  TDOS Deployment Guide

(1) Preparation: Before installing the wizard tool, if necessary, install Vmware-tools (see attachment 2 for details) 

(2) Wizard tool installation 

&nbsp;&nbsp;Step 1: Click the red arrow to open the application interface; 

![Picture14](img/install/picture14.png)

&nbsp;&nbsp;Step 2: Click the application icon in the red box to start the installation; 
![Picture15](img/install/picture15.png)

&nbsp;&nbsp;Step 3: After opening, as shown in the figure, click inside the red box to enter the installation; 
![Picture16](img/install/picture16.png)

&nbsp;&nbsp;Step 4: This is the TDOS software installation license agreement. After reading and agreeing, click in the red box to proceed to the next step; 
![Picture17](img/install/picture17.png)

&nbsp;&nbsp;Step 5: After agreeing, display the installed components and click the "Continue" button; 
![Picture18](img/install/picture18.png)

&nbsp;&nbsp;Step 6: Enter the administrator password (login password) set in the virtual machine image, and click "Continue" to enter the next step; 
![Picture19](img/install/picture19.png)

&nbsp;&nbsp;Step 7: Copy the TDOS serial number and paste it. If there is no TDOS serial number, please contact the TDOS team to apply for the serial number. Click the "Continue" button; 
![Picture20](img/install/picture20.png)

&nbsp;&nbsp;Step 8: Click to download the two files of node program and operation and maintenance tool respectively; 
![Picture21](img/install/picture21.png)

&nbsp;&nbsp;Step 9: After the installation is complete, click the "Continue" button; 
![Picture22](img/install/picture22.png)

&nbsp;&nbsp;Step 10: By default, "Connect to an existing network" is selected, or you can choose to create a new network. The new network has two modes: template mode and expert mode. Choose any network, click "Continue" to proceed to the next step; 

&nbsp;&nbsp;<b>1. The prerequisite for choosing to connect to an existing network is that the existing network is successfully deployed. There is no need to select consensus mechanism, cryptographic components, block generation speed, etc., just enter the network address to be connected and click to continue deployment. </b>
![picture23](img/install/picture23.png)

&nbsp;&nbsp;<b>2. Select the template mode and click Continue. </b>
![picture82](img/install/picture82.png)

&nbsp;&nbsp;Select the consensus mechanism, you can choose any of poa, pos and pow, and click to proceed to the next step. 
![picture83](img/install/picture83.png)

&nbsp;&nbsp;Asymmetric encryption algorithm can choose SM2, ED25519, hash algorithm can choose SM3, KECCAK-256, SHA3-256, and they can be combined arbitrarily. After selecting the cryptographic components, click Continue. 
![picture84](img/install/picture84.png)

&nbsp;&nbsp;The block generation speed should not be less than 5 seconds. Fill in the number directly without the time unit. The default time unit is s; 

&nbsp;&nbsp;The network ID cannot start with a number, nor can special characters appear; 

&nbsp;&nbsp;The node type can be full node or consensus node. If the seed node does not enable node discovery, neighbor nodes can connect to the network, but the seed node block will not be synchronized; 

&nbsp;&nbsp;Click Continue to proceed to the next deployment. 
![picture85](img/install/picture85.png)

&nbsp;&nbsp;<b>3. Select expert mode and click Continue. </b> 
![picture86](img/install/picture86.png)

&nbsp;&nbsp;Consensus mechanism can choose poa, pos and pow three; 

&nbsp;&nbsp;The block generation speed should not be less than 5 seconds. Fill in the number directly without the time unit. The default time unit is "s"; 

&nbsp;&nbsp;Handling fee = gas unit price * gas, a handling fee will be charged for sending a transaction, the gas unit price is set to 0, and no handling fee will be charged for sending a transaction; 

&nbsp;&nbsp;The seed node is mainly used to connect the node when the TDOS network is first started. If it is a single miner node, no input is required. The format of the seed node is: network ID://IP:port number, for example: helloworld://192.168.1.3:7000; 

&nbsp;&nbsp;If you choose to turn on mining, the node is a consensus node, and if you choose not to turn on mining, it is a full node; 

&nbsp;&nbsp;Asymmetric encryption algorithm can choose SM2, ED25519, hash algorithm can choose SM3, KECCAK-256, SHA3-256, they can be any combination; 

&nbsp;&nbsp;After selecting the cryptographic components, click Continue. 

![picture87](img/install/picture87.png)

&nbsp;&nbsp;If you select an empty block, the system will automatically generate a block regardless of whether there is a transaction sent or not. If no empty block is selected, the block will be generated only after the transaction is successfully sent; 

&nbsp;&nbsp;If turn on "node discovery", neighbor nodes can synchronize data, if you do not turn on "node discovery", neighbor nodes cannot synchronize data; 

&nbsp;&nbsp;The difficulty value parameter is only available when the pow consensus is selected, and this parameter must be greater than or equal to 1, and cannot be 0; 

&nbsp;&nbsp;The network ID cannot start with a number, nor can it be a special character; 

&nbsp;&nbsp;Click Continue to the next step. 

![picture88](img/install/picture88.png)

&nbsp;&nbsp;The pre-allocated address and amount will be written into the genesis block, and these accounts will have the corresponding amount after the node is started; 

&nbsp;&nbsp;For PoA and PoS consensus, miner addresses need to be preset, and these addresses will become the earliest consensus participants; 

&nbsp;&nbsp;The pre-allocated addresses and miner addresses are generated by selecting different cryptographic algorithms in [JS-SDK](https://github.com/TrustedDataFramework/js-sdk). The system supports adding up to 20 pre-allocated addresses and miners address. 

&nbsp;&nbsp;<span style="color:red;background:background color;font-size:text size;font-family:font;">It is important to note that the current node address is determined by the selected asymmetric encryption algorithm and Determined by different combinations of Greek algorithm</span> 

&nbsp;&nbsp;Click to continue. 

![picture89](img/install/picture89.png)

&nbsp;&nbsp;The configured parameters are displayed here for confirmation, click to continue. 

![picture90](img/install/picture90.png)

&nbsp;&nbsp;Step 11: Click the "Deploy" button to enter the next step; 

![Picture24](img/install/picture24.png)

&nbsp;&nbsp;Step 12: Wait for the progress bar to complete the process; 

![Picture25](img/install/picture25.png)

&nbsp;&nbsp;Step 13: Click Finish to end the installation. 

![Picture27](img/install/picture26.png)

### Installation completion status description

(1) The installation completion interface is as follows: 

![Picture27](img/install/picture26.png)

(2) Function description 

&nbsp;&nbsp;Account password: uniformly assigned by the system, used to log in to the TDOS operation and maintenance platform; 

&nbsp;&nbsp;TDOS operation and maintenance tools: TDOS operation and maintenance tools can help users synchronize the specific conditions of the monitoring chain, and make operation and maintenance adjustments in terms of fork recovery, jam monitoring, import and export, early warning notifications, and authentication settings. 

&nbsp;&nbsp;DAPP Homepage: Users can understand TDOS application scenarios more intuitively through DAPP 

&nbsp;&nbsp;Smart contract development environment: Users can develop smart contracts on it. 

### annex

#### annex 1:

 virtual machine configuration in Windows

Download the VMware virtual machine file from the Internet for installation.

Step 1: Create a new virtual machine

![图片57](img/install/picture57.png)

Step 2: Default information and proceed to the next step;

![图片58](img/install/picture58.png)

Step 3: Select the local mirror file TDOS.iso, download link: https://tdos-store.oss-cn-beijing.aliyuncs.com/TDOS.iso


![图片59](img/install/picture59.png)

Step 4: Choose the guest operating system linux and version Ubuntu

![图片61](img/install/picture61.png)

Step 5: Name the virtual machine and select the installation location

![图片62](img/install/picture62.png)


Step 6: Specify the disk capacity, set >=50g

![图片63](img/install/picture63.png)

Step 7: Customize the virtual machine hardware. It is recommended that the memory configuration is not less than 8G, the number of processors is not less than 4, the network adapter selects the bridge mode, and the copy physical network connection status is checked.

![图片64](img/install/picture64.png)

![图片65](img/install/picture65.png)

![图片66](img/install/picture66.png)

![图片67](img/install/picture67.png)

Step 8: Configuration is complete

## How to use this document

Through this document, you can get the details of the TDOS architecture and the basic methods for users to use TDOS. First of all, if you want to be a node in the network, you need to install and run a TDOS client. Users can install the required components according to the installation steps. In use, TDOS provides a complete set of tools from the deployment of trusted data links to smart contract development, provides a browser for real-time display of key indicators, provides operation and maintenance tools to maintain nodes, and provides an application square to display TDOS's multi-domain application scenarios. Users can perform specific operations according to the detailed instructions of the corresponding tools and the detailed steps in the use documents.