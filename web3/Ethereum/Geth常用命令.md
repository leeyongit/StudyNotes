## Geth常用的命令

1. **查看账户**
```
eth.accounts
```
这个命令列出当前节点下所有的账户。

2. **创建新账户**
```
personal.newAccount("your_password")
```
这个命令创建一个新的账户,并设置密码。

3. **解锁账户**
```
personal.unlockAccount(eth.accounts[0], "your_password")
```
这个命令解锁指定的账户,以便进行交易等操作。

4. **查看账户余额**
```
eth.getBalance(eth.accounts[0])
```
这个命令查看指定账户的以太币余额。

5. **转账**
```
eth.sendTransaction({from:eth.accounts[0], to:"0x1234567890123456789012345678901234567890", value: web3.toWei(1, "ether")})
```
这个命令从第一个账户向指定地址转账 1 以太币。

6. **部署智能合约**
```javascript
var source = "contract SimpleStorage { uint storedData; function set(uint x) { storedData = x; } function get() constant returns (uint) { return storedData; }}";
var compiled = eth.compile.solidity(source);
var contract = eth.contract(compiled.info.abiDefinition);
var simple = contract.new({from: eth.accounts[0], data: compiled.bytecode, gas: 300000});
```
这段代码首先编译 Solidity 源代码,然后部署一个名为 `SimpleStorage` 的智能合约。

7. **启动/停止挖矿**
```
miner.start()
miner.stop()
```
这两个命令用于启动和停止节点的挖矿过程。

8. **查看区块信息**
```
eth.getBlock("latest")
```
这个命令显示最新挖掘的区块的信息。

9. **查看交易信息**
```
eth.getTransaction("0x1234567890abcdef1234567890abcdef12345678")
```
这个命令显示指定交易哈希的交易信息。

这些只是 Geth 提供的一部分常用命令,实际上 Geth 还有更多功能和命令可供探索。您可以根据需要查阅 Geth 的文档,了解更多命令的用法。