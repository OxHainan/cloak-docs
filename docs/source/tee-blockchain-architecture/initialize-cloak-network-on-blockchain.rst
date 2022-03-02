=======================================
Initialize Cloak Network on Blockchain
=======================================
Initializing Cloak Network on blockchain is to deploy Cloak Service, including four components:

* **cloak-tee**: the core component, which is a `CCF <https://github.com/microsoft/CCF>`__ app that runs in a TEE environment,
  deals with Ethereum and Cloak transaction from users and synchronizes the results to blockchain.
* **cloak-tee-agent**: as described above, cloak-tee runs in SGX Enclave(TEE), it is inconvenient to 
  communicate with the outside system (blockchain, file system, etc.), so cloak-tee-agent is the untrusted 
  part that reads cloak-tee log file and communicates with outside.
* **CloakService contract**: provide the service to get PK from address for encryption and decryption and some useful functions that include tee address register, 
  verification of synchronization.

Build Cloak-tee
**********************
Cloak-tee is a CCF App, compile and run it just like a standard CFF App operation.

First of all, if you need to configure the cloak-tee environment yourself, see: `CCF Development Setup <https://microsoft.github.io/CCF/main/build_apps/build_setup.html>`__, but it is complicated, we recommend you use Dcoker to setup it:

.. code-block::

   docker pull plytools/cloak-tee:latest

Get cloak-tee code in Docker:

.. code-block::

    git clone --recurse-submodules https://github.com/OxHainan/cloak-tee.git
    cd cloak-tee

Build it:

.. code-block::

    mkdir build && cd build
    cmake .. -GNinja
    ninja -j 4

Setup Cloak Service
**********************
After building, the next steps are to build CloakService contracts, run cloak-tee.

Get cloak service contract:

.. code::

  git clone https://github.com/OxHainan/cloak-service-contract.git

Build it:

.. code-block::

  npm install
  truffle compile
 

If the compilation is successful, you will see the `build` directory. 

Setup Cloak Network:

.. code::
  
  cd build
  ./sandbox.sh -p libcloak.virtual.so --cloakservice-dir <CLOAK SERVICE PAYH> --manager-address <MANAGER ADDRESS> --blockchain-url <BLOCKCHAIN-HTTP-URL>

The `CLOAK SERVICE PAYH` option is the generated path where you compiled the cloak contract, (e.g. <CLOAK SERVICE REPO>/build/contract)

The `MANAGER ADDRESS` option is a person who manager the cloak network, format likes ethereum account address. If you don`t commit it, it will choose the first account of the blockchain backend as the management address by default.

The `BLOCKCHAIN-HTTP-URL` option is the connect to blockchain backend, and default is http://127.0.0.1:8545. If you want to connect aother blockchain backend, please commit it. 

.. Note::

  If you want to develop or test cloak-tee, ganache-cli may be a good choice as a blockchain backend, after installed ganache and started it.

After deploying CloakService contracts, the script will output the addresses of them, they are useful for compiling cloak contracts, and if you don't want to deploy them again when you restart Cloak Network, you can use the options `--cloak-service-address` to set your deployed contracts.
