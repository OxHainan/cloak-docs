=================================
Quick Start
=================================

---------------
Prerequisites
---------------
Before using Cloak, you need to know Cloak is a framework that includes a
Cloak Service and a Cloak language compiler as
`Introduction <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html>`__
described, in a normal case, we will provide a test Cloak Service, though
you can deploy Cloak Service for yourself, check `deploy Cloak
Service <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/initialize-cloak-network-on-blockchain.html>`__.

---------------
Installation
---------------
To install compiler, there are two ways, the easiser way is to use docker:

.. code:: 

   docker pull plytools/circleci-compiler:v0.2.0

Or install it to any host that you want, Cloak Compiler is implemented by
Python 3, so you need to prepare an environment that includes an executable
Python 3 and pip3, and its version is at least greater than 3.8.

Clone code:

.. code:: 

   git clone https://github.com/OxHainan/cloak-compiler.git
   cd cloak-compiler

Setup:

.. code:: 

   python3 setup.py develop


--------------------
Cloak by Examples
--------------------
The steps that use cloak:

1. Write a Cloak file
2. Compile it to generate public_contract.sol, private_contract.sol and policy.json
3. Deploy public_contract.sol to Cloak Blockchain
4. Register your public key to PKI contract
5. Deploy private_contract.sol to cloak-tee
6. Bind policy.json to the private contract
7. Propose or participate a transaction

Here we will show you how to finish there steps through `demo.cloak <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/index.html>`__:

demo.cloak
**********************

.. code-block::

    // SPDX-License-Identifier: Apache-2.0

    pragma cloak ^0.2.0;

    contract Demo {
        final address@all _manager;                  // all
        mapping(address => uint256) pubBalances;     // public
        mapping(address!x => uint256@x) priBalances; // private

        constructor(address manager) public {
            _manager = manager;
            pubBalances[manager] = 1000;
        }

        //  CT-me
        //
        // @dev Deposit token from public to private balances
        // @param value The amount to be deposited.
        //
        function deposit(uint256 value) public returns (bool) {
            require(value <= pubBalances[me]);
            pubBalances[me] = pubBalances[me] - value;
            priBalances[me] = priBalances[me] + value;
            return true;
        }

        //  MPT
        //
        // @dev Transfer token for a specified address
        // @param to The address to transfer to.
        // @param value The amount to be transferred.
        //
        function multiPartyTransfer(address to, uint256 value)
            public
            returns (bool)
        {
            require(value <= priBalances[me]);
            require(to != address(0));

            priBalances[me] = priBalances[me] - value;
            priBalances[to] = priBalances[to] + value;
            return true;
        }
    }

For demonstrating the demo.cloak, We use the following test account as an example.

.. code::

   private key: 0x55b99466a43e0ccb52a11a42a3b4e10bfba630e8427570035f6db7b5c22f689e
   address: 0xDC8FBC8Eb748FfeBf850D6B93a22C3506A465beE

Compile Cloak Contract
**********************

.. code:: 

    python cloak/__main__.py compile -o output demo.cloak

There are three important files in the output directory, including public_contract.sol, private_contract.sol and policy.json.

* public_contract.sol: a solidity contract, it will be deployed to Blockchain.
* private_contract.sol: a solidity contract, it will be deployed to cloak-tee and be executed by eEVM in TEE environment.
* policy.json: privacy policy definition of the Cloak smart contract binding to the private contract.

Deploy Public Contract
***********************
Web3 is a recommended tool for deploying the public contract to the blockchain.
For convenience, cloak-complier provides a command to complete it.

.. code::

    python cloak/__main__.py deploy <compiled output dir> <args...>  --blockchain-backend w3-ganache --blockchain-node-uri http://127.0.0.1:8545 --blockchain-pki-address <PKI Address> --blockchain-service-address <cloak service address>

`<args...>` option is the constructor function arguments. In this example, it is *0xDC8FBC8Eb748FfeBf850D6B93a22C3506A465beE*.

Use cloak-client
**********************
After you deploy public_contract.sol, for the next steps, we have writed a `sample <https://github.com/OxHainan/cloak-client/tree/main/samples/demo>`__ that uses cloak-client to show you how to register pk, deploy private contract, bind privacy policy and send MPT, *etc*.

Clone cloak-client and change directory to sample/demo:

.. code::

   git clone https://github.com/OxHainan/cloak-client.git
   cd cloak-client/samples/demo

Install dependencies:

.. code::

   npm install

run:

.. code::

   # CCF_AUTH_DIR: a directory that includes CCF network.cert and a user cert and pk, typically workspace/sandbox_common/ under cloak-tee build directory if you use sandbox.sh setup cloak-tee.
   # COMPILE_DIR: cloak-compiler output directory
   node index.js <CCF_AUTH_DIR> <COMPILE_DIR> <PKI_ADDRESS> <PUBLIC_CONTRACT_ADDRESS>

More detail usage of `cloak-client document <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#cloak-client>`__,
the full `sample code <https://github.com/OxHainan/cloak-client/tree/main/samples/demo>`__

