=============================
Cloak Compiler
=============================


--------------------
Compile
--------------------

In `Quick Start <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/quick-start.html>`_, the basic usage is shown with command.

.. code-block ::

    python cloak/__main__.py compile -o output --blockchain-service-address <CLOAK SERVICE ADDRESS> test/demo.cloak
   

Here, we will briefly show you how the cloak-compiler works inside.


In the `development phase <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/started/introduction.html#cloak-overview>`_, developers first annotate privacy invariants in Solidity smart contract intuitively to get Cloak smart contract and use the cloak-complier to compile the Cloak contract. 

The components of cloak-compiler works as following:

*  **Annotation Checker**, checks the annotation to make sure the privacy invariants are correct.
*  **Policy Generator**, generates *privacy policy*.
*  **Code Generator**, generates *verifier contract (aka public contract)* and *private contract*.


For more detail, we recommend the `Publications <https://oxhainan-cloak-docs.readthedocs-hosted.com/en/latest/publications/publications.html>`_.

Annotate Privacy Invariants
==============================
Developers could annotate variable owner in the declaration statement to one of the {``all``, ``me``, ``id``, ``tee``}.

* ``all``, means public;

*  ``me``, means the ``msg.sender``;

* ``id``, is declared variable in type address;

* ``tee``, means any registered address of SGX with Cloak runtime.

With Cloak, users could intuitively specify the MPT as a Cloak smart contract, the *.cloak* file shows in the following contract, aka the *demo.cloak*.


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

* In line 6, the developer specifies ``_manager`` should be public. 
* In line 7, the developer specifies a public mapping. 
* In line 8, the developer specifies a private mapping with ``!x`` and ``@x``.
* In lines 20-25, the developer defines a Confidential Transaction (CT) that enables one user to transfer public balance into private balance.
* In lines 33-43, the developer defines a Multi-Party Transaction (MPT) that enables one user (``me``) to transfer private balance to another user (``to``).


Annotation Checker
====================
Taking a Cloak smart contract, Cloak ignores the annotation to checks the Solidity validation first.
Then, Cloak checks privacy invariants consistency.
For example, Cloak prohibits developers from implicitly assigning their private data to variables owned by others.


Policy Generator
====================
After passing the check of Annotation Checker, Policy Generator generates a privacy policy :math:`P` for the contract.
:math:`P` simplifies and characterizes the privacy invariants. Typically, :math:`P` includes variables with data type and owners. It also includes ABI, a read-write set of each function.
Specifically, :math:`P` records each function’s characteristics from four aspects, *inputs*, *read*, *mutate* and *return*. The *inputs* includes its parameters with specified data type and owner; *read* records state variables the function read in execution; *mutate* records the contract states it mutated; *return* records the return variables.
Since :math:`P` has recorded the details of state variables in the head, *e.g.*, data type and owner, **Policy Generator** leaves the variable identities in *read*, *mutate* and *return*.

The generated privacy policy is as follows:

.. code-block::

   {
     "contract": "Demo",
     "states": [
       {
         "name": "_manager",
         "type": "address",
         "owner": "all"
       },
       {
         "name": "pubBalances",
         "type": "mapping(address => uint256)",
         "owner": "all"
       },
       {
         "name": "priBalances",
         "type": "mapping(address => uint256)",
         "owner": "mapping(address!x => uint256@x)"
       }
     ],
     "functions": [
       {
         "type": "function",
         "name": "constructor",
         "privacy": 0,
         "inputs": [
           {
             "name": "manager",
             "type": "address",
             "owner": "all"
           }
         ],
         "mutate": [
           {
             "name": "_manager"
           },
           {
             "name": "pubBalances",
             "keys": [
               "manager"
             ]
           }   
         ],
         "entry": "0xf8a6c595"
       },
       {
         "type": "function",
         "name": "deposit",
         "privacy": 3,
         "inputs": [
           {
             "name": "value",
             "type": "uint256",
             "owner": "all"
           }
         ],
         "read": [
           {
             "name": "pubBalances",
             "keys": [
                "msg.sender"
             ]
           },
           {
             "name": "priBalances",
             "keys": [
               "msg.sender"
             ]
           }
         ],
         "mutate": [
           {
             "name": "pubBalances",
             "keys": [
               "msg.sender"
             ]
           },
           {
             "name": "priBalances",
             "keys": [
               "msg.sender"
             ]
           }
         ],
         "outputs": [
           {
             "type": "bool",
             "owner": "all"
           }
         ],
         "entry": "0xb6b55f25"
       },
       {
         "type": "function",
         "name": "multiPartyTransfer",
         "privacy": 3,
         "inputs": [
           {
             "name": "to",
             "type": "address",
             "owner": "all"
           },
           {
             "name": "value",
             "type": "uint256",
             "owner": "all"
           }
         ],
         "read": [
           {
             "name": "priBalances",
             "keys": [
               "msg.sender",
               "to"
             ]
           }
         ],
         "mutate": [
           {
             "name": "priBalances",
             "keys": [
               "msg.sender",
               "to"
             ]
           }
         ],
         "outputs": [
           {
             "type": "bool",
             "owner": "all"
           }
         ],
         "entry": "0x821cdc5b"
       }
     ]
   }

