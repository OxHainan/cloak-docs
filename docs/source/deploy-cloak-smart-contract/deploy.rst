
=================================
Deploy to Blockchain
=================================

deploy the generated smart contracts to a blockchain


=================================
Deploy to Cloak Network
=================================

deploy the generated smart contracts to the Cloak Network


=================================
Cloak Client
=================================
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
            data: web3.utils.Hex(<POLICY FILE DATA>)
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
