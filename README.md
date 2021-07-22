# openvpn-deployment


## 00. openvpn configuration 
```
openvpn az-1 : 1 server, 1 client
openvpn az-2 : 1 server, 1 client
```

## 01. update openvpn info
> Update openvpn server information
``` 
$ vi vars-az1.yml
$ vi vars-az2.yml
``` 

## 02. generate certificate
> Do not run for each server, run only the first time
``` 
$ source generate_ca.sh
$ ll creds
drwxrwxr-x 2 ubuntu ubuntu  4096 Jul 22 02:12 ./
drwxrwxr-x 4 ubuntu ubuntu  4096 Jul 21 01:47 ../
-rw-rw-r-- 1 ubuntu ubuntu 18098 Jul 14 04:22 vpn-server-az1.yml
-rw-rw-r-- 1 ubuntu ubuntu 18102 Jul 14 04:22 vpn-server-az2.yml
```

## 03. deploy openvpn az 1
> Deploy using the certificate of step 02. 
``` 
$ ./deploy-vpn-az1.sh
```

> ssh connection method
``` 
$ bosh int creds/vpn-deploy-az1.yml --path /ssh/private_key > openvpn-az1.key 
$ chmod 600 openvpn-az1.key
$ ssh openvpn@<openvpn-az1-ip> -i openvpn-az1.key
```

## 04. deploy openvpn az 2
> Deploy using the certificate of step 02. 
``` 
$ ./deploy-vpn-az2.sh
```

> ssh connection method
``` 
$ bosh int creds/vpn-deploy-az2.yml --path /ssh/private_key > openvpn-az2.key 
$ chmod 600 openvpn-az2.key
$ ssh openvpn@<openvpn-az2-ip> -i openvpn-az2.key
```

## 05. check openvpn connection
> Check the connection openvpn information on each server
> If there are tun0 and tun2 in the network interface, they are connected 
```
$ ssh openvpn@<openvpn-az1-ip> -i openvpn-az1.key 
or 
$ ssh openvpn@<openvpn-az2-ip> -i openvpn-az2.key 

$ sudo su
$ ifconfig 
$ ping <remote_network_ip>
``` 

## 06. Add static routing (optional)
> Add static routing to use client tunnel
```
$ sudo ip route add <remote_network_cidr_block> via <lan_ip>
ex> sudo ip route add 20.0.20.0/24 via 10.0.10.100

$ ping <remote_network_ip>
```
