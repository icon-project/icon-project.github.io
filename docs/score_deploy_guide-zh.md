# SCORE部署指南
本文档介绍了ICON网络中SCORE (Smart Contract on Reliable Environment)的部署过程。

通过T-Bears可以发布SCORE，DAPP开发人员需要一个钱包来支付交易费用。更新已发布的SCORE只能通过相同的钱包地址进行。

在ICON网络上运行的SCORE可能会故意或错误地攻击ICON网络。 ICON网络采用SCORE审核流程，以防止多数人提前受到少数人的攻击。 SCORE审核仅在ICON主网上进行。

ICON网络的审核员对DAPP开发人员部署的SCORE进行预先调查，以确定风险因素。 SCORE可以被“接受”或“拒绝”，只有审计员“接受”的SCORE才能在ICON网络中运行。

接下来是SCORE的状态图。如果DAPP开发人员请求通过T-Bears部署SCORE，则SCORE将在ICON网络中注册为“待处理”。只有在审核员接受后才会安装。如果确定它对ICON网络构成威胁，审核员将“拒绝”SCORE。激活后，初始部署者可以更新SCORE，先前的SCORE将保持“活动”状态，更新的SCORE则会处于“待处理”状态。

![](./images/state_diagram.png)

## 准备工作
对于DAPP开发人员部署SCORE，开发人员必须拥有足够支付服务费的钱包。

