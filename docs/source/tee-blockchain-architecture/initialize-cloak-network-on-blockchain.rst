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

First of all, you need a CCF-0.15.2 environment, see: `CCF Development Setup <https://microsoft.github.io/CCF/main/build_apps/build_setup.html>`__, but it is complicated, we recommend you use Dcoker to setup it:

.. code-block::

   docker pull plytools/circleci-cloak-tee:v0.2.0

Get cloak-tee code in Docker:

.. code-block::

    git clone --recurse-submodules https://github.com/OxHainan/cloak-tee.git
    cd cloak-tee

Build it:

.. code-block::

    mkdir build && cd build
    cmake .. -GNinja
    ninja

Setup Cloak Service
**********************
After building, the next steps are to deploy CloakService contracts, run cloak-tee, cloak-tee-agent and prepare cloak-tee, you can use Cloak manager script to complete it.

The Cloak manager directory is CLOAK-TEE-PROJECT/agent, install dependencies:

.. code::

   cd ../agent
   pip install -r requirements.txt

Setup Cloak Network:

.. code::

   python cloak.py setup --build-path <CLOAK-TEE BUILD PATH> --blockchain-http-uri <BLOCKCHAIN-HTTP-URI>

The `build-path` option is the path where you build cloak-tee.

If you want to develop or test cloak-tee, ganache-cli may be a good choice as a blockchain backend, after installed ganache and started it, The `--blockchain-http-uri` option should be `http://127.0.0.1:8545`

After deploying CloakService contracts, the script will output the addresses of them, they are useful for compiling cloak contracts, and if you don't want to deploy them again when you restart Cloak Network, you can use the options `--cloak-service-address` to set your deployed contracts.
