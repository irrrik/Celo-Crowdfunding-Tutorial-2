---
description: >-
  We will learn how to deploy a Smart Contract which facilitates crowdfunding. This is part two of a three part series.
---

# 2. Deploying a Crowdfunding Smart Contract in Celo

## About the Authors

The written tutorials were created by [Alex Reyes](https://www.linkedin.com/in/alexreyes-tech). Alex is a student \(BS, Computer Science\) and crypto enthusiast who's learning all about the world of web3 one day at a time and he's contributing to Web3 communities actively. He previously completed internships at Facebook and Microsoft.

The videos were created by [Neo Cho](https://www.linkedin.com/in/neocho/). Neo is a student \(BS, Computer Science\) at the University of Central Florida. He enjoys learning about crypto, and is excited about the future of web3.

## Introduction 

If you've done part 1 of this tutorial series, you have a crowdfunding contract written in Solidity. Now that we've got a crowdfunding contract, we'll need to deploy it to the Celo test network in order to interact with it. Let's do that now!

## Video

Click the image below in order to watch the Youtube video for "Building a Crowdfunding Smart Contract in Celo".

[![Deploying a Crowdfunding Smart Contract in Celo](http://img.youtube.com/vi/HShFGDvBgNk/0.jpg)](https://www.youtube.com/watch?v=HShFGDvBgNk "Deploying a Crowdfunding Smart Contract in Celo")  

## Prerequisites

This tutorial assumes that you have followed and completed part 1 (Building a Crowdfunding Smart Contract in Celo). If you have not, please go back and follow that tutorial first. 

## Deployment Setup

First, open the terminal and `cd` into the Celo crowdfunding project folder. 

``cd celo-crowdfunding``

### The migrations folder

If there is already the ``1_initial_migration.js`` file in the folder, delete it. 

Next, create a new file named ``1_celo_crowdfund.js`` and write the following: 

```
const CeloCrowdfund = artifacts.require("CeloCrowdfund");

module.exports = function (deployer) {
  deployer.deploy(CeloCrowdfund);
};
```

Migrations in truffle are essentially deployment scripts. What we have written is a very simple deployment script which takes our contract (``CeloCrowdfund``) and deploy it. 

## Connecting to a Testnet node

We're going to use [Datahub](https://figment.io/datahub/) to connect to the Celo test network. If you don't have an account, sign up on the [Datahub](https://figment.io/datahub/) website and resume this tutorial once you have your Celo API key. 

Now that we have an API key for the Datahub node, we'll want to secure it. Anyone who obtains your API key will be able to use your Datahub account's node. To secure it, let's create a `.env` file in the  **root directory**  of the `celoSmartContract` folder. The `.env` file is used for environment secrets, which means its the perfect place to store the Datahub API key. 

To create this `.env` file, navigate to the root directory of the project and type the following command into the terminal:

```touch .env```

Next, open the `.env` file in a text editor and add the following variable, making sure to enter the API key between the `/`'s:

`REST_URL=https://celo-alfajores--rpc.datahub.figment.io/apikey/<YOUR API KEY>/`

**Note:** there needs to be a trailing `/` at the end for this to work!

**Note:** If the plan is to commit this repo into Github, you won't want to upload the contents of the `.env` file. To get Git to ignore the `.env` file, create a `.gitignore` file from the terminal like so: 

```touch .gitignore```

Inside the `.gitignore`, put the following in a new line: 

```
.env
```

And that's it! The `.env` file will now be ignored by Git. 

# Getting a Celo account

Next, we’re going to need a Celo account to deploy from. We will need three things for deployment:

-   A Celo account address
-   A Celo account private key
-   A Celo account  [loaded with testnet funds](https://celo.org/developers/faucet)

First things first, let's get an account and a private key. Create a file named **getAccount.js** in the project folder. In that file, write the following:

```
const ContractKit = require('@celo/contractkit');

const Web3 = require('web3');

require('dotenv').config();

const main = async() =>  {
  const web3 = new Web3(process.env.REST_URL);
  const client = ContractKit.newKitFromWeb3(web3);

  const account = web3.eth.accounts.create();

  console.log('address: ', account.address);
  console.log('privateKey: ', account.privateKey);
};

main().catch((err)  =>  {
  console.error(err);
});
```
Let's break this down.

First, the script imports `ContractKit`, `Web3`, and `dotenv`. Next, it connects to the Celo network via the REST\_URL in the `.env` file using the following line:

`const web3 = new Web3(process.env.REST_URL);`

Then it creates an account on that same testnet connection with the following line:

`const account = web3.eth.accounts.create();`

After that, we just print out the address and private key for future use.

**Note:** The above code is from [\#2. Create your first Celo account](https://learn.figment.io/network-documentation/celo/tutorial/intro-pathway-celo-basics/2.account), so feel free to rewind if you need a review.

Next, run the script in the terminal:

`node getAccount.js`

The output should print out the address and private key for you new Celo account. It will look like this:

[![output](https://camo.githubusercontent.com/85e62204e4c406103e375245afef4c7ee1fb83998bf2b08e168044c4863fd42b/68747470733a2f2f692e696d6775722e636f6d2f7571314c5854662e706e67)](https://camo.githubusercontent.com/85e62204e4c406103e375245afef4c7ee1fb83998bf2b08e168044c4863fd42b/68747470733a2f2f692e696d6775722e636f6d2f7571314c5854662e706e67)

**Note:** It is important to keep your private key secure! Whoever has it can access all your funds on Celo.

Copy the privateKey into the `.env` file by adding a line with the following:

`PRIVATE_KEY=YOUR-PRIVATE-KEY`

Where **YOUR-PRIVATE-KEY** is the private key you got from the script output.

Now that you have a Celo account, take the address and paste it into the [Celo developer faucet](https://celo.org/developers/faucet). This will give you testnet funds you can use to deploy the smart contract. Fill out the form and wait a couple of seconds, and your account should be loaded up and ready to go.

[![funding](https://camo.githubusercontent.com/0c74bcb5251da3397bae2c6901c25218a6ff73befb825a53c546181e95e0db06/68747470733a2f2f692e696d6775722e636f6d2f7a5074575748572e706e67)](https://camo.githubusercontent.com/0c74bcb5251da3397bae2c6901c25218a6ff73befb825a53c546181e95e0db06/68747470733a2f2f692e696d6775722e636f6d2f7a5074575748572e706e67)

## Truffle config

The **truffle-config.js** is used in order to tell truffle how you want to deploy the contract.

For our purposes, we will need to add the following code to the project truffle-config file:

```javascript
const ContractKit = require('@celo/contractkit');
const Web3 = require('web3');

require('dotenv').config({path: '.env'});

// Create connection to DataHub Celo Network node
const web3 = new Web3(process.env.REST_URL);

const client = ContractKit.newKitFromWeb3(web3);

// Initialize account from our private key
const account = web3.eth.accounts.privateKeyToAccount(process.env.PRIVATE_KEY);

// We need to add private key to ContractKit in order to sign transactions
client.addAccount(account.privateKey);

module.exports = {
  compilers: {
    solc: {
      version: "0.8.0",    // Fetch exact version from solc-bin (default: truffle's version)
    }
  },
  networks: {
    test: {
      host: "127.0.0.1",
      port: 7545,
      network_id: "*"
    },
    alfajores: {
      provider: client.connection.web3.currentProvider, // CeloProvider
      network_id: 44787  // latest Alfajores network id
    }
  }
};
```

First, the config file imports `ContractKit`, `Web3`, and `dotenv` just like the `getAccounts.js` file.

It connects to our Celo node by getting the REST\_URL from `.env`:

`const web3 = new Web3(process.env.REST_URL);`

Then it gets the account from the private key in the `.env` file in order to deploy from your account:

`const account = web3.eth.accounts.privateKeyToAccount(process.env.PRIVATE_KEY);`

We also add the private key to `ContractKit` in order to sign transactions with the following:

`client.addAccount(account.privateKey);`

And finally, in the `module.exports` function, we set the Solidity version we want to use under `compilers: {` and the network we want to deploy to under `networks: {`.

The following block of code is what tells truffle to deploy to Alfajores \(Celo's test network\):

```javascript
alfajores: {
    provider: client.connection.web3.currentProvider, // CeloProvider
    network_id: 44787  // latest Alfajores network id
}
```

## Deployment

We’re almost there! Run the following to check that you did everything correctly:

`truffle compile`

If things are working, you should see the following output:

![image](https://i.imgur.com/WqSM6mL.png)

Now that we’ve compiled the smart contract, the last step is to deploy it. Run the following to deploy to the Alfajores testnet:

`truffle migrate --network alfajores`

You should see the following deployment output:

![output](https://i.imgur.com/9m66tCO.png)

If you see something like the above, our code worked! To see the smart contract on the Celo network, open the [Alfajores block explorer](https://alfajores-blockscout.celo-testnet.org/) and paste in the address on the contract address line of the truffle output:

`> contract address: YOUR-CELO-ADDRESS`

You should see a successful contract deployment at that address!

![block explorer](https://i.imgur.com/IAENWP1.png)

## Next steps

Now that we've deployed our Celo crowdfunding smart contract on Celo, we can move on to interacting with it. In the next tutorial, we'll interact with our smart contract by creating new crowdfunding projects and donating to them. 

## Common Errors & Solutions

If you run into any problems, feel free to ask on the [Figment Learn Discord](https://discord.gg/f5YuEsQTAx). You can also view the source code [here](https://github.com/alexreyes/Celo-Crowdfunding-Tutorial-2). In any case, here are some common errors you may encounter.

If you get the following error:

`Error: Invalid JSON RPC response: {"message":"no Route matched with those values"}`

[![json rpc error](https://camo.githubusercontent.com/b10c13fb11358a60891ccd1e3191e40ffe54d19ffef9b3f0b127e3e567eed1b6/68747470733a2f2f692e696d6775722e636f6d2f42384c657272552e706e67)](https://camo.githubusercontent.com/b10c13fb11358a60891ccd1e3191e40ffe54d19ffef9b3f0b127e3e567eed1b6/68747470733a2f2f692e696d6775722e636f6d2f42384c657272552e706e67)

Then it's a problem with the **REST\_URL** in the **.env** file.

Make sure the URL has a trailing **/** at the end! It should look like this:

`REST_URL = https://celo-alfajores--rpc.datahub.figment.io/apikey/YOUR-API-KEY/`

If the contract didn't deploy to the test network, the output might look like this:

[![didn&apos;t deploy](https://camo.githubusercontent.com/a59c20e99081c85f182da44f68a167aec86a58da7c690367b4dbe35f57673943/68747470733a2f2f692e696d6775722e636f6d2f703637645a444d2e706e67)](https://camo.githubusercontent.com/a59c20e99081c85f182da44f68a167aec86a58da7c690367b4dbe35f57673943/68747470733a2f2f692e696d6775722e636f6d2f703637645a444d2e706e67)

Where the contracts compiled but it didn't give you an address for where it was deployed to.

To fix this, make sure you have your account loaded up with [testnet funds](https://celo.org/developers/faucet).

If you want to double check that your account received the funds, go to the [Alfajores block explorer](https://alfajores-blockscout.celo-testnet.org/) and paste in your account's address.

Make sure your account isn't empty like this one!

[![empty account](https://camo.githubusercontent.com/60a2da1b381d2d9f9c21ade38e54f3793ead7bf2162d4eb7ccb8297733494cbc/68747470733a2f2f692e696d6775722e636f6d2f795055594353442e706e67)](https://camo.githubusercontent.com/60a2da1b381d2d9f9c21ade38e54f3793ead7bf2162d4eb7ccb8297733494cbc/68747470733a2f2f692e696d6775722e636f6d2f795055594353442e706e67)
