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

	daemon=0
	regtest=1
	par=2
	txindex=1
	logevents=1
	port=23888
	rpcport=13888
	onlynet=ipv4
	listenonion=0
	logips=1
	uacomment=qtumd_master
	debug=1
	record-log-opcodes=1
	rpcuser=user1 
	rpcpassword=password1

4. Check if your configuration is correct and request info from your rpc wallet.

    ```bash
    ./qtumd -conf=/home/qtum-0.14.15/bin/qtum.conf &
    ./qtum-cli -conf=/home/qtum-0.14.15/bin/qtum.conf getinfo
    ```

You should get a response that looks like this:


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


5. Igniting your QTUM testnet

	As you can see in the previous response there are 0 block mined so far. In order to ignite the PoS of your development QTUM chain you can generate the first 5-600 blocks. This should enable the network and allow for 'normal functionality'.

    ```bash
	./qtum-cli -conf=/home/qtum-0.14.15/bin/qtum.conf generate 600
    ```

    If you run the getinfo command again you'll now see that the blocks variable is at 600 (or more).

6. Setting up a second (or any number) node

	To set up another node to join your Master-Node in the network you follow the same steps [1-4] with a small difference, the only thing you need to change is adding the ip address of your Master-Node to the qtum.conf file:

    ```bash
	daemon=0
	regtest=1
	par=2
	txindex=1
	logevents=1
	port=23888
	addnode=1.1.1.1:23888
	onlynet=ipv4
	listenonion=0
	logips=1
	uacomment=qtumd_node
	debug=1
	record-log-opcodes=1
	rpcuser=user1 
	rpcpassword=password1
    ```

7. Making qtumd start at boot

	In order to have your QTUM RPC wallet start from boot you can create a systemd service. This can be done in the following way:

    ```bash
	sudo vi /lib/systemd/system/qtumd.service
	```

	Add the following config to have a simple startup service.

    ```bash
	[Unit]
	Description=start qtumd server
	After=network.target

	[Service]
	Type=simple
	User=root
	ExecStart=/home/qtum-0.14.15/bin/qtumd -conf=/home/qtum-0.14.15/bin/qtum.conf
	Restart=on-failure

	[Install]
	WantedBy=multi-user.target
	```

	Test if your service is working by executing the systemct start command.

    ```bash
    sudo systemctl start qtumd
	sudo systemctl status qtumd 
	```

	If everything is alright you can expect a response like:

    ```bash
	 qtumd.service - start qtumd server
	   Loaded: loaded (/lib/systemd/system/qtumd.service; disabled; vendor preset: enabled)
	   Active: active (running) since Sat 2018-04-07 12:47:42 UTC; 1s ago
	 Main PID: 8084 (qtumd)
	    Tasks: 14
	   Memory: 61.3M
	      CPU: 157ms
	   CGroup: /system.slice/qtumd.service
	           └─8084 /home/qtum-0.14.15/bin/qtumd -conf=/home/qtum-0.14.15/bin/qtum.conf
	```

	When you're happy with your service you can enable it to start at boot by executing the systemctl enable function.

    ```bash
	sudo systemtl enable qtumd
	```
