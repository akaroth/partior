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
* Tessera(Privacy Manager) - Implemets privacy features of Quorum

The quorum node has the following feature

* PoW consensus has been replaced with other consesus protocol like IBFT,raft or QBFT. (_In the GoQuorum PoA consensus protocols, a group of nodes in the network act as validators (IBFT and QBFT), verifiers (Raft) or signers (Clique). Existing validators, verifiers, or signers vote to add or remove network nodes._)
* The P2P layer allows only permissioned nodes. 
* Gas pricing is removed. But the concept of gas still remains.
* The state patricia trie is split into 2 . the public and the private trie

<h2> Security on AWS</h2>

Ground Rules 
Below are ground rules to enforce - 
* Minimize your exposure - DO NOT expose unnecessary networking or instances on the public internet. 
* Implement the zero-trust with MFA enablement on all tools and production systems including AWS account, DevOps Tools (Jenkins), Bastion Servers and etc. 
* Secure your credentials with KMS backed automated solutions - database username, password, sensitive API keys and wallet key pairs are suggested to store on Key Management Service (KMS) backed solutions. Applications need to integrate the SDK to automate this process. 
* Grant least privilege to your users - never grant unnecessary permissions to your users or applications. 
* Manage your Infrastructure as Code -  Manage the infrastructure, configuration as code to automate and make implementations consistent with designs. 

DevOps Guidelines and Best Practices
Below are guidelines you need to follow based on the current system design and security rules. 
<h4>Network Design:</h4>

* IP CIDR Plan - Pick a vpc cidr that is not overlapped with existing network.
* Cross-AZ High Availability - The basic subnet design should have three types subnet across all availability zones. Most aws regions have at least three availability zones.
* Three public subnet which has default route point to igw
* Three nat subnet which has default route point to natgw. You need to put one natgw in each of availability zone
* Three private subnet which has no default route
* You can add more subnets just for management purposes when necessary, but the type will always be three. For example, you can create different subnets for internal alb, redis, rds, but they should bind to the same route table which has no default route which you can create different subnets for ec2 and ecs, but they should bind to the same nat route table.
* Routing Table Design - No matter what kind of vpc you are using, 5 route table is enough and has best practice for 3 availability zone region
* One public route table which has a default route to igw. It can be bound to the public ec2 subnet, external ALB subnet, etc.
* One private route table which has no default route. It can be bound to redis subnet, rds subnet, etc.
* One nat route table which is the default route to natgw for each availability zone. It can be bound to nat ec2 subnet, nat ecs subnet, etc.
* Minimize exposure with public subnet design - Although you can have public subnet point to igw, usually you will only put external alb in this subnet. Use the following ways if you want to put ec2 in a public subnet.
* For ssh access: using aws session manager instead
* Open service for public access: using external alb and put ec2 behind it
* EIP will only need to be bind to natgw, ec2 and rds usually doesn’t need to bind public ip
* Security Group - Create a different security group for each of aws services such as ec2/alb/ecs/rds/redis etc even some of the resources may have the same security group rule. And just open specific and source with least privilege
* Use terraform to design and manage your networking - If you can join binance terraform enterprise, we have vpc module to launch above best practice network at one click
* Networking Topology - For external system, you should use cloudfront->external alb->services arch, enable aws shield and waf on cloudfront and alb
* S3 Bucket Access - For S3 bucket, never set s3 bucket as public access. Always put cloudfront in front of s3 for public access content such as static js file.

<h4>AWS Account and IAM Management:</h4>

* Root account management - Enable 2fa/yubikey for root account. The root account should be locked down and never use it in daily operation. Please transfer the ownership of the root account for centralized management. 
* Terraform IAM Module - Join the binance terraform enterprise. Then you can use the IAM module to create a user or role for each user. If you can’t join binance terraform enterprise, make sure to enable 2fa/yubikey for each of iam user you created and only attach least privilege policy to iam user
* Never create Access & Secret Key pairs on production systems - Never create access key/secret access key, use the following ways instead.
* For ec2/ecs/lambda etc to access aws services, always use role
* For local development, use saml2aws to get temp ak/sk

<h4>DevOps Tool Security</h4>

* Zero-Trust Security on daily DevOps - For internal systems that need web access such as jenkins,grafana,kibana,aws,archery, etc, you need to integrate with okta. Whitelist vpn ip in security group and build private connection between vpc and binance vpn.
* Bastion Server v.s. AWS Session Manager - AWS Session Manager usually provides better security over open source or commercial bastion servers. If you need to have a dedicated bastion server solution, please reach DevSecOps team for discussions.

<h4>Sensitive credential management and integrations</h4>

* Credentials such as database access credentials, wallet key, api key should be kept in AWS secrets manager (KMS backed), where applications need to integrate AWS SDK for access.  Note that we usually need to separate the access credentials for applications from manual operations - like database management.


<h4>Database Management</h4>

* Centralized database management of the DBA team is suggested for production systems.  Development team can leverage “Archery” for database access with necessary permission granted.  

* Enable system audit logs 
* Enable cloudtrail and keep the log in aws s3
* Enable aws session manager log and keep it in aws s3
* Enable cloudfront and ALB log and keep them in aws s3


<h4>AWS Security Services & Account Management</h4>

* Turn on GuardDuty, and Access Analyzer for all accounts to send their logs and alerts to the Security tunnel ( webex, pagerduty).
* We usually recommend account separation to isolate the various business systems and production environments from others. For instance, DEV, QA should have a dedicated account from the PROD environment. 
