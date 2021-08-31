# Cloak-Docs

This is a Cloak documentation for guiding interested people to understand and use Cloak.

## What is Cloak

Cloak is a pluggable and configurable framework for developing and deploying confidential smart contracts. 
To this end, Cloak allows users to specify *privacy invariants*
(what is private data and to who is the data private) in a 
declarative way. Then, it automatically generate runtime with verifiably 
enforced privacy and deploy it to the existing EVM-enabled platforms 
(e.g., Ethereum) and TEE devices to enable the confidential smart 
contract. 

## Contents of the Docs

- GETTING STARTED, an introduction of Cloak and a tutorial to guide you how to run Cloak.
- TEE-BLOCKCHAIN ARCHITECTURE, the architecture of Cloak.
- DEVELOP CLOAK SMART CONTRACT, the guide to develop Cloak contract.
- DEPLOY CLOAK SMART CONTRACT, the guide to deploy Cloak contract.
- CONTRIBUTE, the roadmap and API documentation.
- ABOUT CLOAK, the research papers and team of Cloak.

## Build The Docs

### Prerequisites

```shell
$ pip install -r requirements.txt
```

### Build Documents

```shell
$ cd docs
$ make html
```

## Learn More

- Browse the [documentation](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/#)
- Read the [Research Papers](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/publications/publications.html)

## Contributing

This project welcomes contributions and suggestions. Please see the [Call for Contributions](https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/contribute.html).