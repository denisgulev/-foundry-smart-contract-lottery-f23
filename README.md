# Random Raffle Contract

## About

A provably random smart contract lottery.

## What we want?

1. Users can enter by paying for a ticket
    a. The ticket fees are going to go to the winner during the draw.
2. After X period of time, the lottery will automatically draw a winner
3. Using Chainlink VRF and Automation
    a. VRF -> randomness
    b. Automation -> time based trigger

## Installation

```
make install
```

```
make test
```

- To deploy contract on local chain:
1. run anvil chain in a separate terminal
```
make anvil
```

2. deploy the contract on chain
```
make deploy
```

- To deploy on test net
1. assign values to the variables in the '.env' file
2. run the following command to make use of the variables
```
make deploy ARGS="--network sepolia"
```


### Style guides

- Layout of contract:
version
imports
errors
interfaces, libraries, contracts
type declarations
state variables
events
modifiers
functions

- Layout of functions:
constructor
receive functions
fallback functions
external
public
internal
private
view & pure

### Custom Errors

More gas efficient way to revert a transaction, as opposed to 'require()'.

Name the errors adding as a prefix the name of the contract.

### Events

Whenever a store variable is updated, we should emit an event.

Benefits:
- Make migration easier
- Make frontend indexing easier

Events allows us to save information as logs for a specific operation, whithout the need of a storage variable.
Logs are saved in a special data structure, that smart contracts cannot access, thus making it cost efficient.
Event logs are tied to the smart contract or the address that emitted the event.

Listening for events, to know when a certain operation has been executed, is much cheaper.

Events have the following form:
```
event storedNumber (
    uint256 indexed oldNumber;
    uint256 indexed newNumber;
    uint256 addedNumber;
    address user;
)
```
this can contain two types of parameters:
- INDEXED (maximum 3 allowed), also called TOPICS -> these are searchable parameters
- NON-INDEXED

### Timestamp inside a contract

Set 2 variables within the constructor:
1. "immutable variable" to save the interval to wait for the Raffle to pick a winner
2. "storage variable" to keep track of the lastTimestamp

### Chainlink VRF

- call VRF to request a random number
- this generate the RN and call a contract on the chain, the VfCoordinator, which in return calls "rawFulfillRandomWords" that internally calls 
 "fulfillRandomWords"
- in the "fulfillRandomWords" function we receive the RN and implement logic to use it

### CEI : Checks, Effects, Interactions (pattern to follow for gas optimization)

1. Checks -> controls as 'require'
2. Effect -> affect the contract, i.e. update variables, create local ones
3. Interactions -> code that interact with external contracts

### Chainlink Automation

We use this to call automatically the "performUpkeep" function.

1. chainlink node call the "checkUpkeep" function
2. if (1) returns true, then "performUpkeep" function is called

### Tests

1. Deploy scripts
2. Write tests such that they
    1. work on a local chain
    2. forked testnet
    3. forket mainnet


### Fuzz Tests

Property based testing, i.e. a way of testing general behaviors.
This is done by defining a property as input of a test and let foundry generate 'X' amount of random values for the property 
to test against in the test.


### Test on Forked-Url

To run test on a forked url, we need to specify a private-key to be passed as input in "vm.startBroadcast()".
To do so:
- in HelperConfig, add 'deployerKey' as a new parameter to NetworkConfig; grab the value from .env file
- for local chain (anvil) we can define a constant to use for local NetworkConfig


### Interact with the contract

- call function
```
cast call <contract-address> "<function>" --rpc-url $SEPOLIA_RPC_URL
```