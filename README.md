# QTUM testnet node setup

This documentation describes how to setup a QTUM blockchain node (cluster) for development purposes. We assume for this documentation that you're running an Ubuntu 16.04.4 LTS environment.


## Getting Started

1. Install the build environment  

    ```bash
	sudo apt-get update && sudo apt-get upgrade
	sudo apt-get install build-essential
    ```  
2. Download & install the QTUM rpc wallet

    ```bash
	cd /home
	wget https://github.com/qtumproject/qtum/releases/download/mainnet-ignition-v0.14.15/qtum-0.14.15-x86_64-linux-gnu.tar.gz
	tar -xvf qtum-0.14.15-x86_64-linux-gnu.tar.gz
    ```  
3. Create the configuration files for your QTUM environment

    ```bash
    cd qtum-0.14.15/bin
	vi qtum.conf
    ```  

Use the following configuration to create your first node, be sure to change your rpcuser and rpcpassword accordingly.

    ```  
	daemon=0
	regtest=1
	par=2
	txindex=1
	logevents=1
	port=23888
	onlynet=ipv4
	listenonion=0
	logips=1
	uacomment=qtumd_node1
	debug=1
	record-log-opcodes=1
	rpcuser=user1 
	rpcpassword=password1
    ```    

4. Check if your configuration is correct and request info from your rpc wallet.

    ```bash
    ./qtumd -conf=/home/qtum-0.14.15/bin/qtum.conf &
    ./qtum-cli -conf=/home/qtum-0.14.15/bin/qtum.conf getinfo
    ``` 

You should get a response that looks like this:

    ```bash
	{
		version: 141501,
		protocolversion: 70016,
		walletversion: 130000,
		balance: 0,
		stake: 0,
		blocks: 0,
		timeoffset: 0,
		connections: 0,
		proxy: "",
		difficulty: {
			proof-of-work: 4.656542373906925e-10,
			proof-of-stake: 4.656542373906925e-10
		},
		testnet: false,
		moneysupply: 0,
		keypoololdest: 1523095203,
		keypoolsize: 100,
		paytxfee: 0,
		relayfee: 0.004,
		errors: ""
	}
    ``` 

5. Igniting your QTUM testnet

As you can see in the previous response there are 0 block mined so far. In order to ignite the PoS of your development QTUM chain you can generate the first 5-600 blocks. This should enable the network and allow for 'normal functionality'.

    ```bash
	./qtum-cli -conf=/home/qtum-0.14.15/bin/qtum.conf generate 600
    ``` 

