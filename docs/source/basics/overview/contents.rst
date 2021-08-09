Cloak Overview
===============

Cloak framework is designed to work with *TEE* to enable confidential smart contract supporting MPT on the blockchain. 
The figure followed shows the workflow of Cloak. It is mainly divided into three phases, 
*development*, *deployment* and *transaction*. 

.. image:: ../../imgs/framework.*
    :alt: The overall workflow of  CLOAK
    :align: center

In the *development* phase, we provide a domain-specific annotation 
language for developers to express privacy invariants. 
Developers can annotate privacy invariants in a Solidity smart 
contract intuitively to get a Cloak smart contract. 
The core of the development phase is *Cloak Engine*, it checks the correctness and 
consistency of the privacy invariants annotation, then generates the *verifier 
contract*, *private contract*, and the *privacy policy*. 

In the *deployment* phase, Cloak helps developers deploy generated code to specified 
blockchain and Cloak network, where the verifier contract is deployed to the blockchain, 
the private contract and privacy policy is deployed to Cloak network and the transaction 
class is held in Cloak SDK.  

In the *transaction* phase, users use transaction class of Cloak SDK. to interact 
with the blockchain and Cloak Network to send MPT transactions.

