# ledger-fabric
To initiate the first Hyperledger Fabric network, we will utilize the Fabric-samples repository. This repository consists of CLI tool binaries and Fabric Docker Images, which are essential for comprehending and utilizing the fundamentals of Fabric. Before commencing the project, it is necessary to install the following dependencies:

# 1.Install Docker

    sudo apt-get -y install docker-compose

# 2. Install Golang-go

    sudo apt install golang-go

# 3. Install jq

    sudo apt install jq

# 4. Install Node/java

    sudo apt install npm
    npm install node

# 5. Installing Fabric-samples Repository

    curl -sSLO https://raw.githubusercontent.com/hyperledger/fabric/main/scripts/install-fabric.sh 
    
    sudo chmod +x install-fabric.sh
    
Then you can pull docker containers by running one of these commands:

    ./install-fabric.sh docker samples
    ./install-fabric.sh d s 

To install binaries for Fabric samples you can use the command below:

    ./install-fabric.sh binary
    
# Building First Network

1.Navigate through the Fabric-samples folder and then through the Test network folder where you can find script files using these we can run our network.

    cd fabric-samples/test-network

2.We would be running ./network.sh down command to remove any previous network containers or artifacts that still exist. 

    ./network.sh down

3.  we can bring up a network using the following command. This command creates a fabric network that consists of two peer nodes and one ordering node.

    ./network.sh up
    
# The components of the test network:

Run the below command to list all of Docker containers that are running on your machine:

    sudo docker ps -a
    
Peers are the fundamental components of any Fabric network. Peers store the blockchain ledger and validate transactions before they are committed to the ledger. An ordering 
service allows peers to focus on validating transactions and committing them to the ledger. The sample network uses a single node Raft ordering service that is operated by the
ordering organization.

# Creating a channel

Now that we have peer and orderer nodes running on our machine, we can use the script to create a Fabric channel for transactions between Org1 and Org2. Channels are a private
layer of communication between specific network members. Run the below command to create a channel (default name of "mychannel", can have custom name by adding "-c
channel_name" to the command):

    sudo ./network.sh createChannel

# Starting a chaincode on the channel

After you have created a channel, you can start using smart contracts to interact with the channel ledger. To ensure that transactions are valid, transactions created using 
smart contracts typically need to be signed by multiple organizations to be committed to the channel ledger. you can start a chaincode on the channel using the below command:

    sudo ./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-go -ccl go

The deployCC subcommand will install the fabcar chaincode and then deploy the chaincode on the channel specified("Go" version by default).

# Interacting with the network
After you bring up the test network, you can use the peer CLI to interact with your network. You can find the peer binaries in the bin folder of the fabric-samples repository.
Use the following command to add those binaries to your CLI Path:

    export PATH=${PWD}/../bin:${PWD}:$PATH

You also need to set the FABRIC_CFG_PATH to point to the core.yaml file in the fabric-samples repository:

    export FABRIC_CFG_PATH=$PWD/../config/

You can now set the environment variables that allow you to operate the peer CLI as Org1:

    export CORE_PEER_TLS_ENABLED=true
    export CORE_PEER_LOCALMSPID="Org1MSP"
    export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    export CORE_PEER_ADDRESS=localhost:7051

you can now query the ledger from your CLI. Run the following command to get the list of cars that were added to your channel ledger:

    peer chaincode query -C mychannel -n fabcar -c '{"Args":["queryAllCars"]}'



![252158422-07c16d36-240b-4892-95af-8fa71ddd3515](https://github.com/khushi798/ledger-fabric/assets/120015301/7ed6a621-69e2-4540-854c-10ef6e55ed84)

Since we already queried the Org1 peer, we can take this opportunity to query the chaincode running on the Org2 peer. Set the following environment variables to operate as 
Org2 (Run one by one):

    export CORE_PEER_TLS_ENABLED=true
    
    export CORE_PEER_LOCALMSPID="Org2MSP"
    
    export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
    
    export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    
    export CORE_PEER_ADDRESS=localhost:9051

You can now query the fabcar chaincode running on peer0.org2.example.com:

    peer chaincode query -C mychannel -n fabcar -c '{"Args":["queryCar","CAR9"]}'


![252161113-5a2a1021-90a5-4312-8ba1-d457e8227bb5](https://github.com/khushi798/ledger-fabric/assets/120015301/091128d1-b094-453b-9cff-a0aee96f83ea)

# Bring down the network
When you are finished using the test network, you can bring down the network with the following command:

    ./network.sh down
    
The command will stop and remove the node and chaincode containers, delete the organization crypto material, and remove the chaincode images from your Docker Registry.
