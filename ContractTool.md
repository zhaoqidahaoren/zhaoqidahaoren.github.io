# Introduction

 The TDOS smart contract development tool aims to guide people with certain development capabilities to develop smart contracts.

# Instructions for use



1.After TDOS installation is complete, as shown below:

<img src="../img/contract/picture48.png" alt="picture48" style="zoom:75%;" />

2.Click to view the smart contract development environment, jump to the contract-template directory, right-click the mouse, open the terminal, install git, type in the command line: sudo apt install git, press Enter;

Enter the TDOS system login password and press Enter;

Encountered Do you want to continue? Enter y and press Enter.

![picture59](../img/contract/picture59.png)

3.To download nvm, type in the command line: `Git clone https://github.com/creationix/nvm.git`, press Enter, the download time is slightly longer, please be patient;

To install nvm, enter bash ./nvm/install.sh on the command line;

To enable nvm, enter source ~/.bashrc on the command line.

![picture57](../img/contract/picture57.png)

4.Install node.js, enter sudo apt-get install -y nodejs on the command line;

Enter node -v on the command line to view the node version, the node version must be greater than and contain 12;

Install dependencies, enter npm install on the command line;

After the dependencies are installed, you can deploy or call the written smart contract.

![picture58](../img/contract/picture58.png)



5.The following is an example of a smart contract. First, you need to write a smart contract. Edit the code by commanding vim coin.ts. The code of the coin smart contract is as follows:

```
/**
 * erc 20 example in assembly script
 */

export { __change_t, __malloc, __peek } from './node_modules/@salaku/js-sdk/lib' // Blockchain application program interface
import { Util, U256, Globals, ABI_DATA_TYPE } from './node_modules/@salaku/js-sdk/lib'
import { Store } from './node_modules/@salaku/js-sdk/lib'
import { Context, Address } from './node_modules/@salaku/js-sdk/lib'

const _balance = Store.from<Address, U256>('balance');
const _freeze = Store.from<Address, U256>('freeze');

export function init(tokenName: string, symbol: string, totalSupply: U256, decimals: u64, owner: Address): void {
    // tokenName || symbol || totalSupply || decimals || owner
    Globals.set<string>('tokenName', tokenName);
    Globals.set<string>('symbol', symbol);
    Globals.set<U256>('totalSupply', totalSupply);
    Globals.set<u64>('decimals', decimals);
    Globals.set<Address>('owner', owner);
    _balance.set(owner, totalSupply);
}

// display balance
export function balanceOf(addr: Address): U256 {
    return _balance.getOrDefault(addr, U256.ZERO);
}


/* Send coins */
export function transfer(to: Address, amount: U256): void {
    const msg = Context.msg();
    assert(amount > U256.ZERO, 'amount is not positive');
    let b = balanceOf(msg.sender)
    assert(b >= amount, 'balance is not enough');
    _balance.set(to, balanceOf(to) + amount);
    _balance.set(msg.sender, balanceOf(msg.sender) - amount);
}

```

6.The file coinDeploy.js code for deploying and calling the contract is as follows:

```
const tool = require('@salaku/js-sdk')
const fs = require('fs')

// Read configuration
const sk = 'b01bb4ceb384ceee3b1eaf2ed78deba989ed92b25c75f28de58fa8bba191d7bc'
const pk = tool.privateKey2PublicKey(sk)
const addr = tool.publicKey2Address(pk)
console.log(addr)

// const contract = new tool.Contract('', require('./assembly/index.abi.json'))
//
// Transaction construction tool
// const builder = new tool.TransactionBuilder(tool.constants.POA_VERSION, sk, 0, 200000)

// rpc tool
// const rpc = new tool.RPC('192.168.1.52', 7010)
const rpc = new tool.RPC('localhost', 7010)
const ascPath = 'node_modules/.bin/asc';

const contract = new tool.Contract()

// Transaction construction tool
// const builder = new tool.TransactionBuilder(tool.constants.POA_VERSION, sk, 0, 200000)

// get nonce by addr
async function getNonceByAddr(addr){
    return rpc.getNonce(addr)
}

async function deployCoin(){
    // contract.binary = await tool.compileContract(ascPath, 'coin.ts', { debug: true })
    // contract.abi = require('./coin.abi.json')
    const buf = await tool.compileContract(ascPath, 'coin.ts')
    const abi = tool.compileABI(fs.readFileSync('coin.ts'))
    fs.writeFileSync('coin.abi.json', JSON.stringify(abi))
    const c = new tool.Contract('', abi, buf)
    const builder = new tool.TransactionBuilder(tool.constants.POA_VERSION, sk, 0, 0, (await getNonceByAddr(addr)) + 1)
    const nonce = await rpc.getNonce(addr) + 1
    console.log(nonce)
    builder.nonce = nonce
    // deploy contract
    let tx = builder.buildDeploy(c, {
        tokenName: 'doge',
        symbol: 'DOGE',
        totalSupply: '90000000000000000',
        decimals: 8,
        owner: addr
    }, 0)
    c.address = tool.getContractAddress(addr, await rpc.getNonce(addr) + 1)
    console.log(c.address)
    return (await rpc.sendAndObserve(tx, tool.TX_STATUS.INCLUDED))
}

async function transfer(){
    const abi = require('./coin.abi.json') // abi file
    const c = new tool.Contract("5fca9a8e9f593597d61061f2a470f3e52d143eb5", abi)
    builder.nonce = await rpc.getNonce(addr) + 1
    const tx = builder.buildContractCall(c, 'transfer', ['fb59589b3b3e63d345ef48c809b15db067e41748', 10000000000], 0)
    console.log(JSON.stringify(tx))
    tx.sign(sk)
    console.log(JSON.stringify(tx))
    return await rpc.sendAndObserve(tx, tool.TX_STATUS.INCLUDED)
}

async function balanceOf(){
    const abi = require('./coin.abi.json') // abi file
    const c = new tool.Contract("5fca9a8e9f593597d61061f2a470f3e52d143eb5", abi)
    // builder.nonce = await rpc.getNonce(addr) + 1
    const tx = builder.buildContractCall(c, 'balanceOf', ['fb59589b3b3e63d345ef48c809b15db067e41748'], 0)
    tx.nonce = (await rpc.getNonce(addr)) + 1
    console.log(JSON.stringify(tx))
    tx.sign(sk)
    // console.log(JSON.stringify(tx))
    return await rpc.sendAndObserve(tx, tool.TX_STATUS.INCLUDED)
}

console.log(deployCoin().catch(e => console.log(e)).then(x => console.log(x)))
// console.log(transfer().then(x => console.log(x)))
// console.log(balanceOf().then(x => console.log(x)))


```

7.After deploying the contract successfully, two addresses will be returned. The owner is the owner address and the other is the contract address. The response results are as follows：

![picture60](../img/contract/picture60.png)

8.When calling the contract, you need to fill in the address of the contract returned by the deployment contract to the constant c, comment out the deployment transaction, and open the transfer transaction.

![picture61](../img/contract/picture61.png)

9.The call contract and response results are as follows:

![picture62](../img/contract/picture62.png)