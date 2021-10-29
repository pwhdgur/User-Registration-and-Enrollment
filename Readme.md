# User Registration and Enrollment
- Reference Material : the linux foundation lfd272

## Set Netowrk && Environment

### test-network 구동
- hyperledger fabric v2.2
- ./network.sh up createChannel -ca -s couchdb

### CouchDB URL
- http://localhost:5984/_utils 확인 가능
- COUCHDB_USER=admin
- COUCHDB_PASSWORD=adminpw

### Terminal 1 Environment variables for Org1MS 
- export PATH=${PWD}/../bin:$PATH
- export FABRIC_CFG_PATH=$PWD/../config/
- cd $HOME/go/src/github.com/pwhdgur/hyperledger/fabric-samples/test-network
- export CORE_PEER_TLS_ENABLED=true
- export CORE_PEER_LOCALMSPID="Org1MSP"
- export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
- export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
- export CORE_PEER_ADDRESS=localhost:7051

## Deploy Chaincode
- ./network.sh deployCC -ccn balance_transfer -ccv 1.0 -ccs 1 -ccp ~/go/src/github.com/pwhdgur/hyperledger/fabric-samples/lfd272/chaincodes/Lab12/balance_transfer -ccl javascript

## Enroll Org1 Fabric CA server administrator
- cd ~/go/src/github.com/pwhdgur/hyperledger/fabric-samples/lfd272/chaincodes/Lab12/balance_transfer_app
- npm install
- node enrollUser.js 'CAAdmin@org1.example.com' admin adminpw

## Register and enroll a new user with CAAdmin@org1.example.com
- node registerUser.js 'CAAdmin@org1.example.com' 'User@org1.example.com' '{"secret": "userpw"}'
- node enrollUser.js 'User@org1.example.com' 'User@org1.example.com' userpw

# Setting Custom Attributes

## Deploy the BalanceTransfer chaincode and update sequence number
- cd $HOME/go/src/github.com/pwhdgur/hyperledger/fabric-samples/test-network
- ./network.sh deployCC -ccn balance_transfer -ccv 2.0 -ccs 2 -ccp ~/go/src/github.com/pwhdgur/hyperledger/fabric-samples/lfd272/chaincodes/balance_transfer -ccl javascript

## register and enroll a new user with the init attribute set to true
- node registerUser.js 'CAAdmin@org1.example.com' 'InitUser@org1.example.com' '{"secret": "userpw", "attrs": [{"name": "init", "value":"true"}]}'
- node enrollUser.js 'InitUser@org1.example.com' 'InitUser@org1.example.com' userpw '[{"name": "init", "optional": false}]'

## Try to submit the initAccount transactionon behalf of both User and InitUser
- node submitTransaction.js 'User@org1.example.com' initAccount acc 100
=> Error

- node submitTransaction.js 'InitUser@org1.example.com' initAccount acc 100

## list the accounts to make sure that one was created.
- node submitTransaction.js 'InitUser@org1.example.com' listAccounts