* contract, indicates the name of the confidential smart contract.

* states 

    States records all types of contract data state variables, The meaning of the ``owner`` field is

    * ``owner: "all"`` is defaults value, means that anyone can query the data and store it on Block Chain in plaintext.

    * ``owner: id``, means that the owner of data is ``id``, ``id`` type is ``address``. 
      Only user has verified the identity of the ``id`` (e.g., digital signature) can be allowed to read the data. 
      Therefore, the value of data is private and crypted it before export Cloak (e.g., synchronized data to Blockchain).

    * ``owner: "mapping(address!x=>uint256@x)``, statement of the mapping ``key`` is temporary variable ``x``, 
      and flag the owner of ``value`` is ``x``. the same as ``id``.

    .. note ::

        Temporary variable ``x`` is only valid in the mapping declaration, e.g., in a contract, 
        allow ``mapping(address!x => uint256@x)`` and ``mapping(address!x => mapping(address => uint256@x))`` can be valid 
        at the same time, because the scope of ``x`` is limited to their respective mapping.

* functions

    functions is an array collection, mark the inputs and outputs expressions of a single function, as shown below

    * ``name``, is a name of function

    * ``inputs``, input parameters of the function, each input contains the variable ``name``, ``type``, and ``owner`` of the parameter

    * ``read``, record the name of the contract data state variable required in current function contract code, in order to synchronize data
      with Block Chain.

    * ``mutate``, the contract data state binding relationship of owner of data ``id`` in this function.

    * ``outputs``, output function execution result in EVM.


Code Generator
====================
**Code Generator** generates a verifier contract :math:`V` and a private contract :math:`F`.
The former is deployed in the blockchain to verify the result and update the state.
The latter is deployed in the TEE to execute confidential transaction (CT) and  Multi-Party Transaction (MPT). In our implementation, we use the SGX to build a trusted execution environment.


The generated public contract is as follows:

.. code-block::

   pragma solidity ^0.8.0;

   import "./CloakService.sol";

   contract Demo {

       // Helper Contracts
       CloakService public constant CloakService_inst = CloakService(0);

       // TEE helper variables
       uint public constant teeCHash = 33184773818284367035659484839640936095181433820508061007086907661336906690385;
       uint public constant teePHash = 95421834508635786258380600414803568343321044037425343624422990737583510413960;
       address public tee = CloakService_inst.getTEEAddress();

       // User state variables
       address public _manager;
       mapping(address => uint256) public pubBalances;
       mapping(address/*!x*/ => uint[3]/*uint256@x*/) public priBalances;

       constructor(address manager) public {
           _manager = manager;
           pubBalances[manager] = 1000;
       }

       function get_states(uint256[] memory read, uint return_len) public returns (uint256[] memory ) {
           uint256[] memory oldStates = new uint256[](return_len);
           oldStates[0] = 0;
           oldStates[1] = uint(uint160(_manager));
           uint m_idx = 0;
           uint o_idx = 2;
           oldStates[o_idx] = read[m_idx];
           oldStates[o_idx + 1] = read[m_idx + 1];
           for (uint i = 0; i < read[m_idx + 1]; i = i + 1) {
               oldStates[o_idx + 2 + i * 2] = read[m_idx + 2 + i];
               oldStates[o_idx + 3 + i * 2] = uint(pubBalances[address(uint160(read[m_idx + 2 + i]))]);
           }
           o_idx = o_idx + 2 + read[m_idx + 1] * 2;
           m_idx = m_idx + 2 + read[m_idx + 1];
           oldStates[o_idx] = read[m_idx];
           oldStates[o_idx + 1] = read[m_idx + 1];
           for (uint i = 0; i < read[m_idx + 1]; i = i + 1) {
               oldStates[o_idx + 2 + i * 4] = read[m_idx + 2 + i];
               oldStates[o_idx + 3 + i * 4] = priBalances[address(uint160(read[m_idx + 2 + i]))][0];
               oldStates[o_idx + 4 + i * 4] = priBalances[address(uint160(read[m_idx + 2 + i]))][1];
               oldStates[o_idx + 5 + i * 4] = priBalances[address(uint160(read[m_idx + 2 + i]))][2];
           }
           return oldStates;
       }

       function set_states(uint256[] memory read, uint old_states_len, uint256[] memory data, uint[] memory proof) public {
           require(msg.sender == tee, 'msg.sender is not tee');
           uint256 osHash = uint256(keccak256(abi.encode(get_states(read, old_states_len))));
           if (!CloakService_inst.verify(proof, teeCHash, teePHash, osHash)) {
               revert('hash error');
           }
           _manager = address(uint160(data[1]));
           uint m_idx = 2;
           for (uint i = 0; i < data[m_idx + 1]; i = i + 1) {
               pubBalances[address(uint160(data[m_idx + 2 + i * 2]))] = data[m_idx + 3 + i * 2];
           }
           m_idx = m_idx + 2 + data[m_idx + 1] * 2;
           for (uint i = 0; i < data[m_idx + 1]; i = i + 1) {
               priBalances[address(uint160(data[m_idx + 2 + i * 4]))][0] = data[m_idx + 3 + i * 4];
               priBalances[address(uint160(data[m_idx + 2 + i * 4]))][1] = data[m_idx + 4 + i * 4];
               priBalances[address(uint160(data[m_idx + 2 + i * 4]))][2] = data[m_idx + 5 + i * 4];
           }
       }
   }



