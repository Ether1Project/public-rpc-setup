#### public-rpc-setup - updated march 11 2022

#### This guide will show you how to setup a public rpc node for Etho-Protocol (ETHO) previously branded Ether-1

#### Setting Up the Server & Building geth

```bash
apt-get update
apt-get upgrade -y
apt-get install build-essential nano git make screen unzip curl nginx pkg-config tcl -y

wget https://storage.googleapis.com/golang/go1.16.9.linux-amd64.tar.gz
sudo tar -xvf go1.16.9.linux-amd64.tar.gz
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

#### Installing nginx & Setting up the services

......```bash 
......sudo apt-get install nginx 

nano /etc/systemd/system/geth-etho-rpc.service

#### Copy and paste the following into the file - remember to replace <name> with your node name

[Unit]
Description=Geth for public ETHO RPC
After=network-online.target

[Service]
ExecStart=/usr/local/bin/geth --cache 1024 --http.vhosts "*" --http --http.port 8545 --http.addr 127.0.0.1 --httpcorsdomain "*" --nat "any" --http.api "eth,web3,personal,net,network,debug,txpool" --syncmode "fast" --etherbase <your-address> --mine --extradata "<your-pool>"
User=<your-user-name>

[Install]
WantedBy=multi-user.target

#### Exit nano - save your changes!

systemctl enable geth-etho-rpc && systemctl start geth-etho-rpc

nano /etc/nginx/sites-enabled/default

#### Use CRTL+K to clear out the files contents & replace with the below - Replace RPC_URL_HERE with your rpc url for example: rpc.ether1.org

server {
server_name RPC_URL_HERE;

 location / {
      proxy_pass http://localhost:8545/;
      proxy_set_header Host $host;
 }
}

#### Exit nano - save your changes!

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
