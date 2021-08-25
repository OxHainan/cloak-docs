
=================================
Deploy to Blockchain
=================================

Deploy the generated smart contracts to the blockchain


=================================
Deploy to Cloak Network
=================================

Deploy the generated smart contracts to the Cloak Network


=================================
Cloak Client
=================================
Cloak-client
************************
To deploy the private contract, send policy to cloak-tee and execute MPT int it, cloak-client is a good tool that implements web3 provider.

Install cloak-client:

.. code::

   npm install OxHainan/cloak-client

Cloak-client is an extended web3, so the usage of cloak-client is the same as web3 except Cloak module.

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

Deploy a Solidity contract:

.. code::

    var contract = new Contract(jsonInterface, address);

    contract.methods.somFunc().send({from: ....})
    .on('receipt', function(){
        ...
    });

Extended Cloak Module
***********************
There are extended functions under `web3.cloak`, which include send policy, send MPT and get MPT, etc.

Send policy:

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

The return value is a Policy HASH.

Send MPT:

.. code::

   return web3.cloak.sendMultiPartyTransaction({
      account: account,
      params: {
          nonce: <NONCE>,
          to: <PRIVATE CONTRACT ADDRESS OR MPT ID>,
          data: <CALL DATA JSON>
      }
   })

* ``nonce``: same as Ethereum nonce.
* ``to``: if ``to`` is private contract address, that means to propose an MPT transaction, otherwise, that means to participate an MPT(which id is `<MPT ID>`).
* ``data``: it includes the function what you want to call and input arguments, it looks like:

  .. code::
    
    {
        "function": "getSum",
        "inputs" : [
            { "name": "_a", "value": "100"},
            { "name": "_b", "value": "201"}
        ]
    }

Executed MPT will not get the result immediately, it will return an id of that MPT regardless of proposing or participating.
You need to call ``getMultiPartyTransaction()`` to check the MPT status and the result.

Get MPT:

.. code::

   web3.cloak.getMultiPartyTransaction({id: <MPT ID>})
