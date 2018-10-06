Quickstart Guide

Intended audience are the developers who are new to ICON, but are familair with programming languages and have basic SW development experience. The purpose of this guide is to help you setup the SCORE developement environment, and deploy your first SCORE quickly without touching any in-depth knowledge.

This tutorial is in three parts. In part 1, you will get familar with the developement tools, and the local testing environment. You will write a simple SCORE, and excute it on your PC. In part 2, you will learn how to deploy the SCORE onto the testnet, and write a python client code to interact with it. Part 3 willl explain how to set up your own private devnet on AWS.

#### Part 1. [HelloWorld on local emulated environment](#helloworld-on-local-emulated-environment)

- Intall T-Bears (Docker)
- Test account
- Check your account balance
- Create HelloWorld contract and deploy it
- Modify HelloWorld contract to greet you

#### Part 2. [HelloWorld on testnet](#helloworld-on-testnet)

- Connect to the testnet (in Chrome wallet)
- Create an account (in Chrome wallet)
- Get test ICX
- Transaction fees
- Deploy HelloWorld contact to the testnet (T-Bears)
- Execute HelloWorld contract (T-Bears, Python)

#### Part 3. Private devnet on AWS

- TBD



## Part 1. HelloWorld on local emulated environment

### Intall T-Bears (Docker)

