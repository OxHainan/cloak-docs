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
To install compiler, there are two ways, the easy way is to use docker:

.. code:: 

   docker pull plytools/circleci-compiler:v0.2.0

Or install it to any host that you want, Cloak compiler is implemented by
Python 3, so you need to prepare an environment which includes a executable
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

Compile cloak contract
**********************

.. code:: 

    python cloak/__main__.py compile -o output demo.cloak

There are three important files in output directory, including public_contract.sol, private_contract.sol and policy.json.

* public_contract.sol: a solidity contract, it will be deployed to Blockchain.
* private_contract.sol: a solidity contract, it will be deployed to Cloak-Tee and be executed by eEVM in TEE environment.
* policy.json: privacy policy definition of the cloak contract binding to the private contract.

Deploy public contract
***********************
To deploy public contract to blockchain, you can use web3(or others), but cloak-compiler provide a command that can easily finish it:

.. code::

    python cloak/__main__.py deploy <compiled output dir> <args...>  --blockchain-backend w3-ganache --blockchain-node-uri http://127.0.0.1:8545 --blockchain-pki-address <PKI Address> --blockchain-service-address <cloak service address>

`<args...>` option is the constructor function arguments.

We have writed a `sample <https://github.com/OxHainan/cloak-client/tree/main/samples/demo>`__ that uses cloak-client to show you how to register pk, deploy private contract, bind privacy policy and send MPT etc,
next, we will step by step go through the cloak transaction workflow based on the sample.

Register public key
***********************
Before you execute an MPT, if you are the owner of some state data (*e.g.*, _manager in Demo contract),
you need to register your public key to PKI contract,
and the public key must be specified by a standard PEM format,
here is an example that get a PEM-format public key from hex-string private key:

.. code::

    echo 302e0201010420 <PRIVATE KEY> a00706052b8104000a | xxd -r -p | openssl ec -inform d -pubout

Replace <PRIVATE KEY> with `55b99466a43e0ccb52a11a42a3b4e10bfba630e8427570035f6db7b5c22f689e` and execute:

.. code::

   ➜  ~ echo 302e0201010420 55b99466a43e0ccb52a11a42a3b4e10bfba630e8427570035f6db7b5c22f689e a00706052b8104000a | xxd -r -p | openssl ec -inform d -pubout
   read EC key
   writing EC key
   -----BEGIN PUBLIC KEY-----
   MFYwEAYHKoZIzj0CAQYFK4EEAAoDQgAEXFZ6txDg9knTl5E5mFnj7G1j9x91x5d9
   MPCYiA6CoewqASjNGc8orVGol8ajLiz3rnleoSm2OyoPsM/3KXdrMg==
   -----END PUBLIC KEY-----

Based on it, in our demo sample, register pk to blockchain looks like:

.. code::

   async function get_pem_pk(account) {
      const cmd = `echo 302e0201010420 ${account.privateKey.substring(2,)} a00706052b8104000a | xxd -r -p | openssl ec -inform d -pubout`
      const {stdout,} = await p_exec(cmd)
      return stdout.toString()
   }

   async function register_pki(web3, account) {
     const pki_file = compile_dir + "/CloakPKI.sol"
     const [abi, ] = await get_abi_and_bin(pki_file, "CloakPKI")
     var pki = new web3.eth.Contract(abi, pki_address)
     var tx = {
         to: pki_address,
         data: pki.methods.announcePk(await get_pem_pk(account)).encodeABI(),
         gas: 900000,
         gasPrice: 0,
     }
     var signed = await web3.eth.accounts.signTransaction(tx, account.privateKey)
     return web3.eth.sendSignedTransaction(signed.rawTransaction)
   }

cloak web3
***********************
Cloak-client wraps a web3 Provider, so you can create a web3 object and create _manager account like:

