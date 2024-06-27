3.1 Function Description

(1)Fork monitoring

  When the number of neighbor nodes is greater than or equal to three, if the number of inconsistencies between the hash of the latest height of the master node and the hash corresponding to the neighbor node exceeds two-thirds, it is considered a fork.

(2)Stuck monitoring

  Real-time monitoring of data synchronization, if it is found that it stays at a high altitude for a long time, it will send an email to notify that there is a stuck.

(3)Export function (not yet open)

  Directly export transaction data inquired by address to Excel. Use POI to export the results of the query. Export byte format data and restore it to relational data.

(4)Warning notice

  Early warning notification is an optional feature. When the monitoring node finds a fork or node block, it will send an email notification. Use Javamail to send mail.

(5) Authentication settings

​     Operation and maintenance tools need to have the settings of user permissions. By default, they are divided into three roles: administrator, operator, and query only. The service side of the operation and maintenance tool needs to be authenticated, and arbitrary queries are prohibited. To apply for a login account, you need to apply for a new user through the administrator. The administrator can modify the permissions of other users, and the operator permissions cannot modify the permissions of other users. Only query permissions can only perform query operations.

(6)Node details

 The node details are actually the monitoring of the TDOS browser. When the operation and maintenance tool is opened, the TDOS browser is not running, and it can be started, stopped, restarted, and accessed.

(7)Data initialization

 The original intention of data initialization is to prevent multiple deployments of the TDOS operating system, the node data is not cleared, and the deployment fails. This function is mainly used to clear node information, close TDOS browser, and close TDOS operation and maintenance tools. The TDOS operating system has been deployed once, and data initialization is required when you want to deploy it again.

   

3.2 Front end operation

(1)Login interface, enter the user name and password to log in to the TDOS operation and maintenance platform

<img src="../img/operational/picture49.png" alt="picture49" style="zoom:55%;" />

(2)The information summary includes monitoring information and warning status. The monitoring information includes the number of blocks produced per day, block height, average block time, average gas unit price, transaction pool size, and current difficulty; the warning status includes cpu status and memory , Block situation, fork situation, operating status, miner detection

<img src="../img/operational/picture50.png" alt="picture50" style="zoom:55%;" />

Number of blocks produced per day: The number of blocks produced by local nodes per day

Block height: the current block height of the local node

Average block time: the average of all block times

Average gas unit price: the average gas unit price of all transactions

Transaction pool size: the number of transactions to be packaged

Current difficulty: current consensus difficulty value

cpu status: cpu status of the current node

Memory: the memory of the current node

Stuck situation: If the local node stays at the same height for a long time, it will display Yes, otherwise it will display No. If the user does not bind the node, it will display to be confirmed.

Forking: Check whether the local node is forking. If it is forked, it will display Yes, if it is not forked, it will display No. If the user does not bind the node, it will display to be confirmed.

Running status: If the local node does not start successfully, the connection timeout is displayed, and the node starts successfully, then it displays running

Miner detection: check whether the local node is a miner node, if it is a miner node, it will show yes, if it is a full node it will show no

(3) Console, manage and bind nodes

<img src="../img/operational/picture51.png" alt="picture51" style="zoom:55%;" />

The console can bind, unbind and delete nodes;

(4) Early warning information, management of receiving and sending mail information

<img src="../img/operational/picture52.png" alt="picture52" style="zoom:55%;" />

(5) Authentication settings, management user settings permissions

<img src="../img/operational/picture53.png" alt="picture53" style="zoom:55%;" />

(6) Node details

<img src="../img/operational/picture54.png" alt="picture54" style="zoom:55%;" />

Click visit, to visit the binding node

(7) Data initialization

<img src="../img/operational/picture55.png" alt="picture55" style="zoom:55%;" />

Click the initialization button, the node information and browser information will be cleared. After success, the operation and maintenance platform will exit and the wizard tool needs to be redeployed.