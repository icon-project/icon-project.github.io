# T-Bears CLI

T-Bears provides the command line interface to interact with the ICON network. It implements all the JSON-RPC v3 APIs. 

For detailed usage guideline, please see [T-Bears tutorial](https://github.com/icon-project/t-bears/blob/master/README.md). 
To interact with the ICON network, you don’t need to `start` T-Bears service. 
Below listed CLI commands are ready to use after T-Bears installation.

While every client SDKs expose same functions, one distinctive command of T-Bears is `deploy`. 
If you want to deploy your own SCORE, you always do it from T-Bears.

```console
$ tbears [-h] [-d] command ...
```

| command | description |
|-------|-------|
| keystore | Create a keystore file. Generate a private and public key pair using secp256k1 library. |
| deploy | Deploy the SCORE. |
| scoreapi | Query the list of APIs that the given SCORE exposes. |
| call | Request icx_call with user input json file. |
| sendtx | Request icx_sendTransaction with user input json file. |
| txresult | Get transaction result by transaction hash. |
| txbyhash | Get transaction info by transaction hash. |
| transfer | Transfer ICX coins. |
| balance | Get the balance of given address in loop. |
| totalsupply | Query the total supply of ICX in loop. |
| lastblock | Get last block info |
| blockbyheight | Get block info using given block height. |
| blockbyhash | Get block info using given block hash. |

