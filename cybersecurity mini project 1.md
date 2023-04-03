
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
![image](https://user-images.githubusercontent.com/62655613/229294305-6e32c918-efae-4797-8271-c05fd6b69bc5.png)

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
![image](https://user-images.githubusercontent.com/62655613/229294454-bcd8ea00-9965-4f30-b584-44bfb92f5527.png)

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
After execution this command paste bellow code

```bash
nameserver 192.168.246.6
options edns0 trust-ad
search localdomain
```
To save the file amd back previous 
CTRL+O CTRL+X for exit

![image](https://user-images.githubusercontent.com/62655613/229294629-467de732-2ba9-4a2b-a05f-79db1ca6e7b3.png)

```bash
sudo nano /etc/named.conf
```
After execution name configuration paste the bellow code

```bash
//
//named.conf
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only name server (as a localhost DNS resolver only).
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
        listen-on port 53 { 192.168.246.6; };
//      listen-on-v6 port 53 { ::1; };
        forwarders { 8.8.8.8; 8.8.4.4; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { localhost; 192.168.0.0/24, 192.168.246.6 };
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
 ![image](https://user-images.githubusercontent.com/62655613/229294706-c7daca47-97e1-40b1-8e6a-1fb728afab00.png)

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
sudo nano /etc/bind/hackssl.com.zone
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
@ IN A 192.168.246.6 (your IP Address)
```
![image](https://user-images.githubusercontent.com/62655613/229294901-35a52bdb-157d-492f-9831-930e85a2b9f3.png)

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
![image](https://user-images.githubusercontent.com/62655613/229295032-1651907c-4e1b-4016-9dad-94dee303ad88.png)

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
![image](https://user-images.githubusercontent.com/62655613/229295195-0e1211c8-093c-433d-b560-2b82198ffd68.png)

## Step 5 Create certificate ans sign the site with the certificate
```bash
mkdir openssl
cd openssl
mkdir {root-ca,sub-ca,server}
mkdir {root-ca,sub-ca,server}/{private,certs,newcerts,crl,csr}
```
```bash
touch root-ca/index
touch sub-ca/index
```
![image](https://user-images.githubusercontent.com/62655613/229295372-6056ff85-a9d9-4017-9a69-c6290a0f0ac8.png)

```bash
openssl genrsa -aes256 -out root-ca/private/ca.key 4096
openssl genrsa -aes256 -out sub-ca/private/sub-ca.key 4096
openssl genrsa -out server/private/server.key 2048
```
![image](https://user-images.githubusercontent.com/62655613/229295418-781f0418-7c34-4a30-98e9-aeb0faa94fe0.png)
```bash
cd root-ca
touch root-ca.conf
```
```bash
cd ../sub-ca
touch sub-ca.conf
```
```bash
cd ../server
touch server.conf
```
before go to next command make sure create sub-ca.conf root-ca.conf server.conf all files are given to this repository ossl folder
![image](https://user-images.githubusercontent.com/62655613/229295811-02f62a79-72a0-4487-beed-79026a87ea65.png)
![image](https://user-images.githubusercontent.com/62655613/229295829-b0f88c5f-9ddb-4592-8c4e-c4ce5b7f3efc.png)
![image](https://user-images.githubusercontent.com/62655613/229295865-fb9d4f58-7b54-42f0-b749-5bfddfafd497.png)

#### DNS add 
![image](https://user-images.githubusercontent.com/62655613/229295913-601d6023-a16c-4166-bbec-bfba3a2c9920.png)
![image](https://user-images.githubusercontent.com/62655613/229295956-e6e76ed3-8109-40aa-a087-c22585500dbe.png)
![image](https://user-images.githubusercontent.com/62655613/229295982-e2058f18-61d6-4770-bafe-5f10f2fbe330.png)

```bash
cd root-ca
openssl req -config root-ca.conf -key private/ca.key -new -x509 -days 7200 -sha256 -extensions v3_ca -out certs/ca.crt
#common name : Root-CA


```
![image](https://user-images.githubusercontent.com/62655613/229296098-fb26157c-72b9-427d-9f09-a68c6cbdff5b.png)

```bash
cd ../sub-ca/
openssl req -config sub-ca.conf -new -key private/sub-ca.key -sha256 -out csr/sub-ca.csr
#common name : Acme
```
![image](https://user-images.githubusercontent.com/62655613/229296157-e42e6192-35e9-4aac-8e12-67ba05b8154f.png)


```bash
cd ../root-ca
openssl ca -config root-ca.conf -extensions v3_intermediate_ca -days 3650 -notext -in ../sub-ca/csr/sub-ca.csr -out ../sub-ca/certs/sub-ca.crt -rand_serial
```
![image](https://user-images.githubusercontent.com/62655613/229296218-7161b3c5-45d3-443f-8fcd-0897d9d41bc4.png)

```bash
cd ../server
openssl req -config server.conf -key private/server.key -new -sha256 -out csr/server.csr
#common name : verysecureserver.com (your Domain Name)
```
![image](https://user-images.githubusercontent.com/62655613/229296261-dfb3c751-1eb1-4071-9db3-615d7bab8553.png)

```bash
cd ../sub-ca
openssl ca -config sub-ca.conf -extensions server_cert -days 365 -notext -in ../server/csr/server.csr -out ../server/certs/server.crt -rand_serial
```
![image](https://user-images.githubusercontent.com/62655613/229296300-cd7a8e20-bdbb-49ca-8d37-2af5ac508559.png)

```bash
cd ..
cat ./server/certs/server.crt ./sub-ca/certs/sub-ca.crt > chained.crt
```
![image](https://user-images.githubusercontent.com/62655613/229296359-84ef6509-46c9-43d1-ab4e-10495eeb74a5.png)
#### Site Configuration
![image](https://user-images.githubusercontent.com/62655613/229296506-d71f2cae-443c-4a1a-bc09-075559072915.png)

![image](https://user-images.githubusercontent.com/62655613/229296636-ced21d0b-898b-479c-856a-b29be1b9d624.png)


![image](https://user-images.githubusercontent.com/62655613/229296588-ec98556d-cd6b-40e4-adf8-ee286a995d4e.png)

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
```bash
sudo a2enmod ssl
```

```bash
systemctl enable named
```

Browser site with Https:

![image](https://user-images.githubusercontent.com/62655613/229296955-f4690c01-87e1-4e3e-b6af-40dd0cb632c8.png)
# Firewall
```bash
sudo ufw default deny
sudo ufw allow 53
sudo ufw allow 80
sudo ufw allow 443
```
# IDS
### Step 1
```bash
sudo apt-get install snort -y
```

```bash
sudo snort -A console -q -u snort -g snort -c /etc/snort/snort.conf -i enp0s3
```

### step2 Client

```bash
sudo apt install hping3 -y
```
```bash
sudo hping3 -S --flood -V -p 80 192.168.246.6
```
![image](https://user-images.githubusercontent.com/62655613/229309660-3712719c-0f00-4c55-8b68-0f84c8e4c9c6.png)

