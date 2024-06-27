## TDOS Browser Overview

TDOS browser contains four major modules, namely homepage, block, transaction, and contract. The homepage of the browser lists out some basic data of TDOS. You can jump to the details page of the corresponding transaction by querying different conditions.

## TDOS Browser User Manual
(1)Homepage

<img src="../img/browser/picture56.png" alt="图片56" style="zoom:75%;" />

In the homepage search box, you can query based on transaction hash, block hash, and address, and you can jump to block details, transaction details, and transaction list details corresponding to the query address, respectively.

The homepage shows the basic data of TDOS, including the number of blocks produced per day, total number of transactions, average block time, current difficulty value, transaction pool size and consensus mechanism.

Display the latest 10 blocks and 10 transactions, click on the view all blocks and view all transactions buttons to jump to the block list page and transaction list page respectively.

（2）Block

<img src="../img/browser/picture57.png" alt="图片57" style="zoom:75%;" />

In the search box of the block module, you can query according to the transaction hash, block hash, and address, and you can jump to the block details, transaction details, and transaction list details corresponding to the query address respectively.

The block list display supports paging, height descending sorting, and the displayed data is block height, block generation time, number of transactions, and miner address.

Click the block height to enter the block details interface as follows:

<img src="../img/browser/picture58.png" alt="图片58" style="zoom:75%;" />

Click the transaction quantity to enter the transaction details interface as follows:

<img src="../img/browser/picture59.png" alt="图片59" style="zoom:75%;" />

Display transaction hash, transaction type, block height, block time, from and to address, amount, handling fee

（3） Transaction

<img src="../img/browser/picture60.png" alt="图片60" style="zoom:75%;" />

The transaction list display can be paged, and the block height is sorted in descending order. The transaction displays the transaction hash, transaction type, block height, block generation time, sender and receiver addresses, amount and handling fee.

Click the transaction hash to display the transaction details, as shown in the following interface:

<img src="../img/browser/picture61.png" alt="图片61" style="zoom:75%;" />

Click the sender address or receiver address, the current nonce value, current balance and transaction list under this address will be displayed.

<img src="../img/browser/picture62.png" alt="图片62" style="zoom:75%;" />

（4） Contract

<img src="../img/browser/picture63.png" alt="图片63" style="zoom:73%;" />

Display a list of deployed transaction contracts, which can be paged and sorted by block height in descending order. Display parameters include transaction hash, contract address, block height, block generation time, from, amount, and handling fee.

Click the transaction hash to enter the contract details interface, as shown in the following figure:

<img src="../img/browser/picture64.png" alt="picture64" style="zoom:75%;" />

The detailed data of the transaction is displayed above. Display data: contract address, balance, transaction hash, block height, block time, from, to.

The following shows the list of transactions calling the contract + contract details
Invoking contract transactions, the block height is sorted in descending order, paging is supported, and the display parameters include transaction hash, block time, from, to, amount, and handling fee. The transaction hash, from and to can jump to the contract details of the corresponding page. The reference diagram is as follows:



<img src="../img/browser/picture65.png" alt="picture65" style="zoom:75%;" />

The parameters are: smart contract source code, contract ABI and payload. If the contract is not uploaded and verified, it will be empty.

Add a button, Verify Contract, which is used to upload custom contract code.

After clicking, the verification page will be displayed. The page needs to enter two parameters, the first is the address of the contract, and the second is the source code of the contract. Refer to the following figure

<img src="../img/browser/picture66.png" alt="picture66" style="zoom:75%;" />

Click the verify contract button to display the following page, which is used to upload the custom contract code. This page needs to enter two parameters. The first is the address of the contract, and the second is the source code of the contract. Refer to the following figure

Enter the contract address, the system will automatically generate the contract ABI and constructor bytecode, then enter the contract source code, click verify and publish, as shown in the following figure.

<img src="../img/browser/picture67.png" alt="picture67" style="zoom:75%;" />