- Install Docker [[Get Started with Docker](https://www.docker.com/get-started)]

- Install T-Bears and run the container.

Below command will download tbears docker image, create a container, start the container, and attach your stdin/stderr to the container. 

```bash
$ docker run -it --name local-tbears -p 9000:9000 iconloop/tbears
 * Starting RabbitMQ Messaging Server rabbitmq-server                    [ OK ]
Made tbears_cli_config.json, tbears_server_config.json, keystore_test1 successfully
Started tbears service successfully
root@c5b81f9874ee:/tbears#
```

Exit and stop the container.

```bash
root@07dfee84208e:/tbears# exit
```

Show the container list.

```bash
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS                    NAMES
c5b81f9874ee        iconloop/tbears     "entry.sh"          6 minutes ago       Up 6 minutes               0.0.0.0:9000->9000/tcp   local-tbears
```

Start and attach to the container, or attach to a running container.

```bash
$ docker container start -a local-tbears
root@07dfee84208e:/tbears#

$ docker container attach local-tbears
root@07dfee84208e:/tbears#
```



### Test account

Test keystore file, `keystore_test1`, is provided for your convenience. Be careful not to use this keystore file  to sign your transaction to any network other than T-Bears, because the password is open to public. 

- Address : hxe7af5fcfd8dfc67530a01a0e403882687528dfcb
- Password : test1_Account
- ICX balance : 0x2961fff8ca4a62327800000



### Check your account balance

Let's issue a `tbears balance` command to check your balance. 

```bash
root@07dfee84208e:/tbears# tbears balance hxe7af5fcfd8dfc67530a01a0e403882687528dfcb
balance in hex: 0x2961fff8ca4a62327800000
balance in decimal: 800460000000000000000000000
```



### Create HelloWorld contract and deploy it

Now, we will create a SCORE. `tbears init` command will initialize a project. 

```bash
root@07dfee84208e:/tbears# tbears init hello_world HelloWorld
Initialized tbears successfully

root@07dfee84208e:/tbears# ls hello_world
hello_world.py  __init__.py  package.json

```

`hello_world.py` has the SCORE implementation template which is fully functional.

```python
from iconservice import *

class HelloWorld(IconScoreBase):

    def __init__(self, db: IconScoreDatabase) -> None:
        super().__init__(db)

    def on_install(self) -> None:
        super().on_install()

    def on_update(self) -> None:
        super().on_update()

    @external(readonly=True)
    def hello(self) -> str:
        return "Hello"
```

Let's deploy the contract and get the contract address. For every transaction, you need to check the execution result using txhash. Transaction result contains the SCORE address if the deploy was successful. 

```bash
root@07dfee84208e:/tbears# tbears deploy hello_world
Send deploy request successfully.
If you want to check SCORE deployed successfully, execute txresult command
transaction hash: 0xc40cbbf2b89cd1e2890132145e6d86ad61835edaca0bcc3a4c34b5cb22b8be28

root@07dfee84208e:/tbears# tbears txresult 0xc40cbbf2b89cd1e2890132145e6d86ad61835edaca0bcc3a4c34b5cb22b8be28
Transaction result: {
    "jsonrpc": "2.0",
    "result": {
        "txHash": "0xc40cbbf2b89cd1e2890132145e6d86ad61835edaca0bcc3a4c34b5cb22b8be28",
        "blockHeight": "0x3158",
        "blockHash": "0x9e0c1385128bf0d425773f9f9130d683d327a058a9c8dc0a6c4df71bb98195e1",
        "txIndex": "0x0",
        "to": "cx3176b5d6cae66a1abbc3ca9070423a5c708834a9",
        "scoreAddress": "cx3176b5d6cae66a1abbc3ca9070423a5c708834a9", <-- SCORE address
        "stepUsed": "0x4d361d0",
        "stepPrice": "0x0",
        "cumulativeStepUsed": "0x4d361d0",
        "eventLogs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "status": "0x1"
    },
    "id": 1
}
```

SCORE has been successfully deployed. We will invoke `hello` method from CLI and see the result. `call.json` file contains the request message. `to` is the SCORE address, `from` is the message sender's address. Don't forget to provide the actual SCORE address, the one you got after deploy. For the complete json message format, please refer to [ICON JSON-RPC API v3 specification](https://github.com/icon-project/icon-rpc-server/blob/master/docs/icon-json-rpc-v3.md).

```bash
root@07dfee84208e:/tbears# tbears call call.json
response : {
    "jsonrpc": "2.0",
    "result": "Hello",
    "id": 1
}
root@07dfee84208e:/tbears# cat call.json 
{
    "jsonrpc": "2.0",
    "method": "icx_call",
    "id": 1,
    "params": {
        "from": "hxe7af5fcfd8dfc67530a01a0e403882687528dfcb",
        "to": "cx3176b5d6cae66a1abbc3ca9070423a5c708834a9",
        "dataType": "call", 
        "data": {
            "method": "hello" 
        }
    }
}
```



### Modify HelloWorld contract to greet you

It will be a pleasure to have more agreeable HelloWorld SCORE. Let's give them a name, and greet you nicer way. One method will be added, `hello` will be modified.

- `name` is a read-only funtion, and returns the SCORE name.

- `hello` method is slightly modified to recognize you. `msg` is a built-in property that holds information of the message sender, and the amount of ICX the sender attempted to transfer. They can be referenced by `msg.sender` and `msg.value` respectively. 

```python
    @external(readonly=True)
    def name(self) -> str:
        return "HelloWorld"
    
    @external(readonly=True)
    def hello(self) -> str:
        return f'Hello, {self.msg.sender}. My name is {self.name()}'
```

We need to deploy the SCORE again to reflect the change. `deploy` command with  `-m update` and `-o [score_address]` options will update the SCORE. Updating is a unique feature of ICON. Previous SCORE address remains the same.

```bash
root@07dfee84208e:/tbears# tbears deploy -m update -o cx3176b5d6cae66a1abbc3ca9070423a5c708834a9 hello_world
Send deploy request successfully.
If you want to check SCORE deployed successfully, execute txresult command
transaction hash: 0xc412dc9c6685701c8837eddea091283244303d322aa1fba36bc0782e1b483763
...
```

We will invoke the `hello` method with the same request message, and see the changes. 

```bash
root@07dfee84208e:/tbears# tbears call call.json
response : {
    "jsonrpc": "2.0",
    "result": "Hello, hxe7af5fcfd8dfc67530a01a0e403882687528dfcb. My name is HelloWorld",
    "id": 1
}
```



## Part 2. Hello World on testnet

In this part 2, we will deploy the HelloWorld SCORE onto the testnet. You will learn how to configure your tools to interact with the testnet.

### Connect to the testnet (in Chrome wallet)

If you have not installed ICON Chrome wallet, here is the chrome web store [link](https://chrome.google.com/webstore/detail/iconex/flpiciilemghbmfalicajoolhkkenfel).

ICON Chrome wallet is connected to the mainnet. You can switch to other network by changing its configuration. 

- Open the Chrome DevTools by pressing F12, then go to the **Application** tab. In the **Storage** section, expand **Local Storage**.
- Add a new key/value pair, **isDev/true**, by clicking on the empty row at the bottom of the table.
- Reload your wallet, then you will see the menu in the bottom. Click the **ICX (SERVER)** button to open the dropup list of the available networks. Select **YEOUIDO** network, this is a testnet for DApp developers. 

Please refer to the [network guide](icon_network.md) for more information.



### Create an account (in Chrome wallet)

Let's create an account in ICONex, and download the keystore file. 

We will use this keystore file on testnet. The keystore file downloaded from ICONex will look something like `UTC--2018-10-06T06_00_02.195Z--hxbac99ffea54749ca1c86ab4e6bfe0b630bf7a7a0`. As you may have noticed, the latter part of the filename is your address, `hxbac99ffea54749ca1c86ab4e6bfe0b630bf7a7a0` in this example. Let's rename the file to `keyfile_test2` for human readibility. 

Full guideline of creating a keyfile is [here](wallet.md#create-an-account).



### Get test ICX

You need ICX to deploy and invoke a SCORE on testnet, because every transaction on testnet requires a  fee. To receive testnet ICX, please send email to `testicx@icon.foundation` with following information.

- Testnet node url
- Address to receive the testnet ICX, the one you created and downloaded above.

Check your balance. 

```bash
# tbears -u https://bicon.net.solidwallet.io/api/v3 balance [account]
```



### Transaction fees

[Transaction fees explained](step.md).  

- `stepLimit` in transaction request message

  Every transaction request message has a `stepLimit` field. This value can not exceed the "maximun step limit" defined in the Governance SCORE. 

  ```json
  {
      "jsonrpc": "2.0",
      "method": "icx_sendTransaction",
      "id": 1234,
      "params": {
          "version": "0x3",
          "from": "hxe7af5fcfd8dfc67530a01a0e403882687528dfcb",
          "to": "cx9a4c4229ab2cbd61a5cc051fbbb6ee7e3e3adfac",
          "stepLimit": "0x123450",
           ...
  ```

- `stepUsed` in transaction result 

  You may have already noticed that every transaction result returns a `stepUsed` field. This is the actual amount of step consumed by the transaction. It is a good practice to test against T-Bears to figure out the rough step amount required by each transaction. On T-Bears, step is calculated but the step price is set to zero, so transaction fee is always zero. T-Bears will return `Out of step` error, if the step usage reaches the `stepLimit` defined in your trasaction request. 

  ```json
  {
      "jsonrpc": "2.0",
      "result": {
          "txHash": "0xc40cbbf2b89cd1e2890....",
          "blockHeight": "0x3158",
          "blockHash": "0x9e0c1385128bf0d4257....",
           ...
          "stepUsed": "0x4d361d0",
           ...
  ```

- `Out of step` error in transaction result

  When you recieve an `Out of step` error, increase the `stepLimit` in the request message. The value should be larger than `stepUsed`.

  ```json
  {
      "jsonrpc": "2.0", 
      "result": {
          "txHash": "0x9f512fe4b431d8780d75c29ad307f3....", 
           ...
          "stepUsed": "0x4d361d0",
          "status": "0x0", 
          "failure": {
              "code": "0x7d64", 
              "message": "Out of step: contractSet"
          }
          ...
  ```



### Deploy HelloWorld contact to the testnet (T-Bears CLI, Python)

Unlike T-Bears emulated environment, on testnet, valid sinature is required to send a transaction. So, every transaction request will require a keystore file to sign it. 

#### T-Bears CLI

We will modify the default cli configuration file, "tbears_cli_config.json",  for testnet. In this config file, we will set `uri`, `nid`, and `stepLimit`. 

```bash
{
    "uri": "https://bicon.net.solidwallet.io/api/v3",  <-- testnet api endpoint
    "nid": "0x3",  <-- testnet network id. deploy command reads this value.
    "keyStore": null,
    "from": "hxe7af5fcfd8dfc67530a01a0e403882687528dfcb",
    "to": "cx0000000000000000000000000000000000000000",
    "stepLimit": "0x5000000",  <-- deploy command reads this value.
    ...
}
```

`tbears deploy` command with `-k [keystore_file]` options will install the SCORE on testnet. Target network info (uri, nid) is read from the default config file.  Don't forget to get the SCORE address from the transaction result. 

```bash
root@07dfee84208e:/tbears# tbears deploy hello_world -k keystore_test2
root@07dfee84208e:/tbears# tbears txresult [txhash]
```



### Execute HelloWorld contract (T-Bears, Python)

#### T-Bears

Same command will invoke `hello` method on testnet. Read-only function call does not require keystore file. Testnet `uri` is read from the defaul config file.  Just make sure you correctly updated `to` and `from` values in the "call.json" request message.

```bash
root@07dfee84208e:/tbears# tbears call call.json

root@07dfee84208e:/tbears# cat call.json 
{
    "jsonrpc": "2.0",
    "method": "icx_call",
    "id": 1,
    "params": {
        "from": "hxbac99ffea54749ca1c86ab4e6bfe0b630bf7a7a0",  <-- test2 address
        "to": "cx9a4c4229ab2cbd61a5cc051fbbb6ee7e3e3adfac",  <-- SCORE address on testnet 
        "dataType": "call", 
        "data": {
            "method": "hello" 
        }
    }
}
```



#### Python SDK

We will create a "hello.py" to invoke a ` hello` method of the contract on testnet. Use the actual SCORE address and keystore password in your code.

```python
from iconsdk.icon_service import IconService
from iconsdk.providers.http_provider import HTTPProvider
from iconsdk.wallet.wallet import KeyWallet
from iconsdk.builder.call_builder import CallBuilder

node_uri = "https://bicon.net.solidwallet.io/api/v3"
network_id = 3
hello_world_address = "__SCORE_ADDRESS__"
keystore_path = "./keystore_test2"
keystore_pw = "__PASSWORD__"

wallet = KeyWallet.load(keystore_path, keystore_pw)
tester_addr = wallet.get_address()
icon_service = IconService(HTTPProvider(node_uri))

call = CallBuilder().from_(tester_addr)\
                    .to(hello_world_address)\
                    .method("hello")\
                    .build()

result = icon_service.call(call)
print(result)
```

Because you don't need to sign your read-only function call request, creating a wallet instance from the keystore file is not a mandatory step. We created a wallet instance just to get your account address. 

Run the code. 

```bash
$ python3 test.py
Hello, hxbac99ffea54749ca1c86ab4e6bfe0b630bf7a7a0. My name is HelloWorld
```

