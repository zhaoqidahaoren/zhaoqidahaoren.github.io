## 简介
&#160;&#160;&#160;&#160;&#160;&#160;TDOS（Trusted Data Operation System）又称可信数据操作系统，是由上海财经大学金融科技研究院和常州市勇阳信息技术有限公司联合研发的全球首款操作系统级区块链服务，包含一整套区块链底层系统、智能合约应用程序的开发与运维技术框架及其衍生服务。 
    
&#160;&#160;&#160;&#160;&#160;&#160;TDOS以快捷部署为核心特点，旨在让一般技术人员和用户能够快速上手开发、自由配置、轻松使用区块链业务系统，打通区块链与一般技术人员、大众间的技术壁垒、认知壁垒，实现区块链应用的加速落地。 

## 产品路线图

&#160;&#160;&#160;&#160;&#160;&#160;TDOS产品的版本迭代计划如下：

&#160;&#160;&#160;&#160;&#160;&#160;1）版本号：v2.0

&#160;&#160;&#160;&#160;&#160;&#160;时间：2020年12月

&#160;&#160;&#160;&#160;&#160;&#160;TDOS初步集成版：TDOS基础技术栈及生态工具，一键部署安装区块链底层系统。

&#160;&#160;&#160;&#160;&#160;&#160;2）版本号：v3.0

&#160;&#160;&#160;&#160;&#160;&#160;预计时间：2021年6月

&#160;&#160;&#160;&#160;&#160;&#160;TDOS隐私计算版本：TDOS增强对隐私保护的功能，比如同态隐藏、多方计算、数据重铸等。

&#160;&#160;&#160;&#160;&#160;&#160;3）版本号：v4.0

&#160;&#160;&#160;&#160;&#160;&#160;预计时间：2021年12月

&#160;&#160;&#160;&#160;&#160;&#160;TDOS网中网版本：支持基于TDOS部署的不同的网络跨链及网络合并。

&#160;&#160;&#160;&#160;&#160;&#160;4）版本号：v5.0

&#160;&#160;&#160;&#160;&#160;&#160;预计时间：2022年6月

&#160;&#160;&#160;&#160;&#160;&#160;TDOS全生态工具版本：彻底与操作系统完全集成，软硬件集合，实现可信计算环境。

&#160;&#160;&#160;&#160;&#160;&#160;5）版本号：v6.0

&#160;&#160;&#160;&#160;&#160;&#160;预计时间：2022年12月

&#160;&#160;&#160;&#160;&#160;&#160;TDOS行业集成版：根据不同的业务环境，比如金融、溯源、游戏等，提供定制版本。

## TDOS安装说明

### 1.1 部署环境要求

（1）硬件标准

 &nbsp;&nbsp; CPU i5以上；

 &nbsp;&nbsp; 内存：8G运行内存以上；

 &nbsp;&nbsp; 硬盘容量：100G以上；

 (2) 软件标准

  &nbsp;&nbsp; 初始系统环境Mac OS 10.15 以上 / Windows 10以上；

  &nbsp;&nbsp; 本系统以在虚拟机环境下安装为例，也可以在实体机中安装；

  &nbsp;&nbsp; 虚拟机VMWare具体配置要求见附件1；

  &nbsp;&nbsp;（如果没有安装虚拟机可前往官网下载：https://www.vmware.com ）

###  TDOS操作系统安装