.. code::

    const httpsAgent = new Agent({
        rejectUnauthorized: false,
        ca: readFileSync(args[0]+"/networkcert.pem"),
        cert: readFileSync(args[0]+"/user0_cert.pem"),
        key: readFileSync(args[0]+"/user0_privk.pem"),
    });

    var web3 = new Web3()
    web3.setProvider(new CloakProvider("https://127.0.0.1:8000", httpsAgent, web3))
    const acc_1 = web3.eth.accounts.privateKeyToAccount("0x55b99466a43e0ccb52a11a42a3b4e10bfba630e8427570035f6db7b5c22f689e");

`https://127.0.0.1:8000` is cloak-tee service host and port,
because of encryption, cloak-tee can only accept https request, so you need to provide the network.pem of cloak network as CA, and a trusted user(cert and pk), 
`args[0]` is directory of the three file, if you use cloak.py setup your cloak-tee, it will be workerspace/sanbox_common under cloak-tee build directory.

Deploy private contract
************************
Deploy private contract is more like standard web3 except the web3 object is wrapped by ``CloakProvider``:

.. code::

    async function get_abi_and_bin(file, name) {
        const cmd = `solc --combined-json abi,bin,bin-runtime,hashes --evm-version homestead --optimize ${file}`
        const {stdout,} = await p_exec(cmd)
        const j = JSON.parse(stdout)["contracts"][`${file}:${name}`]
        return [j["abi"], j["bin"]]
    }

    async function deployContract(web3, account, file, name, params) {
        const [abi, bin] = await get_abi_and_bin(file, name)
        var contract = new web3.eth.Contract(abi)
        return contract.deploy({data: bin, arguments: params}).send({from: account.address})
    }


Bind privacy policy
************************

.. code::

    const code_hash = web3.utils.sha3(readFileSync(code_file))
    await web3.cloak.sendPrivacyTransaction({
        account: acc_1,
        params: {
            to: deployed.options.address,
            codeHash: code_hash,
            verifierAddr: public_contract_address,
            data: web3.utils.toHex(readFileSync(policy_file))
        }
    })

Send deposit transaction
*************************
Deposit function store the balance to private mapping from public contract, the proposer and participant is same one (so-called CT).

.. code::

    // deposit
    var mpt_id = await web3.cloak.sendMultiPartyTransaction({
        account: acc_1,
        params: {
            nonce: web3.utils.toHex(100),
            to: deployed.options.address,
            data: {
                "function": "deposit",
                "inputs": [
                    {"name": "value", "value": "100"},
                ]
            }
        }
    })

Get transaction result
**************************

.. code::

    // wait 3 second
    await new Promise(resolve => setTimeout(resolve, 3000));
    web3.cloak.getMultiPartyTransaction({id: mpt_id}).then(console.log).catch(console.log)

After send a CT/MPT transaction to cloak network, it will return a MPT ID, you can use that id check the transaction status,
wait 3 second seem stupid, it did, so we will provide a function that loops to get status until MPT finished later.

send multi party transfer
**************************
This code show how to propose a MPT and how to participate that MPT:

.. code::

    // multi party transfer
    const acc_2 = web3.eth.accounts.create();
    await register_pki(ganache_web3, acc_2)

    var mpt_id = await web3.cloak.sendMultiPartyTransaction({
        account: acc_1,
        params: {
            nonce: web3.utils.toHex(100),
            to: deployed.options.address,
            data: {
                "function": "multiPartyTransfer",
                "inputs": [
                    {"name": "value", "value": "10"},
                ]
            }
        }
    })

    await web3.cloak.sendMultiPartyTransaction({
        account: acc_2,
        params: {
            nonce: web3.utils.toHex(100),
            to: mpt_id,
            data: {
                "function": "multiPartyTransfer",
                "inputs": [
                    {"name": "to", "value": acc_2.address},
                ]
            }
        }
    })

For more detail usage of cloak-client, please check: 
`<https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/deploy-cloak-smart-contract/deploy.html#cloak-client>`__,
the full sample code: `<https://github.com/OxHainan/cloak-client/tree/main/samples/demo>`__

