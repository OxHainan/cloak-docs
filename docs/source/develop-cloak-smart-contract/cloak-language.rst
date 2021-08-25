=================
Cloak Language
=================

Cloak has developed its domain-specific language (DSL) to conveniently define privacy data and privacy policies. Considering the practicability of smart contracts, Cloak is currently developed based on Solidity. Cloak language is a domain-specific annotation language consisting of (memory) locations, data types, privacy types, expressions, statements, functions, and contracts.

-------------
Locations
-------------
Location (L) consists of contract field identifiers, function arguments, local variables and mapping entries. For example:

.. code-block ::

   contract Demo { // contract field identifier

    final address _owner; // local variables
    mapping(address => uint256) pubBalances; // mapping entries
    	function pubBalanceOf(address owner) public view returns (uint256) { // function arguments
    	    return pubBalances[owner];
    	}
    }

-------------
Data Types
-------------
Data types (τ) define the common data structures in Cloak, including:

===========   ========
Type          Key
===========   ======== 
Boolean       bool
Integer       uint256
Addresses     address
Binary data   bin
Mappings      mapping
===========   ========

* Address, is used to indicate the address of users.

* Binary data, is used to capture signatures, public keys and ciphertexts.

-------------
Privacy Types
-------------
Privacy types (α) define the data access rights, including ``me``, ``all``, ``tee`` and identifiers.

* ``me``, indicates that only the runtime address of the caller can obtain its plaintext.

* ``all``, indicates that all users can obtain its plaintext.

* ``tee``, indicates that only the TEE can obtain its plaintext.

* identifiers, depends on the specific business to decide who is able to obtain the plaintext.

For example:

.. code-block::

   uint256@me x; // here, only the runtime address of the caller can obtain the plaintext of x.
   uint256@alice y; // here, only the Alice can obtain the plaintext of y.

------------------
Type Declarations
------------------
Type declaration consisting of Data type and Privacy type is used to specify the owner of a construct, the usage is :

.. code-block:: 

   data type @ privacy type

For example:

.. code-block:: 

   final address@all _manager; // all
   mapping(address => uint256) pubBalances; // public
   mapping(address!x => uint256@x) priBalances; // private
   uint256@tee _totalSupply;  // tee

-------------
Expressions
-------------
The only Cloak-specific expressions (e) consist of the runtime address of the caller (``me``), common address of TEE (``tee``), reclassification of information (``reveal()``) and native functions including standard arithmetic and boolean operators.

**reveal()** is a operation that is able to change the access right. For example, one can reveals the value x to Alice:

.. code-block ::

   uint256@me x; // here, Alice cannot obtain the plaintext of x.
   uint256@alice x_alice;
   x_alice = reveal(x, alice); //here, Alice is able to obtain the plaintext of x.

Of course, it can be public:

.. code-block ::

   uint256 x_public;
   x_public = reveal(x, all);
   
-------------
Statements
-------------
Statements (P) are the smallest executable unit in a program. A statement can complete a basic operation. Statements in Cloak can be divided into the following forms:

.. code-block:: 
   
   skip // to skip some operation
   uint256@x balance //declaration of data type uint256 with privacy type private for identified balance
   L = e // assignment statement
   P1; P2 // semicolon is used between two statements
   require(e) // error handling statement
   if e {P1} else {P2} // judgment statement
   while e {P} // circular statement


-------------
Functions
-------------
Function (F) is a group of statements that perform a task together. The general form of a function definition is as follows:

.. code-block::

   F ::= function f(τ_1@α_1 id_1, . . . , τ_n@α_n id_n) returns τ@α {P; return e; }
       
You can refer to specific examples to correspond to the formal description:

.. code-block::
   
    function pubBalanceOf(address owner) public view returns (uint256) {
        return pubBalances[owner];
    }

It is noteworthy that the function in Cloak also has privacy types according to its data privacy types.
Typically, there are three function types.

* PUB, public, iff all data privacy types are **@all**.

* CT, confidential transaction, iff only one private expression exists but is not owned by TEE.

* MPT, multi-party transaction, iff one involves variables from different parties.

------------------------
A Simple Cloak contract
------------------------
Cloak contract is similar to a traditional solidity smart contract. Let us begin with a simple example that supports Multi-Party Transaction(MPT) with different privacy policies. It is fine if you do not understand everything right now, we will go into more detail later.


