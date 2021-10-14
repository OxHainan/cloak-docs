<img  width="280" src="https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/_static/logo.png" alt="cloak-logo" align="left">

<h1 align="center">
    <a>
    Cloak Docs
  </a>
</h1>

This is a documentation, guiding people to understand and use Cloak.

## What is Cloak?

Cloak is a pluggable and configurable framework for developing and deploying confidential smart contracts. 
To this end, Cloak allows users to specify *privacy invariants*
(what is private data and to who is the data private) in a 
declarative way. Then, it automatically generate runtime with verifiably 
enforced privacy and deploy it to the existing EVM-enabled platforms 
(*e.g.*, Ethereum) and TEE devices to enable the confidential smart 
contract. 


Cloak is designed to work with TEE and EVM-enabled blockchain. 
It initializes the blockchain in a pluggable manner to become a new architecture, where the blockchain and its clients are enhanced to be the Cloak Blockchain and the Cloak Client respectively, and the Cloak Network is added by Cloak.

In [**Cloak Networks**][cloak-networks], all nodes run the same application inside an enclave.
The user sends confidential transactions or multi-party transactions to the network. 
A consortium of members sending governance transactions in charge of governing the network. 
The effects of user and member transactions are eventually committed to a replicated encrypted ledger.

![Clock Network](./docs/source/imgs/cloak-network.svg)

[cloak-networks]: https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/cloak-network.html#cloak-network

## 📖 Table of contents

- Getting Started
    - [Introduction](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html)
        - [What is Cloak?](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html#what-is-cloak)
        - [Cloak Overview](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html#cloak-overview)
        - [Advanced Features](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html#advanced-features)
        - [Limitations](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html#limitations)
        - [Security Disclaimer](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html#security-disclaimer)
    - [Quick Start](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/quick-start.html)
        - [Prerequisites](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/quick-start.html#prerequisites)
        - [Installation](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/quick-start.html#installation)
        - [Cloak by Examples](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/quick-start.html#cloak-by-examples)
    - [Call for Contributions](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/contribute.html)
        - [All Contributions Counts](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/contribute.html#all-contributions-counts)
        - [How to Report Issues](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/contribute.html#how-to-report-issues)
        - [Workflow for Pull Requests](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/contribute.html#workflow-for-pull-requests)
        - [Documentation Style Guide](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/contribute.html#documentation-style-guide)

- Decentralized Oracle Network
    - [Cloak Network](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/cloak-network.html)
        - [Cloak Network Overview](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/cloak-network.html#cloak-network-overview)
        - [Workflow of Transaction](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/cloak-network.html#workflow-of-transaction)
    - [Initialize Cloak Network on Blockchain](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/initialize-cloak-network-on-blockchain.html)
        - [Deploy PKI and Cloak Service Contract](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/initialize-cloak-network-on-blockchain.html#deploy-pki-and-cloak-service-contract)
        - [Build Cloak-tee](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/initialize-cloak-network-on-blockchain.html#build-cloak-tee)
        - [Setup Cloak Service](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/initialize-cloak-network-on-blockchain.html#setup-cloak-service)

- Develop Cloak Smart Contract
    - [Cloak Language](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html)
        - [Locations](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html#locations)
        - [Data Types](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html#data-types)
        - [Privacy Types](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html#privacy-types)
        - [Type Declarations](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html#type-declarations)
        - [Expressions](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html#expressions)
        - [Statements](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html#statements)
        - [Functions](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html#functions)
        - [A Simple Cloak Contract](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/cloak-language.html#a-simple-cloak-contract)
    - [Cloak Compiler](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/compiler.html)
        - [Compile](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/compiler.html#compile)
        - [Debug](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/develop-cloak-smart-contract/compiler.html#debug)

- Deploy Cloak Smart Contract
    - [Cloak Blockchain](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html)
    	- [Deploy Public Key](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#deploy-public-key)
    	- [Deploy Public Contract](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#deploy-public-contract)
    	- [Deploy private contract](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#deploy-private-contract)
    	- [Bind privacy policy](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#bind-privacy-policy)
    	- [Send Transaction](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#send-transaction)
    - [Cloak Client](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#cloak-client)
        - [Install](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#install)
        - [Cloak Web3](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#cloak-web3)
        - [Deploy a Solidity contract](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#deploy-a-solidity-contract)
        - [sendPrivacyTransaction](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#sendprivacytransaction)
        - [sendMultiPartyTransaction](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#sendmultipartytransaction)
        - [getMultiPartyTransaction](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#getmultipartytransaction)

- Contribute
    - [Roadmap](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/roadmap/index.html)
    - [API Documentation](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/apidoc/index.html)

- About Cloak
    - [Publications](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/publications/publications.html)
        - [Technical Papers and Reports](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/publications/publications.html#technical-papers-and-reports)
        - [Release Notes](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/publications/publications.html#release-notes)
    - [About Us](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/about.html)
        - [Oxford-Hainan Blockchain Research Institute](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/about.html#oxford-hainan-blockchain-research-institute)
        - [Cloak Team](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/about.html#cloak-team)

## Contents

- [Building The Docs](#-building-the-docs)
- [Learn More](#-learn-more)
- [Related Links](#-related-links)
- [How to Contribute](#-how-to-contribute)
- [Warning](#-warning)

## 🎉 Building The Docs

Cloak's documentation is contained in the `docs` folder and is published to [Read the Docs](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/#). It is based on Sphinx and can be built using the Makefile contained in the subdirectory:

### 📋 Requirements

```shell
$ pip install -r requirements.txt
```

### 🎉Building Documents

```shell
$ cd docs
$ make html
```

## 📖 Learn More

- Browse the [documentation](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/#).
- Read the [Research Papers](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/publications/publications.html).

## 📖 Related Links

- A trusted EVM of Cloak framework, [cloak-tee](https://github.com/OxHainan/cloak-tee).
- A compiler of Cloak framework, [cloak-compiler](https://github.com/OxHainan/cloak-compiler).
- The client of Cloak for providing web3-compatable API and interacting with cloak-compiler and cloak-tee, [cloak-client](https://github.com/OxHainan/cloak-client).

## 👏 How to Contribute

The main purpose of this repository is to continue evolving Cloak TEE core. We want to make contributing to this project as easy and transparent as possible, and we are grateful to the community for contributing bug fixes and improvements. 
Read below to learn how you can take part in improving Cloak TEE.

### [Code of Conduct][code]

Cloak TEE has adopted a Code of Conduct that we expect project participants to adhere to.
Please read the [full text][code] so that you can understand what actions will and will not be tolerated.

[code]: https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/contribute.html#documentation-style-guide

### [Contributing Guide][contribute]

Read our [**Call for Contributions**][contribute] to learn about our development process, how to propose bugfixed and improvements, and how to build and test your changes to Cloak.

[contribute]: https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/contribute.html#all-contributions-counts

### [Open Source Roadmap][roadmap]

You can learn more about our vision for Cloak Networks in the [**Roadmap**][roadmap].

[roadmap]: https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/roadmap/index.html#roadmap

### Submit Issues

If you find a bug or have some new idea, please submit it to [**issues**][issues]. This is a great place to get started, gain experience,
and get familiar with our contribution process.

[issues]: https://github.com/OxHainan/cloak-tee/issues

## ❗️ Warning

Cloak is an ongoing project. The security of our implementation has not been systematically reviewed yet! Do not use Cloak in a productive system or to process sensitive confidential data now. We will keep working on Cloak, making it cool and practical step-by-step. 
