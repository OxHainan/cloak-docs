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
service <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/tee-blockchain-architecture/initialize-cloak-network-on-blockchain.html>`__.

---------------
Installation
---------------
to install compiler, there are two ways, the easy way is to use docker:

.. code:: 

   docker pull plytools/circleci-compiler:v0.2.0

Or install it to any host what you want, Cloak compiler is implemented by
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


For demonstrating the demo.cloak, we suppose that a test account is:

.. code::

   private key: 0x55b99466a43e0ccb52a11a42a3b4e10bfba630e8427570035f6db7b5c22f689e
   address: 0xDC8FBC8Eb748FfeBf850D6B93a22C3506A465beE

register public key
***********************
Before you execute a MPT, if you are the owner of some state data (e.g. _manager in Demo contract),
you need to register your public key to PKI contract,
and the public key must be specified by a stardard PEM format,
here is a example that get a PEM-format public key from hex-string private key:

.. code::

    echo 302e0201010420 <PRIVATE KEY> a00706052b8104000a | xxd -r -p | openssl ec -inform d -pubout

replace <PRIVATE KEY> with `55b99466a43e0ccb52a11a42a3b4e10bfba630e8427570035f6db7b5c22f689e` and execute:

.. code::

   ➜  ~ echo 302e0201010420 55b99466a43e0ccb52a11a42a3b4e10bfba630e8427570035f6db7b5c22f689e a00706052b8104000a | xxd -r -p | openssl ec -inform d -pubout
   read EC key
   writing EC key
   -----BEGIN PUBLIC KEY-----
   MFYwEAYHKoZIzj0CAQYFK4EEAAoDQgAEXFZ6txDg9knTl5E5mFnj7G1j9x91x5d9
   MPCYiA6CoewqASjNGc8orVGol8ajLiz3rnleoSm2OyoPsM/3KXdrMg==
   -----END PUBLIC KEY-----


compile
**********************

.. code:: 

    python cloak/__main__.py compile -o output demo.cloak

there are three important files in output directory, which are public_contract.sol, private_contract.sol and policy.json

* private_contract.sol: a solidity contract, it will be deployed to Cloak-Tee and be executed by eEVM in TEE environment.
* public_contract.sol: a solidity contract, it will be deployed to Blockchain.
* policy.json: privacy policy definition of the cloak contract binding to the private contract.

deploy public contract
***********************
To deploy public contract to blockchain, you can use web3(or others), but cloak-compiler provide a command that can easily finish it:

.. code::

    python cloak/__main__.py deploy <compiled output dir> <args...>  --blockchain-backend w3-ganache --blockchain-node-uri http://127.0.0.1:8545 --blockchain-pki-address <PKI Address> --blockchain-service-address <cloak service address>

<args...> option is the constructor function arguments.

cloak-client
************************
To deploy private contract, send policy and execute MPT to cloak-tee, cloak-client is a good tool that implements web3 provider.

install cloak-client:

.. code::

   npm install OxHainan/cloak-client

cloak-client is a extended web3, so the usage of cloak-client is same as web3 except cloak module.

Get web3 Object and set CloakProvider:

.. code::

   const cloak = require('cloak-client');

   const httpsAgent = new https.Agent({
       rejectUnauthorized: false,
       ca: <CCF network ca>,
       cert: <CCF USER cert>,
       key: <CCF USER PK>,
   });

   var web3 = new Web3()
   // "https://127.0.0.1:8000" is a CCF(cloak-tee) URI
   web3.setProvider(new cloak.CloakProvider("https://127.0.0.1:8000", httpsAgent, web3))

Deploy a solidity contract:

.. code::

    var contract = new Contract(jsonInterface, address);

    contract.methods.somFunc().send({from: ....})
    .on('receipt', function(){
        ...
    });

extended cloak module
***********************
There are extended functinos under `web3.cloak`, which include send policy, send MPT and get MPT etc.

send policy:

.. code::

    web3.cloak.sendPrivacyTransaction({
        account: account,
        params: {
            to: <PRIVATE CONTRACT ADDRESS>,
            codeHash: <HASH OF PRIVATE CONTRACT>,
            verifierAddr: <PUBLIC CONTRACT ADDRESS>,
            data: JSON.parse(<POLICY FILE DATA>))
        }
    })

the return value is a Policy HASH.

send MPT:

.. code::

   return web3.cloak.sendMultiPartyTransaction({
      account: account,
      params: {
          nonce: <NONCE>,
          to: <PRIVATE CONTRACT ADDRESS OR MPT ID>,
          data: <CALL DATA JSON>
      }
   })

* nonce: same as Ethereum nonce
* to: if `to` is private contract address, that mean to propose a MPT transaction, otherwise, that mean to participate a MPT(which id is <MPT ID>).
* data: it includes the function what you what you want to call and input arguments, it look like:

  .. code::
    
    {
        "function": "getSum",
        "inputs" : [
            { "name": "_a", "value": "100"},
            { "name": "_b", "value": "201"}
        ]
    }

Executed MPT will not get result immediately, it will return a `id` of that MPT regardless of proposing or participating, you need call getMPT to check the MPT status and result.

get MPT:

.. code::

   web3.cloak.getMultiPartyTransaction({id: <MPT ID>})
