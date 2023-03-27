
# Cybersecurity Mini Project 1 Report and Tutorial 
## Step 1 (Prepare Virtual Box)
install a virtual machine in your computer add any operating system ubuntu/lubuntu/xubuntu whatever you preferred
![image](https://user-images.githubusercontent.com/62655613/227700302-4bc8f95f-18a0-4db9-a00a-1f796fba93ef.png)

## Step 2 (Prepare Operating System)
Take a snapshot (not screenshot) of the virtual machine and save it as “State 0”.
Delete everything in the computer with the following command as root.
```bash
 sudo rm -rf /*
```
And enjoy your beloved machine being destroyed in front of your eyes.
Restore the snapshot taken earlier which was saved as “State 0”.
Run update and upgrade:
```bash
sudo apt-get update && sudo apt-get upgrade
```
```bash
sudo apt-get dist-upgrade
```
Configure the network of the virtual machine to use a  host-only network.
```bash
ip addr
```
```bash
ifconfig
```
Change the hostname of the VM to “server”.
Clone the virtual machine, turn it on and change the hostname to “client”.
![image](https://user-images.githubusercontent.com/62655613/227762432-5ece2621-7c65-4565-b72a-61893741710b.png)


# Configure Server 
## Step 3 Install Apache2 and Php
```bash
sudo apt update
```
install apache2
```bash
sudo apt-get install apache2
```
```bash
service apache2 status
```
install php
```bash
sudo apt-get install php
```
## Step 4 DNS Configuration
command for permission folder
```bash
sudo chown -R $USER:$USER /var/www/  
```
folder read write permission
```bash
sudo chmod -R 777 /var/www
```
or
```bash
sudo chmod -R 777 /var/www/server/uploaded_files
```
```bash
sudo a2dissite 000-default
```
```bash
systemctl reload apache2
```
```bash
sudo a2ensite main
```
```bash
systemctl reload apache2
```

Restrat server: Starting And Stopping Apache and Database Server
```bash
service apache2 stop
```
```bash
service apache2 start
```
```bash
service apache2 status
```
```bash
service apache2 restart
```
```bash
 service apache2 reload
```

DNS Configuration
package: 
```bash
sudo apt install bind9
```
```bash
sudo apt install dnsutils
```
```bash
sudo systemctl restart bind9.service
```
```bash
sudo nano /etc/resolv.conf
```
copy and paste ... to ...
........................................
```bash
nameserver 192.168.246.6
options edns0 trust-ad
search localdomain
```
........................................
CTRL+O CTRL+X for exit
```bash
sudo nano /etc/named.conf
```
copy and paste.... to.....
..........................................................................................................
```bash
//
//named.conf
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only name server (as a localhost DNS resolver only).
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
        listen-on port 53 { 127.0.0.1;};
//      listen-on-v6 port 53 { ::1; };
        forwarders { 8.8.8.8; 8.8.4.4; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { localhost; 192.168.0.0/24,192.168.246.6, 127.0.0.1 };
        recursion yes;


        dnssec-enable yes;
        dnssec-validation yes;
        dnssec-lookaside auto;


        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.iscdlv.key";


        managed-keys-directory "/var/named/dynamic";
};
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};
zone "." IN {
        type hint;
        file "named.ca";
};
include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```
 
```bash
 dig google.com
```
```bash
nslookup google.com
```
```bash
systemctl enable named
```
```bash
systemctl start named
```
```bash
sudo nano /etc/bind/verysecureserver.com.zone
```
copy paste ...... to ......
.........................................................................................
```bash
; Authoritative data for saifulislamsarfaraz.com zone
;
$TTL 1D
@ IN SOA saifulislamsarfaraz.com root.saifulislamsarfaraz.com. (
2022041301 ; serial
1D ; refresh
1H ; retry
1W ; expire
3H ) ; minimum
$ORIGIN saifulislamsarfaraz.com.
saifulislamsarfaraz.com. IN NS saifulislamsarfaraz.com.
@ IN A 127.0.0.1(your IP Address)
```
.........................................................................................
```bash
sudo nano /etc/bind/named.conf.local
```
copy and paste ....... to ........ 
................................................................................
```bash
zone "saifulislamsarfaraz.com" IN {
        type master;
        file "/etc/bind/saifulislamsarfaraz.com.zone";
};
```
..............................................................................
```bash
systemctl enable named
```
```bash
systemctl start named
```
```bash
systemctl restart named
```
```bash
dig saifulislamsarfaraz.com
```
```bash
nslookup saifulislamsarfaraz.com
```

## Step 5 Create certificate ans sign the site with the certificate
```bash
mkdir openssl
cd ../openssl/
mkdir {root-ca,sub-ca,server}
mkdir {root-ca,sub-ca,server}/{private,certs,newcerts,crl,csr}
```
```bash
touch root-ca/index
touch sub-ca/index
```
```bash
openssl genrsa -aes256 -out root-ca/private/ca.key 4096
openssl genrsa -aes256 -out sub-ca/private/sub-ca.key 4096
openssl genrsa -out server/private/server.key 2048
```
before go to next command make sure create sub-ca.conf root-ca.conf server.conf all files are given to this repository ossl folder


```bash
cd root-ca
openssl req -config root-ca.conf -key private/ca.key -new -x509 -days 7200 -sha256 -extensions v3_ca -out certs/ca.crt
#common name : Acme-RootCA
```
```bash
cd ../sub-ca/
openssl req -config sub-ca.conf -new -key private/sub-ca.key -sha256 -out csr/sub-ca.csr
#common name : Acme
```
```bash
cd ../root-ca
openssl ca -config root-ca.conf -extensions v3_intermediate_ca -days 3650 -notext -in ../sub-ca/csr/sub-ca.csr -out ../sub-ca/certs/sub-ca.crt -rand_serial
```
```bash
cd ../server
openssl req -config server.conf -key private/server.key -new -sha256 -out csr/server.csr
#common name : verysecureserver.com (your Domain Name)
```
```bash
cd ../sub-ca
openssl ca -config sub-ca.conf -extensions server_cert -days 365 -notext -in ../server/csr/server.csr -out ../server/certs/server.crt -rand_serial
```
```bash
cd ..
cat ./server/certs/server.crt ./sub-ca/certs/sub-ca.crt > chained.crt
```


# Revoke certificate
```bash
cd sub-ca
openssl ca -config sub-ca.conf -revoke ../server/certs/server.crt
```
# Add CRL to server 
```bash
cd sub-ca
nano crlnumber
#type: 1002
```
```bash
openssl ca -config sub-ca.conf -gencrl -out crl/rev.crl
```
# Client Configuration
```bash
sudo nano /etc/netplan/1-network-manager-all.yaml
```

Copy and paste to 
```bash
# Let NetworkManager manage all devices on this system
network:
    version: 2
    renderer: NetworkManager
    ethernets:
        enp0s3:
            dhcp4: no
            addresses: [192.168.246.7/24]
            routes:
                - to: default
                  via: 192.168.0.1
            nameservers:
                addresses: [192.168.246.6]
                search: [saifulislamsarfaraz.com]
```

```
```bash
 sudo netplan try
 ```
 ```bash
sudo resolvectl status
```
```bash
nslookup host/client ip
```
```bash
sudo a2enmod ssl
```
