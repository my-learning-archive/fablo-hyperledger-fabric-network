# Simple Hyperledger Fabric Network With Fablo

This was a small project to learn how to create a local Hyperledger Fabric network for development/testing, using [Fablo](https://github.com/hyperledger-labs/fablo).

**Requirements:**
- Docker 
- Docker Compose
- NodeJS and Go
- Fablo

---
Before starting, here is what each directory in this repository contains:
- `./chaincode` - the chaincode implementing the **smart contract** of our application
- `./sample` - our application, which will interact with the Hyperledger Fabric
- `fablo-config.json` - the configuration file for Fablo ~ where the Orgs, Peers, Channels, Chaincode, etc are defined.

---

To test if Docker is installed, run the hello-world image from Dockerhub:

> `docker run hello-world`

To install Fablo:

> `sudo curl -Lf https://github.com/hyperledger-labs/fablo/releases/download/1.1.0/fablo.sh -o /usr/local/bin/fablo && sudo chmod +x /usr/local/bin/fablo`

To generate the files needed by Fablo to spin the local Hyperledger Fabric network, run the following command in the directory where the `fablo-config.json` file is located (in this case, the root directory of the project):

> `fablo generate`

The previous command creates the `./fabric-target/` folder, where all configuration files go, including the scripts to create the network, the *ConnectionProfiles*, the *MSPs* and all cryptographic material.

**NOTE:** If you are using Docker Compose V2, you are going to have to alter the generated `fabric-target/fabric-docker/commands-generated.sh` file to use the `docker compose` command instead of `docker-compose`.

To spin the Hyperledger Fabric network:

> `fablo up`

The Hyperledger Fabric network is ready to be used! So, let's test it!

Let's start with the preparation of the environment:

> `cd ./sample/javascript-generalized/` \
> `npm install`

Let's enroll the admin *registrar*:

> **`node enrollUser.js <label> <username> <secret> <options>`** \
> `node enrollUser.js 'CAAdmin@org1.example.com' admin adminpw`

Now, we can use our registrar to allow the registration of a new regular user, with the extra option of having a secret, specified in JSON format:

> **`node registerUser.js <registrar-label> <new-uname> <options>`** \
> `node registerUser.js 'CAAdmin@org1.example.com' 'User2@org1.example.com' '{"secret":"userpw"}'`

Now that we have registered a user, we can enroll that user - pull its credentials onto our wallet, so we can act on his behalf:

> `node enrollUser.js 'User2@org1.example.com' 'User2@org1.example.com' userpw`

Let us test the submission of a write and a read transactions, on behalf of `User1@org1.example.com`. The arguments to the transaction are specified in JSON format, with the tag "args". To submit transactions to the distributed ledger on behalf of a user:

> **`node submitAnyTransaction.js <user-label> <function-name> <arguments>`** \
> `node submitAnyTransaction.js 'User2@org1.example.com' writeData '{"args":["Trade","trade1","value1"]}'` \
> `node submitAnyTransaction.js 'User2@org1.example.com' readData '{"args":["Trade","trade1"]}'` \
> `...`