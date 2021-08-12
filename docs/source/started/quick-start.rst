=================================
Quick Start
=================================

---------------
Prerequisites
---------------
Before use Cloak, you need to know Cloak is a framework that includes a
Cloak service and a Cloak language compiler as
`Introduction <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html>`__
described, in normal case, we will provide a test cloak service, though
you can deploy Cloak service for yourself, check `deploy cloak
service <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/index.html>`__.

---------------
Installation
---------------
to install, there two ways, the easy way is to use docker:

.. code:: 

   docker pull plytools/circleci-compiler:v0.2.0

Or install to any host what you want, Cloak compiler is implemented by
Python 3, so you need prepare a environment which includes a executable
Python 3 and pip3, and its version is at least greater than 3.8.

clone code:

.. code:: 

   git clone https://github.com/OxHainan/cloak-compiler.git
   cd cloak-compiler

setup:

.. code:: 

   python3 setup.py develop


--------------------
Cloak by Examples
--------------------
Here we will show you how to compile, deploy, call a Cloak contract through `demo.cloak <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/index.html>`__:

.. code-block::

    // SPDX-License-Identifier: Apache-2.0

    pragma cloak ^0.2.0;

    contract Demo {
        final address@all _manager; // all
        mapping(address => uint256) pubBalances; // public
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

compile
**********************

.. code:: 

    python cloak/__main__.py compile -o output demo.cloak

there are three important files in output directory, which are public_contract.sol, private_contract.sol and policy.json

* private_contract.sol: a normal solidity contract deploying to Cloak-Tee, is been executed by eEVM in TEE environment.
* public_contract.sol: a normal solidity contract too deploying to Blockchain.
* policy.json: it is the privacy policy definition of the cloak contract binding to private contract.

deploy
**********************


------------------------------
Enable Cloak on Blockchain
------------------------------


