### 以太坊·*Rinkeby* 测试网络



https://www.rinkeby.io/#stats

https://rinkeby.etherscan.io/

https://rinkeby.etherscan.io/gastracker

[随机密码生成](https://1password.com/zh-cn/password-generator/)

[水龙头](https://faucet.rinkeby.io/)

推特发文申请ETH

```tiki wiki
Requesting faucet funds into 0xfF6DFb4951CB38C82E7E4e547739645Fba4a0F4A on the #Rinkeby #Ethereum test network.
```

https://infura.io/ liyong liyong@fshd.com Fshd@)!(2019)

etherscan API leeyongit leeyongit@gmail.com Fshd@)!(2019) zvBF6#*iRw

测试账户

| 地址                                       | 说明     | 密码                          |
| ------------------------------------------ | -------- | ----------------------------- |
| 0x4c1c376701F6aD6a221003Cf6B0a521f73B383Fa | xpo账户  | 12345678                      |
| 0x36CD0e4D711e95340f0C68b0106aaa384b59c4cc | test账户 | 12345678                      |
| 0xfF6DFb4951CB38C82E7E4e547739645Fba4a0F4A | eth账户  | F2h0s1d9_e%#etui53fP5KP+jx,Yh |

geth查询余额：eth.getBalance("0x4c1c376701F6aD6a221003Cf6B0a521f73B383Fa")

**Mac Ethereum Wallet 命令行启动**

```shell
/Users/liyong/Library/Application Support/Ethereum Wallet/binaries/Geth/unpacked/geth --rinkeby --cache 1024 --ipcpath /Users/liyong/Library/Ethereum/geth.ipc --syncmode fast
```

**Centos运行 Geth Rinkeby 网络**

```shell
geth --rinkeby --cache 1024 --syncmode "light" --rpc --rpcapi "eth,net,web3,personal" --rpcaddr "127.0.0.1" --rpcport "8545" --rpccorsdomain "*" console 2>>geth.log
```



"/Applications/Ethereum Wallet.app/Contents/MacOS/Ethereum Wallet" --rpc https://rinkeby.infura.io/v3/fd6fff8e76704b5785909139ec5930aa

#### 问题

1. 安全组对所有ip开放了RPC端口8545
2. Geth 设置了 —rpcaddr 0.0.0.0 RPC服务对所有ip开放了
3. keystore 密钥放在了服务器上、弱密码12345678

#### 防范

1. 安全组IP白名单，开放RPC端口给特定IP，更换8545端口
2. geth的rpc采用加密访问方式
3. keystore 不要放在服务器上，放在内网机器上，复杂密码



工作备忘：

[4G主板定时开关机功能，PC后台进行配置](http://j.azls.mobi/qy/node/ajax_set_state?node_id=1000055&boot=19:30&halt=19:28&state=ON)

https://golang.org/pkg/crypto/cipher

**API**

鉴权:   machine 表， 条件字段 mac  是否绑定 user_id 有值代表已绑定

心跳：machine_heartbeat 表， 插入记录 mac、update_time

升级：返回固定地址

"/Applications/Ethereum Wallet.app/Contents/MacOS/Ethereum Wallet" --datadir /Users/liyong/Library/Ethereum/rinkeby --rpc  'https://rinkeby.infura.io/v3/fd6fff8e76704b5785909139ec5930aa'
