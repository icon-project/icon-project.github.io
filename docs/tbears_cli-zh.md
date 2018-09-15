# T-Bears CLI

T-Bears提供命令行界面以与ICON网络互动。它实现了所有JSON-RPC v3 API。

有关详细的使用指南，请参阅[T-Bears教程](https://github.com/icon-project/t-bears/blob/master/README.md)
要与ICON网络进行互动，您无需“启动”T-Bears服务。
你可以使用`-u`选项指向远程ICON网络，或更新tbears_cli_config.json文件中的“uri”值。
下面列出的CLI命令可以在T-Bears安装后使用。

虽然每个客户端SDK都拥有相同的功能，但T-Bears的一个独特命令是`deploy`。
如果你想部署自己的SCORE，你必须从T-Bears内执行。

```console
$ tbears [-h] [-d] command ...
```

| 命令 | 描述 |
|-------|-------|
| keystore | 创建密钥文件。使用secp256k1库生成私钥和公钥对。 |
| deploy | 部署SCORE. |
| scoreapi | 查询SCORE公开的API列表。 |
| call | 使用用户输入json文件请求icx_call。 |
| sendtx | 使用用户输入json文件请求icx_sendTransaction。 |
| txresult | 通过交易散列获取交易结果。 |
| txbyhash | 通过交易散列获取交易信息。 |
| transfer | 转移ICX币。 |
| balance | 获取给定地址的余额。 |
| totalsupply | 查询ICX的总供应量。 |
| lastblock | 获取最新区块信息。 |
| blockbyheight | 使用给定的块高度获取区块信息。 |
| blockbyhash | 使用给定的区块散列获取区块信息。 |

