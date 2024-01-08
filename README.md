# NSSF-Slicing
This experiment has been performed by 5G use case lab (5GUCL) IDRBT.
Requirements: USRP B210, PC with 16GB RAM and a hexacore processor 

## Create a VM
Create a VM so that you can run your core inside that VM and gNB on the bare metal.

## Clone the Repo
Now in that VM.
Firstly, clone the repository available on the [oai Gitlab](https://gitlab.eurecom.fr/oai/cn5g/oai-cn5g-fed).
```
git clone https://github.com/5g-ucl-idrbt/nssf-slicing.git
```
Then,
```
cd oai-cn5g-fed/docker-compose/
```
In this folder, clone this repository
```
git clone https://github.com/5g-ucl-idrbt/nssf-slicing.git
```

## Setup for your Core to connect to UE
Now, you to need to make changes for your UE to be able to connect to Core through gNB. 
You can look into the mcc and mnc values in the docker-compose file which you think will work for you. Otherwise, the already set mcc and mnc values will work.
Now, set the IMSI values in the database according to the sim you are going to connect.
```
cd oai-cn5g-fed/docker-compose/database/
```
See, which database is being used in the docker-compose and set the AuthenticationSubscription, AccesAndMobilitySubscriptionData and SessionManagementSubscriptionData accordingly to connect to your sim.


## Commmands to be executed in Core VM in order to connect to the gNB
```
sudo sysctl net.ipv4.ip_forward=1
sudo iptables -P FORWARD ACCEPT
sudo ip route add 192.168.71.194 via <GNB Baremetal IP>
sudo ip route add 12.1.1.0/24 via 192.168.70.134 # Forward packets to Mobiles from external sources
```
To check if the devices are connected to core follow the AMF logs
```
sudo docker logs --follow oai-amf
```

## Setting up gNB
Clone the Repository for [gNB](https://github.com/5g-ucl-idrbt/oai-gnodeb-b210.git) in bare metal.
And execute the following commands.

```
sudo sysctl net.ipv4.ip_forward=1
sudo iptables -P FORWARD ACCEPT
sudo ip route add 192.168.70.128/26 via <Bridge IP of Core VM>
```
```
cd ci-scripts/yaml_files/sa_b200_gnb/
sudo docker-compose up -d
```
```
sudo docker exec -it sa-b200-gnb bash
```
```
bash bin/entrypoint.sh
/opt/oai-gnb/bin/nr-softmodem -O /opt/oai-gnb/etc/gnb.conf $USE_ADDITIONAL_OPTIONS
```

## Connecting UE
Now turn off the airplane mode of your UE/Mobile and you will be able to see your UE/mobile getting connected to the gNB and can also see it in the logs of oai-amf.