.. code-block:: 

   // SPDX-License-Identifier: Apache-2.0

   pragma cloak ^0.2.0;

   contract Demo {

       final address _owner;
       final address@all _manager; // all

       mapping(address => uint256) pubBalances; // public

       mapping(address!x => uint256@x) priBalances; // private

       uint256@tee _totalSupply;  // tee

       constructor(address manager) public {
           _owner = me;
           _manager = manager;
       }

       /** PUB
        *
        * @dev Gets the public balance of the specified address.
        * @param owner The address to query the balance of.
        * @return An uint256 representing the amount owned by the passed address.
        */
       function pubBalanceOf(address owner) public view returns (uint256) {
           return pubBalances[owner];
       }
   
       /** PUB
        *
        * @dev Transfer token for a specified address
        * @param to The address to transfer to.
        * @param value The amount to be transferred.
        */
       function transfer(address to, uint256 value) public returns (bool) {
           require(value <= pubBalances[me]);
           require(to != address(0));
   
           pubBalances[me] = pubBalanceOf(me) - value;
           pubBalances[to] = pubBalanceOf(to) + value;
           return true;
       }
   
       /** CT-me
        *
        * @dev Deposit token from public to private balances
        * @param value The amount to be deposited.
        */
       function deposit(uint256 value) public returns (bool) {
           require(value <= pubBalances[me]);
   
           pubBalances[me] = pubBalances[me] - value;
           priBalances[me] = priBalances[me] + value;
           return true;
       }
   
       /** CT-owner; change ownership; return private data;
        *
        * @dev Gets the public balance of the specified address.
        * @param owner The address to query the balance of.
        * @return An uint256 representing the amount owned by the passed address.
        */
       function totalSupply() public view returns (uint256@_manager) {
           uint256@_manager ts = reveal(_totalSupply, _manager);
           return ts;
       }
   
       /** MPT
        *
        * @dev Transfer token for a specified address
        * @param to The address to transfer to.
        * @param value The amount to be transferred.
        */
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
   
       /** MPT: 2 parties (party0 != party1)
        *
        * @dev Is party0 richer than party1
        * @param party0 address The first address for comparison
        * @param party1 address The second address for comparison
        */
       function compare(address party0, address party1) internal returns (bool) {
           return priBalances[party0] > priBalances[party1];
       }
   
       /** MPT: 2 parties (me != target); function call
        *
        * @dev Is me richer than the target account
        * @param target address The address which you want compare with
        */
       function isRicher(address target) public returns (bool) {
           return compare(me, target);
       }
   
       /** MPT: 5 parties(from, to, me, _owner, _manager)
        *
        * @dev Transfer tokens from one address to another
        * @param from address The address which you want to send tokens from
        * @param to address The address which you want to transfer to
        * @param value uint256 the amount of tokens to be transferred
        */
       function multiPartyVoteTransfer(
           address from,
           address to,
           uint256@me value,
           bool@_owner ownerVote,
           bool@_manager managerVote
       ) public returns (bool) {
           if (ownerVote || managerVote) {
               require(value <= priBalances[from]);
               require(to != address(0));
   
               priBalances[from] = priBalances[from] - value;
               priBalances[to] = priBalances[to] + value;
           }
   
           return true;
       }
   }   
   

The first line tells you that the source code is licensed under the Apache version 2.0.
The next line specifies that the source code is written for Cloak version 0.2.0.

.. note::

   Cloak is based on Solidity, so it is convenient for Solidity programmers, but it should be noted that the second line is the version of Cloak rather than solidity! Because Cloak has its own underlying compilation environment, which is different from solidity.
   
Most of the syntax is consistent with solidity, the difference lies in the privacy policy. 

The line ``final address _owner;`` declares a state variable of type ``address``.  ``final`` is a keyword of `zkay <https://eth-sri.github.io/zkay/language.html>`_, meaning that they can only be assigned once in the constructor. 
The line ``final address@all _manager;`` declares a state variable that everyone can learn its plaintext. 
The line ``mapping(address!x => uint256@x) priBalances; // private`` shows a private privacy policy that the only ``x`` is able to obtain the plaintext. 
Analogously, ``uint256@tee _totalSupply;  // tee`` assigns the access right to TEE.
    
.. code-block::
   
   function pubBalanceOf(address owner) public view returns (uint256) {
        return pubBalances[owner];
    }
    
The function ``pubBalanceOf(address owner)`` is public to return the owner's pubBalance.
Labelled with the ``view``, it cannot change any variable, so it is safe to be public.

.. code-block::

   function transfer(address to, uint256 value) public returns (bool) {
        require(value <= pubBalances[me]);
        require(to != address(0));

        pubBalances[me] = pubBalanceOf(me) - value;
        pubBalances[to] = pubBalanceOf(to) + value;
        return true;
    }
    
In function ``transfer()``, the ``value`` of ``me`` was transferred to ``pubBalance[to]``.
These two ``require()`` ensures that the security of variables. Users need to use this function to conduct transactions, so it is public too.

.. code-block::

   function deposit(uint256 value) public returns (bool) {
        require(value <= pubBalances[me]);

        pubBalances[me] = pubBalances[me] - value;
        priBalances[me] = priBalances[me] + value;
        return true;
    }
    
Function ``deposit()`` is a CT function, because the variable ``priBalances`` is a private type but does not belong to TEE.

.. code-block::
   
   function totalSupply() public view returns (uint256@_manager) {
        uint256@_manager ts = reveal(_totalSupply, _manager);
        return ts;
    }
    
Function ``totalSupply()`` reveals the ``_totalSupply`` to _manager. Note that, ``ts`` is also private data for others.


.. code-block::
   
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
    
This function is an MPT function, it is very similar to ``transfer()``. The difference lies in the mapping variables ``priBalances[]``, typed with ``@x``.

.. code-block::

   function compare(address party0, address party1) internal returns (bool) {
        return priBalances[party0] > priBalances[party1];
   }
   function isRicher(address target) public returns (bool) {
           return compare(me, target);
   }
    
Similarly, functions ``compare()`` and ``isRicher()`` are also MPT functions due to the private type of ``priBalance[]``.

.. code-block::

   function multiPartyVoteTransfer(
           address from,
           address to,
           uint256@me value,
           bool@_owner ownerVote,
           bool@_manager managerVote
       ) public returns (bool) {
           if (ownerVote || managerVote) {
               require(value <= priBalances[from]);
               require(to != address(0));
   
               priBalances[from] = priBalances[from] - value;
               priBalances[to] = priBalances[to] + value;
           }
   
           return true;
       }

This is a conditional transfer, there private parameters are required.
