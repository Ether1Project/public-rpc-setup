# public-rpc-setup

### This guide will show you how to setup a public rpc node for Ether-1

#### Setting Up the Server & Building geth

```bash
apt-get update
apt-get upgrade -y
apt-get install build-essential nano git

wget https://golang.org/dl/go1.15.2.linux-amd64.tar.gz
sudo tar -xvf go1.15.2.linux-amd64.tar.gz
sudo mv go /usr/local

nano ~/.profile

# Add the lines below to the bottom of the file
export GOROOT=/usr/local/go
export GOPATH=$ROOT
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

# Exit nano with CRTL+X (Make sure you save)

source ~/.profile

git clone https://github.com/Ether1Project/Ether1 && cd Ether1 && make && cd

```

### Installing nginx & Setting up the services

```bash 
sudo apt-get install nginx 

nano /etc/systemd/system/ether1node.service

# Copy and paste the following into the file - remember to replace <name> with your node name

[Unit]
Description=RPC Node
After=network.target
[Service]
User=root
Group=root
Type=simple
Restart=always
ExecStart=/root/Ether1/build/bin/geth --cache=512 --rpcvhosts="*" --rpc --rpcport "8545" --rpcaddr "127.0.0.1" --rpccorsdomain "*" --nat "any" --rpcapi "eth,web3,personal,net" --syncmode "full" -ethstats "<name>:ether1stats@stats.ether1.org"
[Install]
WantedBy=default.target

# Exit nano - save your changes!

systemctl enable ether1node && systemctl start ether1nod

nano /etc/nginx/sites-enabled/default

# Use CRTL+K to clear out the files contents & replace with the below - Replace RPC_URL_HERE with your rpc url for example: rpc.ether1.org

server {
server_name RPC_URL_HERE;

 location / {
      proxy_pass http://localhost:8545/;
      proxy_set_header Host $host;
 }
}

# Exit nano - save your changes!

systemctl restart nginx
```

#### Enable SSL

```bash

sudo apt install snapd

sudo snap install --classic certbot

# Read instructions from the next command carefully
sudo certbot --nginx  

# Test Automatic SSL Cert Renewal
sudo certbot renew --dry-run
```