（1）在虚拟机中安装[TDOS.iso](https://tdos-store.oss-cn-beijing.aliyuncs.com/TDOS.iso)镜像；

（2）具体安装步骤：

 &nbsp;&nbsp;第一步：打开虚拟机

![图片2](../img/zh-cn/install/picture1.png)

 &nbsp;&nbsp;第二步：选择Boot system installer，运行TDOS虚拟机镜像系统

![图片2](../img/zh-cn/install/picture2.png)

 &nbsp;&nbsp;第三步：进入默认用户账号（yongyang），输入密码123456（默认），解锁系统后点击Sign In 重新设置账号；

![图片3](../img/zh-cn/install/picture3.png)

 &nbsp;&nbsp;第四步：依次填充你的名字、新的用户登录名、用户密码（牢记，后续操作会使用）、Root账户密码(可忽略)以及主机名称（数字英文随意，无字数要求）。完成后，点击进入下一步。

![图片4](../img/zh-cn/install/picture4.png)

 &nbsp;&nbsp;第五步：删除原有分区![图片5](../img/zh-cn/install/picture5.png)

 &nbsp;&nbsp;第六步：选择第二个分区，点击箭头（确认）

![图片6](../img/zh-cn/install/picture6.png)

 &nbsp;&nbsp;第七步：勾选方框内容，然后红箭头处选 / 。完成后，点击Next；

![图片7](../img/zh-cn/install/picture7.png)

 &nbsp;&nbsp;第八步：点击start，等待完成

![图片8](../img/zh-cn/install/picture8.png)

![图片9](../img/zh-cn/install/picture9.png)

 &nbsp;&nbsp;第九步：点击reboot；

![图片10](../img/zh-cn/install/picture10.png)

 &nbsp;&nbsp;第十步：如果长时间未进入此界面，点击回车；

![图片11](../img/zh-cn/install/picture11.png)

 &nbsp;&nbsp;第十一步：输入新的账号密码并重新登录

![图片12](../img/zh-cn/install/picture12.png)

 &nbsp;&nbsp;第十二步：TDOS镜像系统安装完成后，显示此界面。

![图片13](../img/zh-cn/install/picture13.png)

###  TDOS部署向导

（1）准备工作：安装向导工具前，如有需要，可先安装Vmware-tools（详见附件2）

（2）向导工具安装

&nbsp;&nbsp;第一步：点红色箭头指示处打开应用界面；

![图片14](../img/zh-cn/install/picture14.png)

&nbsp;&nbsp;第二步：点击红框内应用图标，开始安装；

![图片15](../img/zh-cn/install/picture15.png)

&nbsp;&nbsp;第三步：打开后如图所示，点击红框内进入安装；

![图片16](../img/zh-cn/install/picture16.png)

&nbsp;&nbsp;第四步：此为TDOS软件安装许可协议，阅读同意后，点击红框内进行下一步；

![图片17](../img/zh-cn/install/picture17.png)

&nbsp;&nbsp;第五步：同意后，显示安装包含组件然后点击“继续”按钮；

![图片18](../img/zh-cn/install/picture18.png)

&nbsp;&nbsp;第六步：输入在虚拟机镜像中设置的管理员密码（登入密码），并点击“继续”进入下一步；

![图片19](../img/zh-cn/install/picture19.png)

&nbsp;&nbsp;第七步：复制TDOS序列号粘贴即可，如果没有TDOS序列号，请联系TDOS团队申请序列号。点击“继续”按钮；

![图片20](../img/zh-cn/install/picture20.png)

&nbsp;&nbsp;第八步：分别点击下载节点程序、运维工具两个文件；

![图片21](../img/zh-cn/install/picture21.png)

&nbsp;&nbsp;第九步：安装完成后，点击“继续”按钮；

![图片22](../img/zh-cn/install/picture22.png)

&nbsp;&nbsp;第十步：默认选择“连接到已有网络”，也可以选择创建新的网络，新的网络有模板模式和专家模式两种。选择任意一种网络，点击“继续”，进行下一步；

&nbsp;&nbsp;<b>1、选择连接已有网络的前提条件是已有网络部署成功，无需选择共识机制、密码学组件、出块速度等，只需输入待连接的网络地址，点击继续进行部署。</b>

![picture23](../img/zh-cn/install/picture23.png)

&nbsp;&nbsp;<b>2、选择模板模式，点击继续。</b>

![picture82](../img/zh-cn/install/picture82.png)

&nbsp;&nbsp;选择共识机制，可选择poa、pos和pow任意一种，点击继续进行下一步。

![picture83](../img/zh-cn/install/picture83.png)

&nbsp;&nbsp;非对称加密算法可选择SM2、ED25519，哈希算法可选择SM3、KECCAK-256 、 SHA3-256，它们之间可以任意组合，选择好密码学组件之后点击继续。

![picture84](../img/zh-cn/install/picture84.png)

&nbsp;&nbsp;出块速度不得小于5秒，直接填数字，不需要填时间单位，默认时间单位为s；

&nbsp;&nbsp;网络ID不能以数字开头，也不可以出现特殊字符；

&nbsp;&nbsp;节点类型可以选择全节点或共识节点、如果种子节点不开启节点发现，邻居节点可以连接网络，但是不会同步种子节点区块；

&nbsp;&nbsp;点击继续进行下一步部署。

![picture85](../img/zh-cn/install/picture85.png)

&nbsp;&nbsp;<b>3、选择专家模式，点击继续。</b>

![picture86](../img/zh-cn/install/picture86.png)

&nbsp;&nbsp;共识机制可选择poa、pos和pow三种；

&nbsp;&nbsp;出块速度不得小于5秒，直接填数字，不需要填时间单位，默认时间单位为s；

&nbsp;&nbsp;手续费=gas单价* gas，发送事务会收取手续费，gas单价设置为0，发送事务不会收取手续费；

&nbsp;&nbsp;种子节点主要用于TDOS网络首次启动时的连接节点，如果是单矿工节点可以不用输入。种子节点的格式为：网络ID://IP:端口号，例：helloworld://192.168.1.3:7000；

&nbsp;&nbsp;选择开启挖矿则节点为共识节点，选择不开启挖矿为全节点；

&nbsp;&nbsp;非对称加密算法可选择SM2、ED25519，哈希算法可选择SM3、KECCAK-256 、 SHA3-256，它们之间可以任意组合；

&nbsp;&nbsp;选择好密码学组件之后点击继续。

![picture87](../img/zh-cn/install/picture87.png)

&nbsp;&nbsp;选择出空块，无论有没有发送事务，系统都会自动出块，选择不出空块，只有在发送事务成功后才会出块；

&nbsp;&nbsp;开启节点发现，邻居节点可以同步数据，不开启节点发现，邻居节点无法同步数据；

&nbsp;&nbsp;只有选择pow共识的时候才有难度值参数，且该参数必须大于等于1，不能为0；

&nbsp;&nbsp;网络ID不能以数字开头，也不可以为特殊字符；

&nbsp;&nbsp;点击继续进行下一步。

![picture88](../img/zh-cn/install/picture88.png)

&nbsp;&nbsp;预分配地址和金额会被写入创世区块，节点启动后这些账户会拥有相应的金额；
 
&nbsp;&nbsp;对于PoA和PoS共识，需要预设矿工地址，这些地址会成为最早的共识参与者；
 
&nbsp;&nbsp;预分配地址和矿工地址是在[JS-SDK](https://github.com/TrustedDataFramework/js-sdk)中通过选择不同的密码学算法生成的，系统最多支持添加20个预分配地址和矿工地址。
 
&nbsp;&nbsp;<span style="color:red;background:背景颜色;font-size:文字大小;font-family:字体;">需要特别注意的是：当前的节点地址是由所选择的非对称加密算法和哈希算法的不同组合决定的</span>
 
&nbsp;&nbsp;点击继续。

![picture89](../img/zh-cn/install/picture89.png)

&nbsp;&nbsp;配置好的参数都展示在这里可以进行确认，点击继续。

![picture90](../img/zh-cn/install/picture90.png)

&nbsp;&nbsp;第十一步：点击“部署”按钮，进入下一步；

![图片24](../img/zh-cn/install/picture24.png)

&nbsp;&nbsp;第十二步：等待进度条走完全程即可；

![图片25](../img/zh-cn/install/picture25.png)

&nbsp;&nbsp;第十三步：点击完成结束安装。

![图片27](../img/zh-cn/install/picture26.png)

### 安装完成状态说明

（1）安装完成界面如下：

![图片27](../img/zh-cn/install/picture26.png)

（2）功能说明

&nbsp;&nbsp;账号密码：系统统一分配，用于登录TDOS运维平台；

&nbsp;&nbsp;TDOS运维工具：TDOS运维工具能够帮助用户同步监测链的具体情况，在分叉恢复、卡块监测、导入导出、预警通知以及鉴权设置等方面进行运维调整。

&nbsp;&nbsp;DAPP首页：用户可以通过DAPP更直观的了解TDOS应用场景

&nbsp;&nbsp;智能合约开发环境： 用户可在上面进行智能合约的开发。

### 附件

#### 附件1:

Windows版虚拟机配置

在网上下载VMware虚拟机文件进行安装。

第一步：创建新的虚拟机

![picture57](../img/zh-cn/install/picture57.png)

第二步：默认信息进行下一步；

![picture58](../img/zh-cn/install/picture58.png)

第三步：选择本地镜像文件TDOS.iso，下载链接：https://tdos-store.oss-cn-beijing.aliyuncs.com/TDOS.iso

![picture59](../img/zh-cn/install/picture59.png)

第四步：选择客户操作系统 linux和版本Ubuntu

![picture61](../img/zh-cn/install/picture61.png)

第五步：虚拟机命名并选择安装位置

![picture62](../img/zh-cn/install/picture62.png)

第六步：指定磁盘容量大小，设置>=50g

![图片63](../img/zh-cn/install/picture63.png)

第七步：自定义虚拟机硬件，建议内存配置不低于8G，处理器数量不少于4个，网络适配器选择桥接模式，勾选复制物理网络连接状态。

![picture65](../img/zh-cn/install/picture65.png)                                   

![picture66](../img/zh-cn/install/picture66.png)

![picture67](../img/zh-cn/install/picture67.png)

第八步：配置完成

## 如何使用本文档

&#160;&#160;&#160;&#160;&#160;&#160;通过本文档可以获取TDOS的架构详情，以及用户使用TDOS的基本方法。首先，要想成为网络中的节点，需要安装并运行一个TDOS客户端。用户可根据安装步骤自行安装需要的组件。在使用上，TDOS提供从部署可信数据链到智能合约开发的全套工具，提供浏览器以供实时展示关键指标，提供运维工具以维护节点，提供应用广场来展现TDOS的多领域应用场景。用户可根据对应工具的详细说明和使用文档中的步骤详解来进行具体操作。