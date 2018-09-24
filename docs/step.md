# Step Price

Transaction fee is calculated as `requiredStep = usedStep * stepPrice`.

- Where `stepPrice` is the ICX exchange rate, and `usedStep` is calulated by the `sum of (stepCost(action) * action)`.

- If the `requiredStep` exceeds the `stepLimit` you privided, the transaction will fail, but the amount of `stepLimit` is deducted from your balance.  

- Before excuting your transaction, there is a prevalidation of the `requiredStep`. You account must hold at least `stepLimit * stepPrice`. If you do not have sufficient ICX, your transaction will fail immediately.


You can query the `stepCost` of each action and the maximum possible `stepLimit` value you can set. 

- SCORE Address - cx0000000000000000000000000000000000000001
- [getStepCost](https://github.com/icon-project/governance/blob/master/README.md#getstepcosts)
- [getMaxStepLimit](https://github.com/icon-project/governance/blob/master/README.md#getmaxsteplimit)

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

```bash
root@b65c6a4cccf8:/tbears# cat maxsteplimit.json 
{
    "jsonrpc": "2.0",
    "id": 2,
    "method": "icx_call",
    "params": {
        "to": "cx0000000000000000000000000000000000000001",
        "dataType": "call",
        "data": {
            "method": "getMaxStepLimit",
            "params": {
                "contextType": "invoke"
            }
        }
    }
}
root@b65c6a4cccf8:/tbears# tbears call -u https://bicon.net.solidwallet.io/api/v3 maxsteplimit.json 
response : {
    "jsonrpc": "2.0",
    "result": "0x9502f900",
    "id": 2
}
```
