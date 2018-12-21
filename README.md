# ICON - Hyperconnect the World

ICON is a scalable smart contract enabled blockchain platform with an innovative consensus mechanism, decentralized governance structure, and a long-term goal of interoperability between enterprise and public blockchains.

As a brief overview of the technical details: ICON has smart contracts known as SCOREs that are written in Python, an enhanced BFT (Byzantine Fault Tolerance) based algorithm known as LFT, and a concept known as “Virtual Step” allowing SCORE operators to cover user transaction fees.

Our goal is to Hyperconnect the World, and by combining groundbreaking technology, a strong community, and relentless growth strategies we believe this goal is reachable.

# Network Overview
  - LFT
  - [Transaction Fees](/docs/step.md) - \[[download yellow paper](https://icon.foundation/resources/file/ICON_Yellowpaper_Transactionfee_EN_V1.0.pdf)\]
  - IISS - \[[download yellow paper](https://icon.foundation/resources/file/ICON_Yellowpaper_ICON_Incentives_Scoring_System_EN_V1.0.pdf)\]
  - BTP
  - Governance - \[[download yellow paper](https://icon.foundation/resources/file/ICON_Yellowpaper_ICONstitution_and_Governance_EN_V1.0.pdf)\]

# [ICON Network](/docs/icon_network.md)
  - [Private Devnet on AWS](/docs/icon_network.md#private-devnet-on-aws)
  - [Testnet for DApps](/docs/icon_network.md#testnet-for-dapps)
  - [Testnet for Exchanges](/docs/icon_network.md#testnet-for-exchanges)
  - [Mainnet](/docs/icon_network.md#mainnet)

# [Account Management](/docs/wallet.md)

# Client SDK
Client SDK implements ICON JSON-RPC API v3. Java, Python, and JavaScript libraries are available for your application to interact with Smart Contracts and the ICON network. SDKs and T-Bears CLI provide an ability to generate a signed transaction using given private key. If you use raw JSON-RPC APIs, you need to generate a signature for each transaction, please refer to [Generating transaction signature](/docs/transaction_signature.md) to learn how to sign a transaction. 
  - [CLI (T-Bears)](/docs/tbears_cli.md)
  - [Java SDK](https://github.com/icon-project/icon-sdk-java/blob/master/quickstart/README.md)
  - [Python SDK](https://github.com/icon-project/icon-sdk-python/blob/master/README.md)
  - [JavaScript SDK](https://github.com/icon-project/icon-sdk-js/blob/master/README.md)
  - [JSON-RPC API v3](https://github.com/icon-project/icon-rpc-server/blob/master/docs/icon-json-rpc-v3.md)
  - [Generating transaction signature](/docs/transaction_signature.md)
  
# SCORE
  - [SCORE Guide](https://icon-project.github.io/score-guide/)
  - [T-Bears Development Suite](https://github.com/icon-project/t-bears/blob/master/README.md)

# [SCORE Audit](/docs/score_audit.md)
  - [Audit Checklist](/docs/audit_checklist.md)
  - [Deploy Guideline](/docs/score_deploy_guide.md)

# Tutorials
  - [Quickstart Guide](/docs/quickstart.md)
    - [Part 1. HelloWorld on local emulated environment](/docs/quickstart_p1.md)
    - [Part 2. HelloWorld on testnet](/docs/quickstart_p2.md)
  - Sample SCOREs 
    - [Hello World](https://github.com/icon-project/samples/blob/master/hello_world/README.md)
    - [IRC2 Token](https://github.com/icon-project/samples/blob/master/irc2_token)
    - [Crowdsale](https://github.com/icon-project/samples/blob/master/crowdsale)
    - [Multisig Wallet](https://github.com/icon-project/multisig-wallet)
