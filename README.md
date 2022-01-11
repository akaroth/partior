# Quorum Blockchain and DevOps Practice.

<h2> Quorum blockchain</h2>

Soft fork of the ethreum base code .

<h2>Primary Features</h2>

* Privacy - Transcation and smart contract on the blockchain can be private
* Voting based consesum mechanism - Raft based and istanbul byzantine fault tolerence (IBFT) consensus mechanism
* Peer/node permissioning using smart contracts - ensures that only known parties can join network
* Increased scalibility and network performance

<h2>Basic Architecture</h2>  

![images/basic_quorum_architecture.jpeg](images/basic_quorum_architecture.jpeg)

* Quorum-node- Soft-fork of geth
* Constellation(Privacy Manager) - Implemets privacy features of Quorum

The quorum node has the following feature

* PoW consensus has been replaced with other consesus protocol like IBFT,raft or QBFT. (_In the GoQuorum PoA consensus protocols, a group of nodes in the network act as validators (IBFT and QBFT), verifiers (Raft) or signers (Clique). Existing validators, verifiers, or signers vote to add or remove network nodes._)
* The P2P layer allows only permissioned nodes. 
* Gas pricing is removed. But the concept of gas still remains.
* The state patricia trie is split into 2 . the public and the private trie