### T-Bears
请参考 [ICON SCORE开发套件（tbears）教程](https://github.com/icon-project/t-bears/blob/master/README.md) and install.

### 钱包
您可以在ICONex中创建钱包。

可以在 https://icon.foundation/ 的电子钱包菜单中访问ICONex，并安装其chrome扩展程序安装。您可以存入一定数量的ICX，交易费将从余额中扣除。创建钱包后，您可以备份密钥文件，该文件将在从T-Bears CLI部署SCORE时使用。

![](./images/wallet_backup.png)

密钥文件也可以在T-Bears中创建，在这种情况下，您可以将密钥文件导入ICONex。

```bash
(work) $ tbears keystore key_DAPPDEV.txt

input your key store password:

Made keystore file successfully

```
![](./images/wallet_load.png)

### 追踪系统
ICON网络提供追踪系统（https://tracker.icon.foundation/），您可以从这访问任何区块和交易的状态。 

![](./images/tracker.png)

## 部署

### tbears 部署安装
开启 `tbears_cli_config.json` 文档并确认 `keyStore`, `contentType`, `mode` 和 `uri` 质皆为正确。

`uri`，请使用主网的uri，`keyStore`，请设定路径至您的密钥文件。

```text
{
"uri": "http://wallet.icon.foundation:9000/api/v3",
"nid": "0x3",
"keyStore": "key_DAPPDEV.txt",
"from": "hxaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",
"to": "cx0000000000000000000000000000000000000000",
"stepLimit": "0x300000",
"deploy": {
    "contentType": "zip",
    "mode": "install",
    "scoreParams": {}
},
"txresult": {},
"transfer": {}
}

```

部署您选择的SCORE。下面范例部署了名为“abc”的SCORE。

```bash
(work) $ tbears deploy abc

input your key store password:

Send deploy request successfully.

transaction hash: 0x469fce37cf1e7fb9892e1333a15d4e20f86e8f010b56fe0708bd89246dedcfbf
```

您可以通过追踪系统中的交易散列（上面的命令结果）验证SCORE部署的结果。 ![](./images/deploy_1.png)

如果您在上面的屏幕中选择“创建合约”，则可以检查SCORE审核的状态。在审核员接受之前，状态为“待处理”。

![](./images/deploy_2.png)

On T-Bears, you can also get the transaction results using the transaction hash. In the result, you can get the SCORE address in the `scoreAddress` field as shown below. Note that the SCORE address is assigned before passing the audit, it doesn't mean it has been accepted.


在T-Bears上，您还可以使用交易散列获取交易结果。在结果中，您可以在`scoreAddress`字段中获取SCORE地址，如下所示。请注意，SCORE地址在通过审核之前已分配，但并不意味着它已被接受。

```bash
(work) $ tbears txresult 0x469fce37cf1e7fb9892e1333a15d4e20f86e8f010b56fe0708bd89246dedcfbf
 Transaction result: {
     "jsonrpc": "2.0",
     "result": {
         "txHash": "0x469fce37cf1e7fb9892e1333a15d4e20f86e8f010b56fe0708bd89246dedcfbf",
         "blockHeight": "0x1",
         "blockHash": "0x2e7012a444a49b69e7e31a6b8a5f7a38f7bd860ec5fcf896b15416169d1dc924",
         "txIndex": "0x0",
         "to": "cx0000000000000000000000000000000000000000",
         "scoreAddress": "cx7990f4e8e224e238f5eca089ebf48c5351b7ce30",
         "stepUsed": "0x216ee30",
         "stepPrice": "0x0",
         "cumulativeStepUsed": "0x216ee30",
         "eventLogs": [],
         "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
         "status": "0x1"
     },
     "id": 1
}
```

您可能会从上面的结果中注意到命令`tbears txresult`不会返回审核状态。此外，如果对SCORE地址运行`tbears scoreapi`，并且SCORE未激活（待处理或拒绝），将返回错误消息，如下所示。

```bash
(work) $ tbears scoreapi cx6177643ef0da653ce2f1c62c6220033e2d25e5cf
Can not get cx6177643ef0da653ce2f1c62c6220033e2d25e5cf's API
{
    "jsonrpc": "2.0",
    "error": {
        "code": -32602,
        "message": "SCORE is inactive: cx1e0135cff7fa06be17840badc9ad7144b4c82606"
    },
    "id": 1   
}
```

由于您无法使用tbears CLI查看SCORE审核状态，直到SCORE处于活动状态，因此您可以检查追踪系统上的SCORE审核状态。
只有在审核员接受审核结果时，SCORE状态才会变为“活动”。

![](./images/score_active.png)

一旦激活，`tbears scoreapi`将返回如下所示的有效结果（实际结果因实施而异）。此时，SCORE实际上正在ICON网络上运行。

```
(work) $ tbears scoreapi cx6177643ef0da653ce2f1c62c6220033e2d25e5cf
SCORE API: [
    {
        "type": "fallback",
        "name": "fallback",
        "inputs": []
    },
    {
        "type": "function",
        "name": "hello",
        "inputs": [],
        "outputs": [
            {
                "type": "str"
            }
        ],
        "readonly": "0x1"
    }
]
```

### tbears 部署更新
初始部署者可以更新SCORE。

您可以使用 -m 选项执行`tbears deploy`命令，或者在'tbears_cli_config.json'文件中将deploy模式设置为'update'。

```text
"deploy": {
    "mode": "update",
}
```

```bash
(work) $ tbears deploy -m update -o cx6177643ef0da653ce2f1c62c6220033e2d25e5cf abc

input your key store password:

Send deploy request successfully.

transaction hash: 0x95fd26a68ea60fe223a9a80cf5a54ab0cb7a895d65fa4836a2fc74380b230c54
```
![](./images/deploy_update.png)

如果您在上面的屏幕中单击“Contract Updated”，则可以看到SCORE的详细信息屏幕。您可以看到当前SCORE的状态，即“active”。在交易清单中，您可以看到“Contract Updated”，这意味着它处于“pending”状态。审核完成后，将显示“Contract Accepted”或“Contract Rejected”交易。

![](./images/deploy_update_contract.png)

## 审核被拒绝
当审核员拒绝SCORE时，SCORE状态变为“Rejected”。您可以在追踪系统的合约详细信息屏幕中查看状态。

![](./images/rejected_1.png)
您可以在拒绝交易中查看拒绝的详细原因。 

![](./images/rejected_2.png)

如果SCORE更新遭拒，先前的SCORE仍保持活动状态。

## 常见错误
1. 如果您无法支付交易费用，

```bash
(work) $ tbears deploy abc

input your key store password:

Got an error response
{'jsonrpc': '2.0', 'error': {'code': -32600, 'message': 'Out of balance'}, 'id': 1}
```

2. 只有初始部署者才能更新SCORE。如果您在使用 `deploy update` 使用其他钱包,![](./images/deploy_other_owner.png)
3. 如果你“deploy update”的SCORE不是处于active状态，

```bash
(work) $ tbears deploy -m update -o cx06a427d41e87612c27c3caa2f1d7444c69781dc9  abc

input your key store password:

Got an error response

{'jsonrpc': '2.0', 'error': {'code': -32600, 'message': 'cx06a427d41e87612c27c3caa2f1d7444c69781dc9 is inactive SCORE'}, 'id': 1}

```

---
[Reference](https://github.com/icon-project/icon-project.github.io/tree/2b560055206301b45d5af750d8a39165ebe0da1c)