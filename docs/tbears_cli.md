# T-Bears CLI

T-Bears provides the command line interface to interact with the ICON network. It implements all the JSON-RPC v3 APIs. 

For detailed usage guideline, please see [T-Bears tutorial](https://github.com/icon-project/t-bears/blob/master/README.md). 
To interact with the ICON network, you don’t need to `start` T-Bears service. 
Instead, point to the remote ICON network using `-u` option, or update "uri" value in the tbears_cli_config.json file.
Below listed CLI commands are ready to use after T-Bears installation.

```bash
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

## Getting familiar with T-Bears
We will install T-Bears using docker, and send some queries to the testnet.  

- Install docker. [[Get started with Docker](https://www.docker.com/get-started)]
- Install and run T-Bears with docker.
Below command will download the image and run the container.
On execution, configuration files and the test account keyfile are generated.
Note that the test account refers to the one reside on the T-Bears simulated environment,
you can use the `keystore_test1` file on the local T-Bears environment only,
and should not use the file to sign your transaction to other networks.  

```bash
$ docker run -it --name tbears-container -p 9000:9000 iconloop/tbears
Unable to find image 'iconloop/tbears:latest' locally
latest: Pulling from iconloop/tbears
124c757242f8: Pull complete 
2ebc019eb4e2: Pull complete 
dac0825f7ffb: Pull complete 
82b0bb65d1bf: Pull complete 
ef3b655c7f88: Pull complete 
abf2199d9d18: Pull complete 
dd59e2bf156a: Pull complete 
02a056d8159d: Pull complete 
Digest: sha256:cbec58fc5ae717934b510794d54bab6095de03eac013cc89a34ab1a8899e1813
Status: Downloaded newer image for iconloop/tbears:latest
 * Starting RabbitMQ Messaging Server rabbitmq-server                    [ OK ] 
Made tbears_cli_config.json, tbears_server_config.json, keystore_test1 successfully
Started tbears service successfully
root@b65c6a4cccf8:/tbears#
```

We will interact with the testnet.
[[Testnet Info](https://icon-project.github.io/docs/icon_network.html#testnet-for-dapps)].
You can pass the node API endpoint to the -u option to make T-Bears talk to the right one.
For the sake of simplicity, we will invoke query methods which do not reqiure keystore file.
- Sample query 1 : Get the genesis block of the testnet. You can verify the network id in the response message.
Make sure block height should be in hexadecimal. 

```bash
root@b65c6a4cccf8:/tbears# tbears blockbyheight -u https://bicon.net.solidwallet.io/api/v3 0x0
block info : {
    "jsonrpc": "2.0",
    "result": {
        "version": "0.1a",
        "prev_block_hash": "",
        "merkle_tree_root_hash": "831da0a095133e4a0dfd7b6527e8d1851a474dfface9748ec2fe2c6464d345ed",
        "time_stamp": 0,
        "confirmed_transaction_list": [
            {
                "message": "{X}iconNet",
                "accounts": [
                    {
                        "balance": "0x2961fff8ca4a62327800000",
                        "name": "god",
                        "address": "hx5a05b58a25a1e5ea0f1d5715e1f655dffc1fb30a"
                    },
                    {
                        "balance": "0x0",
                        "name": "treasury",
                        "address": "hx1000000000000000000000000000000000000000"
                    },
                    {
                        "balance": "0x2961fff8ca4a62327800000",
                        "name": "test_1",
                        "address": "hx6e1dd0d4432620778b54b2bbc21ac3df961adf89"
                    }
                ],
                "nid": "0x3"
            }
        ],
        "block_hash": "974e2a8fbefad82b30af0ebfd9939314a53cd7ce3c54b19079b59501122987fe",
        "height": 0,
        "peer_id": "",
        "signature": ""
    },
    "id": 1
}

```

- Sample query 2 : Query the built-in [Governance SCORE](https://github.com/icon-project/governance/blob/master/README.md) APIs.

```bash
root@b65c6a4cccf8:/tbears# tbears scoreapi -u https://bicon.net.solidwallet.io/api/v3 cx0000000000000000000000000000000000000001
SCORE API: [
    {
        "type": "function",
        "name": "acceptScore",
        "inputs": [
            {
                "name": "txHash",
                "type": "bytes"
            }
        ],
        "outputs": []
    },
    {
        "type": "function",
        "name": "addAuditor",
        "inputs": [
            {
                "name": "address",
                "type": "Address"
            }
        ],
        "outputs": []
    },
    ...
```

- Sample query 3 : Let's check the current step cost for each operation.

```bash
root@b65c6a4cccf8:/tbears# cat stepcost.json 
{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "icx_call",
    "params": {
        "to": "cx0000000000000000000000000000000000000001",
        "dataType": "call",
        "data": {
            "method": "getStepCosts"
        }
    }
}
root@b65c6a4cccf8:/tbears# tbears call -u https://bicon.net.solidwallet.io/api/v3 stepcost.json
response : {
    "jsonrpc": "2.0",
    "result": {
        "default": "0x186a0",
        "contractCall": "0x61a8",
        "contractCreate": "0x3b9aca00",
        "contractUpdate": "0x5f5e1000",
        "contractDestruct": "-0x11170",
        "contractSet": "0x7530",
        "get": "0x0",
        "set": "0x140",
        "replace": "0x50",
        "delete": "-0xf0",
        "input": "0xc8",
        "eventLog": "0x64",
        "apiCall": "0x0"
    },
    "id": 1
}
```
