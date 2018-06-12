configtxgen -profile ThreeOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/mojaniMSPanchors.tx -channelID $CHANNEL_NAME -asOrg mojaniMSP
configtxgen -profile ThreeOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/kaveriMSPanchors.tx -channelID $CHANNEL_NAME -asOrg kaveriMSP
configtxgen -profile ThreeOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/financeMSPanchors.tx -channelID $CHANNEL_NAME -asOrg financeMSP


curl -sSL https://goo.gl/6wtTN5 | bash -s 1.1.0-alpha

docker rm -f $(docker ps -aq)

export PATH=/home/vn/.nvm/versions/node/v6.9.5/bin:/home/vn/bin:/home/vn/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/vn/fabric-samples/bin:/opt/ibm/node/bin:/usr/local/docker:/usr/local/go/bin

export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin

cryptogen generate --config=./crypto-config.yaml


export FABRIC_CFG_PATH=$PWD

mkdir channel-artifacts

configtxgen -profile ThreeOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block

export CHANNEL_NAME=lrm

configtxgen -profile ThreeOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $CHANNEL_NAME


configtxgen -profile ThreeOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/mojaniMSPanchors.tx -channelID $CHANNEL_NAME -asOrg mojaniMSP
configtxgen -profile ThreeOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/kaveriMSPanchors.tx -channelID $CHANNEL_NAME -asOrg kaveriMSP
configtxgen -profile ThreeOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/financeMSPanchors.tx -channelID $CHANNEL_NAME -asOrg financeMSP

Start the network :

CHANNEL_NAME=$CHANNEL_NAME docker-compose -f docker-compose-cli.yaml up -d

Run time configurations :


To enter the cli container to interact with the peers :

docker exec -it cli bash



CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/users/Admin@kaveri.cts.com/msp
CORE_PEER_ADDRESS=peer0.kaveri.cts.com:7051
CORE_PEER_LOCALMSPID="kaveriMSP"
CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/peers/peer0.kaveri.cts.com/tls/ca.crt

Creating and adding orderer node to the channel : 

export CHANNEL_NAME=lrm

peer channel create -o orderer.cts.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/cts.com/orderers/orderer.cts.com/msp/tlscacerts/tlsca.cts.com-cert.pem


running the command ls will show the channel

Join the peers to the channel :

peer channel join -b lrm.block   (this will join the default peer which is peer0 of mojani to the channel)

Next join the other peers to the channel :

CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/users/Admin@kaveri.cts.com/msp CORE_PEER_ADDRESS=peer0.kaveri.cts.com:7051 CORE_PEER_LOCALMSPID="kaveriMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/peers/peer0.kaveri.cts.com/tls/ca.crt peer channel join -b lrm.block

CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/users/Admin@kaveri.cts.com/msp CORE_PEER_ADDRESS=peer1.kaveri.cts.com:7051 CORE_PEER_LOCALMSPID="kaveriMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/peers/peer0.kaveri.cts.com/tls/ca.crt peer channel join -b lrm.block


CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/finance.cts.com/users/Admin@finance.cts.com/msp CORE_PEER_ADDRESS=peer0.finance.cts.com:7051 CORE_PEER_LOCALMSPID="financeMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/finance.cts.com/peers/peer0.finance.cts.com/tls/ca.crt peer channel join -b lrm.block


To view the logs for any peer (exit from the cli first)

e.g. docker logs peer0.mojani.cts.com


Update the anchor peers (from the cli bash)

peer channel update -o orderer.cts.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/mojaniMSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/cts.com/orderers/orderer.cts.com/msp/tlscacerts/tlsca.cts.com-cert.pem

CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/users/Admin@kaveri.cts.com/msp CORE_PEER_ADDRESS=peer0.kaveri.cts.com:7051 CORE_PEER_LOCALMSPID="kaveriMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/peers/peer0.kaveri.cts.com/tls/ca.crt peer channel update -o orderer.cts.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/kaveriMSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/cts.com/orderers/orderer.cts.com/msp/tlscacerts/tlsca.cts.com-cert.pem

CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/finance.cts.com/users/Admin@finance.cts.com/msp CORE_PEER_ADDRESS=peer0.finance.cts.com:7051 CORE_PEER_LOCALMSPID="financeMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/finance.cts.com/peers/peer0.finance.cts.com/tls/ca.crt peer channel update -o orderer.cts.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/financeMSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/cts.com/orderers/orderer.cts.com/msp/tlscacerts/tlsca.cts.com-cert.pem

Install chain code on all the peers

peer chaincode install -n mycc -v 1.0 -p github.com/chaincode/bhoomi/go/

peer chaincode install -n mycc -v 1.6 -p github.com/chaincode/bhoomi/go/

CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/users/Admin@kaveri.cts.com/msp CORE_PEER_ADDRESS=peer0.kaveri.cts.com:7051 CORE_PEER_LOCALMSPID="kaveriMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/peers/peer0.kaveri.cts.com/tls/ca.crt peer chaincode install -n mycc -v 1.6 -p github.com/chaincode/bhoomi/go/

CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/users/Admin@kaveri.cts.com/msp CORE_PEER_ADDRESS=peer1.kaveri.cts.com:7051 CORE_PEER_LOCALMSPID="kaveriMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/kaveri.cts.com/peers/peer1.kaveri.cts.com/tls/ca.crt peer chaincode install -n mycc -v 1.6 -p github.com/chaincode/bhoomi/go/



CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/finance.cts.com/users/Admin@finance.cts.com/msp CORE_PEER_ADDRESS=peer0.finance.cts.com:7051 CORE_PEER_LOCALMSPID="financeMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/finance.cts.com/peers/peer0.finance.cts.com/tls/ca.crt peer chaincode install -n mycc -v 1.6 -p github.com/chaincode/bhoomi/go/


peer chaincode instantiate -o orderer.cts.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/cts.com/orderers/orderer.cts.com/msp/tlscacerts/tlsca.cts.com-cert.pem -C $CHANNEL_NAME -n mycc -v 1.6 -c '{"Args":["initLedger"]}' -P "OR ('mojaniMSP.member','kaveriMSP.member','financeMSP.member')"


peer chaincode instantiate -o orderer.cts.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/cts.com/orderers/orderer.cts.com/msp/tlscacerts/tlsca.cts.com-cert.pem -C $CHANNEL_NAME -n mycc -v 1.6 -c '{"Args":["queryLandRecord","999999999"]}' -P "OR ('mojaniMSP.member','kaveriMSP.member','financeMSP.member')"

peer chaincode query -C $CHANNEL_NAME -n mycc -v 1.6 -c '{"Args":["queryLandRecord","999999999"]}'

peer chaincode query -C $CHANNEL_NAME -n mycc -v 1.6 -c '{"Args":["initLedger"]}' 
