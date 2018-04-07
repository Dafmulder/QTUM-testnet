# QTUM testnet deploy your first smartcontract

This documentation describes how to compile and write your first (Solidity) smart contract to your freshly deployed testnetwork. 


## Writing the Contract

The Ethereum foundation has put out a simple smart contract code to perform a "hello world" proof of concept. 
For the first deployment we will be using this one to make sure we're on the right track.

1. Creating the contract

Create a hello.sol file on your local machine and save it. The content for this example is shown below.

    ```js
        contract mortal {
            /* Define variable owner of the type address */
            address owner;
    
            /* This function is executed at initialization and sets the owner of the contract */
            function mortal() { owner = msg.sender; }

            /* Function to recover the funds on the contract */
            function kill() { if (msg.sender == owner) selfdestruct(owner); }
        }

        contract greeter is mortal {
            /* Define variable greeting of the type string */
            string greeting;

            /* This runs when the contract is executed */
            function greeter(string _greeting) public {
                greeting = _greeting;
            }

            /* Main function */
            function greet() constant returns (string) {
                return greeting;
            }
        }
    ```
    
    
    
