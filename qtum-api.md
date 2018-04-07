## Connecting QTUM RPC client to NodeJS

To give the smart contract on the QTUM dev network some human understandable interfacd we will create an API using NodeJS with Express and QWeb3 modules installed. This allows us to both talk to the RPC client through a simple to program interface as well as provide a public API for other services to connect to our development chain.

More information on both Express and QWeb3 can be found on:

https://www.npmjs.com/package/qweb3
https://www.npmjs.com/package/express

# Step 1 Installing NodeJS

Installing the NodeJS and the Node packages is easiest to do through NVM (Node Version Manager). You can simply 


    wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

    vi /root/.profile

    
Inser the following lines to enable NVM to run from any location.

    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

Let the system refresh the .profile settings after saving them.

    source /root/.profile

You're all set up to install any NodeJS version you would like. For this API - QTUM connection we need version 8.10.0 (or higher). 

    nvm install 8.10.0
    nvm alias default 8.10.0

Since QWeb3 requires us to compile some parts of the module from scratch we need to make sure our python environment is updated and running as well. 

    sudo apt-get update     
    sudo apt-get install python2.7    
    ln -s /usr/bin/python2.7 /usr/bin/python 

The last command will create a symlink so you install scripts can find the right version of Python.


# Step 2 Installing dependencies

    npm install express --save
    npm install qweb3 --save


# Step 3 Creating an API

Goto your prefered development folder, for this example we'll take /home/ and create a directory to work in.

    cd /home
    mkdir testapi
    cd testapi

    vi api_server.js

        var express = require('express');
        var app = express();

        app.get('/hello', function (req, res) {
             res.send("hello");  
        })


        var server = app.listen(80, function () {
           var host = server.address().address
           var port = server.address().port

           console.log("Example api listening, port)
        })

To test our api you can run it and query through CURL or your browser.

    node api_server.js

response:
    Example api listening 80

    Server response should be 'hello'


# Step 4 Connecting QWeb3

Now the last step to put everything together is to add the QWeb3 module in the server application. 

    var express = require('express');
    var app = express();

    const { Qweb3 } = require('qweb3');
    const qClient = new Qweb3('http://user1:password1@localhost:13888');

    app.post('/', function(request, response){
      console.log(request.body);             // your JSON
       response.send(request.body.email);    // echo the result back
    });

    app.get('/blockheight', function (req, res) {
       myObj = { "blockheight":"" };
       getBlockCount().then((result)=>{
        myObj["blockheight"] = result.toString(); 
         res.send(JSON.stringify(myObj));   
       })
    })

    app.get('/connected', function (req, res) {
        myObj = { "connected":"" };
        isConnected().then((result)=>{
            myObj["connected"] = result.toString();
            res.send(JSON.stringify(myObj));    
        })
    })

    app.get('/getinfo', function (req, res) {
            getInfo().then((result)=>{
            res.send(JSON.stringify(result));   
        })
    })

    async function getBlockCount() {
      return await qClient.getBlockCount();
    }

    async function isConnected() {
      return await qClient.isConnected();
    }

    async function getBlockchainInfo() {
      return await qClient.getBlockchainInfo();
    }

    var server = app.listen(80, function () {
       var host = server.address().address
       var port = server.address().port

       console.log("Example app listening at http://%s:%s", host, port)
    })

If you now do a call to the api, you will get the appropriate response. 

e.g. 

    http://[yourserverip]/blockheight

returns

    {
        blockheight: "888"
    }


# Step 5 Calling a smart contract on the testchain.


    var express = require('express');
    var app = express();

    const { Contract } = require('qweb3');

    // The path to your local Qtum node via RPC
    const rpcAddress = 'http://user1:password1@localhost:13888';

    // contractAddress = The address of your contract deployed on the blockchain (this address is probably different for you)
    const contractAddress = '3574ac48e97eb4e657747711306f4265ddc56429';

    // contractAbi = The ABI of the contract, you can find this in the folder of your compiled smart contract outputs
    const contractAbi = [{"constant":false,"inputs":[],"name":"kill","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"greet","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[{"name":"_greeting","type":"string"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"}]

    const contract = new Contract(rpcAddress, contractAddress, contractAbi);    


    app.get('/call', function(req, res) {       // When the server gets the /call api function
        exampleCall().then((result)=>{
            res.send(JSON.stringify(result));   // Return server response
        })
    });

    async function exampleCall() {          
      return await contract.call('greet', {     // Call the greet function in the smart contract 
        methodArgs: [],
        senderAddress: "qQVJhrFou55VBrTXYVH13NAwVqvih52f7v",    // Set the sender address
      });
    }

    var server = app.listen(80, function () {
       var host = server.address().address
       var port = server.address().port

       console.log("Example API listening at port %s", port)
    })







