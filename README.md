#### public-rpc-setup - updated march 11 2022

#### This guide will show you how to setup a public rpc node for Etho-Protocol (ETHO) previously branded Ether-1

#### Setting Up the Server

    apt-get update
    apt-get upgrade -y
    apt-get install build-essential nano git make screen unzip curl nginx pkg-config tcl -y

#### Install Go

    wget https://storage.googleapis.com/golang/go1.16.9.linux-amd64.tar.gz
    sudo tar -xvf go1.16.9.linux-amd64.tar.gz
    sudo mv go /usr/local

    nano ~/.profile

#### Add the lines below to the bottom of ~/.profile

    export GOROOT=/usr/local/go
    export GOPATH=$ROOT
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

#### Exit nano with CRTL+X (Make sure you save)

    source ~/.profile
    
#### Download and install ***LATEST*** version of geth    

    git clone https://github.com/Ether1Project/Ether1 && cd Ether1 && make && cd build/bin/ && sudo mv geth /usr/local/bin/

#### Setting up etho-geth-rpc.service 

    sudo nano /etc/systemd/system/geth-etho-rpc.service

#### Copy and paste the following into the file - remember to replace "name" (2x) with your node name, and "your-user-name" with the user account you wish to run the node under

    [Unit]
    Description=Geth for public ETHO RPC
    After=network-online.target

    [Service]
    ExecStart=/usr/local/bin/geth --cache 1024 --http.vhosts "*" --http --http.port 8545 --http.addr 127.0.0.1 --http.corsdomain "*" --nat "any" --http.api "eth,web3,personal,net,network,debug,txpool" --syncmode "fast" --identity "<name>" --ethstats "<name>:ether1stats@stats.ethoprotocol.com"
    User=<your-user-name>

    [Install]
    WantedBy=multi-user.target

#### Exit nano - save your changes!

#### These are the available systemctl commands - you will want to do the first three commands to get the service up and confirm operation

    sudo systemctl enable geth-etho-rpc   <-- enables geth-etho-rpc.service
    sudo systemctl start geth-etho-rpc    <-- starts geth-etho-rpc.service
    sudo systemctl status geth-etho-rpc   <-- shows status of geth-etho-rpc.service
    sudo systemctl stop geth-etho-rpc     <-- stops geth-etho-rpc.service
    
    journalctl -f -u geth-etho-rpc        <-- this is similar to systemctl status but gives much more information.  also very useful

#### Setup for nginx

nano /etc/nginx/sites-enabled/default

#### Use CRTL+K to clear out the files contents & replace with the below - Replace RPC_URL_HERE with your rpc url for example: rpc.etho-protocol.ca

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

#### Read instructions from the next command carefully
sudo certbot --nginx  

#### Test Automatic SSL Cert Renewal
sudo certbot renew --dry-run
```