* In line 1, it is an obvious statement to indecate it is a Solidity contract.
* In lines 9, ``CloakService`` is a service contract of cloak-tee. ``teeCHash`` and ``teePHash`` are parameters to verify the proof in line 55.
* In lines 13-15, there are three TEE helper variables. 
* In lines 27-50, the function ``get_states`` calculates and returns the old states.
* In lines 52-69, the function ``set_states`` receives the parameters from TEE and set the new states.


The generated private contract is as follows:

.. code-block::

   pragma solidity ^0.8.0;

   contract Demo {
       address _manager;
       mapping(address => uint256) pubBalances;
       mapping(address => uint256) priBalances;

        constructor(address manager) public {
           _manager = manager;
           pubBalances[manager] = 1000;
       }

       function deposit(uint256 value) public returns (bool) {
           require(value <= pubBalances[msg.sender]);
           pubBalances[msg.sender] = pubBalances[msg.sender] - value;
           priBalances[msg.sender] = priBalances[msg.sender] + value;
           return true;
       }

       function multiPartyTransfer(address to, uint256 value) public returns (bool) {
           require(value <= priBalances[msg.sender]);
           require(to != address(0));
           priBalances[msg.sender] = priBalances[msg.sender] - value;
           priBalances[to] = priBalances[to] + value;
           return true;
        }

       function get_states(uint256[] memory read, uint return_len) public returns (uint256[] memory ) {
           uint256[] memory oldStates = new uint256[](return_len);
           oldStates[0] = 0;
           oldStates[1] = uint(uint160(_manager));
           uint m_idx = 0;
           uint o_idx = 2;
           oldStates[o_idx] = read[m_idx];
           oldStates[o_idx + 1] = read[m_idx + 1];
           for (uint i = 0; i < read[m_idx + 1]; i = i + 1) {
               oldStates[o_idx + 2 + i * 2] = read[m_idx + 2 + i];
               oldStates[o_idx + 3 + i * 2] = uint(pubBalances[address(uint160(read[m_idx + 2 + i]))]);
           }
           o_idx = o_idx + 2 + read[m_idx + 1] * 2;
           m_idx = m_idx + 2 + read[m_idx + 1];
           oldStates[o_idx] = read[m_idx];
           oldStates[o_idx + 1] = read[m_idx + 1];
           for (uint i = 0; i < read[m_idx + 1]; i = i + 1) {
               oldStates[o_idx + 2 + i * 2] = read[m_idx + 2 + i];
               oldStates[o_idx + 3 + i * 2] = uint(priBalances[address(uint160(read[m_idx + 2 + i]))]);
           }
           return oldStates;
       }

       function set_states(uint256[] memory data) public {
           _manager = address(uint160(data[1]));
           uint m_idx = 2;
           for (uint i = 0; i < data[m_idx + 1]; i = i + 1) {
               pubBalances[address(uint160(data[m_idx + 2 + i * 2]))] = data[m_idx + 3 + i * 2];
           }
           m_idx = m_idx + 2 + data[m_idx + 1] * 2;
           for (uint i = 0; i < data[m_idx + 1]; i = i + 1) {
               priBalances[address(uint160(data[m_idx + 2 + i * 2]))] = data[m_idx + 3 + i * 2];
           }
        }
   }
   
   
   
* In line 1, it is an obvious statement to indecate it is a Solidity contract, too. However, it is running in the SGX-enabled EVM rather than a normal EVM.
* In lines 4-6, these variables become normal variables without annotation.
* In lines 13-18, function ``deposit()`` works like a normal function.
* In lines 20-26, function ``multiPartyTransfer()``  replaces the ``me`` with ``msg.sender``.
* In lines 28-49, function ``get_states()`` calculates and returns the old states.
* In lines 51-61, function ``set_states()`` receives the oldstates from blockchain and set the values of variables (pubBalances, priBalances).


--------------------
Debug
--------------------


