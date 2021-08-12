=============================
Introduction
=============================


-------------------------------
What is Cloak?
-------------------------------

Cloak a pluggable and configurable framework for developing 
and deploying confidential smart contracts. The key capability 
of Cloak is to allow developers to implement and deploy 
practical solutions to *multi-party transaction* (MPT) problems, 
i.e., to transact with secret inputs and states owned by different 
parties, by simply *specifying* it. 
To this end, Cloak allows users to specify privacy invariants in a 
declarative way, automatically generate runtime with verifiably 
enforced privacy and deploy it to the existing platforms 
with TEE-Blockchain architecture, enabling the MPT. 

Additionally, we identify the pitfalls in achieving MPT, and provide 
the treat, i.e., non-deterministic negotiation and fair 
publication of MPT results. In our evaluation on both 
examples and real-world applications, developers manage 
to deploy business services on blockchain in a concise 
manner by only developing Cloak smart contracts, whose 
size is less than 30% of the deployed ones, and the gas cost 
of deployed MPTs reduced by 19%. 

Cloak is an ongoing project aimting to become a chain-agnostic 
privacy infrasturcture of the blockchain ecology. We are always calling for
talented, self-motivated developers, reserachers or students
who are excited about our vision. Let us make it together.

------------------
Cloak Overview
------------------

Cloak framework is designed to work with *TEE* to enable confidential smart contract supporting MPT on the blockchain. 
The figure followed shows the workflow of Cloak. It is mainly divided into three phases, 
*development*, *deployment* and *transaction*. 

.. image:: ../imgs/framework.*
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

------------------
Advanced Features
------------------

------------------------
Call for Contribution
------------------------

