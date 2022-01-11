# Partior  

Soft fork of the ethreum base code .

<h2>Primary Features</h2>

* Privacy - Transcation and smart contract on the blockchain can be private
* Voting based consesum mechanism - Raft based and istanbul byzantine fault tolerence (IBFT) consensus mechanism
* Peer/node permissioning using smart contracts - ensures that only known parties can join network
* Increased scalibility and network performance

<h2>Basic Architecture</h2>  

![images/basic_quorum_architecture.jpeg](images/basic_quorum_architecture.jpeg)

* Quorum-node- Soft-fork of geth
* Constellation- Implemets privacy features of Quorum

  * - Part1 - Transcation manager which is responsible for transcation privacy
  * - Part2 - siloed encryption/decryption managent -HSM
