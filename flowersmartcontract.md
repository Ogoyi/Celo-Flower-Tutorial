# A Detailed Guide on How to Create a Smart Contract for Flowers on the Celo Blockchain

_Estimated reading time: **10 minutes**_

## Table of contents

* [Introduction](#introduction)
* [Prerequisite](#prerequisite)
* [Requirement](#requirement)
* [Tutorial Overview](#tutorial-overview)
* [Contract Deployment](#contract-deployment)
* [Conclusion](#conclusion)
* [Next steps](#next-steps)
* [About the author](#about-the-author)

## Introduction

**What is Celo?**<br>

<img   src="https://coinculture.com/au/wp-content/uploads/2022/07/Celo.jpg" alt="celo img"/>

 Celo is a decentralized platform that aims to make financial tools accessible to people everywhere, especially in emerging markets. It uses a blockchain to secure transactions and allow for fast, cheap, and secure transfers of its stablecoin, CELO, which is pegged to the value of a basket of national currencies to reduce volatility. The Celo network is maintained by a network of validators who run its blockchain software and are incentivized with CELO tokens. The platform also includes a suite of dApps to offer financial services such as remittances, loans, and payments.<br>

## Prerequisite 

To take this tutorial, you will need:
- A desktop computer with internet and Chrome web browser.
- A basic understanding of blockchain technology and smart contracts. If you're not familiar with this, we suggest taking our [Introduction to Blockchain course first](https://dacade.org/communities/blockchain/courses/intro-to-blockchain).

## Requirement
- A code or text editor. This tutorial uses [Remix IDE](https://remix.ethereum.org).
- An internet browser and stable internet connection.

## Tutorial Overview

In this tutorial, we would be building a smart contract for buying and selling flowers on the Celo blockchain. The contract contains a set of rules and conditions that must be met before the transaction can take place. The conditions include the price of the flowers and any other relevant terms. Once the conditions are met, the contract automatically executes the transaction, transferring ownership of the flowers and the payment between the buyer and the seller in a secure, transparent, and tamper-proof manner. This eliminates the need for intermediaries and ensures that the transaction is fair, efficient, and reliable.<br>

Now let's begin writing our smart contract.

To get started, we will create a new file on Remix IDE called `flower.sol`. Click on this link to learn how to create a new file on Remix IDE [(here)](https://remix-ide.readthedocs.io/en/latest/file_explorer.html).

After creating a new file we start by declaring some statements in our smart contract.
```solidity
// SPDX-License-Identifier: MIT

pragma solidity >=0.7.0 <0.9.0;
```
The line `SPDX-License-Identifier: MIT` is an identifier for the license of the code, in this case, the MIT License. The SPDX (Software Package Data Exchange) identifier is a standardized way to identify open-source licenses.<br>

The next line is the declaration of the Solidity programming language used in a smart contract, specifically stating that the code is written in Solidity version 0.7.0 or later, but not later than 0.9.0. 

Next, we add our token interface.
```solidity
interface IERC20Token {
  function transfer(address, uint256) external returns (bool);
  function approve(address, uint256) external returns (bool);
  function transferFrom(address, address, uint256) external returns (bool);
  function totalSupply() external view returns (uint256);
  function balanceOf(address) external view returns (uint256);
  function allowance(address, address) external view returns (uint256);
  event Transfer(address indexed from, address indexed to, uint256 value);
  event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

The **IERC20Token** is an interface in the Ethereum network that defines the standard set of functions that an ERC-20 token contract must implement. It specifies the following functions:
- `transfer()`: This function allows the transfer of a specified amount of cUSD tokens from the caller's account to another account.
- `approve()`: This function allows the caller to approve a specified address to transfer a specified amount of cUSD tokens from their account.
- `transferFrom()`: This function allows us to transfer of a specified amount of cUSD tokens from one address to another if the caller has previously approved the transfer.
- `totalSupply()`: This function returns the total supply of tokens in the contract.
- `balanceOf()`: This function returns the balance of cUSD tokens for a specified address.
- `allowance()`: This function returns the amount of cUSD tokens that a specified spender is approved to transfer from a specified owner's account.
- `Transfer`: This is an event that is triggered whenever cUSD tokens are transferred from one address to another.
- `Approval`: This is an event that is triggered whenever transfer of tokens is approved.

Next, we begin by naming our contract and also creating our struct.<br>
A `struct` in Solidity is a user-defined complex data type that allows you to group multiple variables under a single name. It is used to define a custom type in the contract and can be used to store multiple values of different types under a single object. A struct can contain elements of different data types including other structs, arrays, and mappings.

```solidity
contract FloralNft {
    struct Flower {
        address payable owner;
        string name;
        string description;
        string image;
        uint price;
        bool forSale;
    }
```

The first line is the name given to our smart contract called `FloralNft`.<br>
The next line is the struct called `Flower` which holds information about a unique digital flower. The struct contains the following fields:
- `owner`: an address that stores the owner of the flower, denoted by the "_address payable_" data type, which allows payments to be made to that address.
- `name`: a string that stores the name of the flower.
- `description`: a string that stores a description of the flower.
- `image`: a string that stores the image of the flower.
- `price`: an unsigned integer (uint) that stores the price of the flower.
- `forSale`: a boolean value that indicates whether the flower is for sale or not.<br>

Additionally, to interact with the cUSD token on the Celo alfajores test network, it is necessary to include the address of the token.
```solidity
 address internal cUsdTokenAddress = 0x874069Fa1Eb16D44d622F2e0Ca25eeA172369bC1;
```
Next, we create our mapping. Click on this link to know more about mapping [(Mapping)](https://medium.com/upstate-interactive/mappings-in-solidity-explained-in-under-two-minutes-ecba88aff96e).
```solidity
mapping (uint => Flower) internal flowers;
uint internal flowersLength = 0; 
```
The mapping, named `flowers`, maps a key of type `uint` (unsigned integer) to a value of type `Flower`. This data structure allows for a unique `uint` key to be associated with a specific `Flower` object, allowing for efficient look-up and retrieval of `Flower` values based on their associated keys.<br>

A state variable named `flowersLength` is being declared in the next line as a data type of `uint`. This variable will store the number of flowers permanently in the contract and only accept unsigned integer values.<br>

Furthermore, to make our smart contract more interesting we begin to add functions. The first function we will be adding is the create flower function.

```solidity
    function createFlower(
        string memory _name,
        string memory _description,
        string memory _image,
        uint _price,
        bool _isSale
    ) public {
        flowers[flowersLength] = Flower(
            payable(msg.sender),
            _name,
            _description,
            _image,
            _price,
            _isSale
        );
        flowersLength++;
    }
```
The `createFlower()` function creates a new flower and stores it in a mapping named `flowers` where `flowersLength` keeps track of the number of flowers stored. The function takes 5 arguments:

- `_name` - the name of the flower
- `_description` - a description of the flower
- `_image` - a URL or reference to the image of the flower
- `_price` - the price of the flower
- `_isSale` - a boolean indicating if the flower is on sale

The `payable` keyword ensures that the sender of the transaction can pay for the gas required to execute the function. <br>

The next inline is the `getFlower()` function.
```solidity
  function getFlower(uint _index) public view returns(
        address payable,
        string memory,
        string memory,
        string memory,
        uint,
        bool
    ){
        Flower storage flower  = flowers[_index];
        return (
            flower.owner,
            flower.name,
            flower.description,
            flower.image,
            flower.price,
            flower.forSale
        );
    }
```

The `getFlower()` function helps retrieves information about a flower stored in the `flowers` mapping based on its index. The function has one input argument, `_index,` which is the index of the desired flower in the mapping. The function is marked with the _view_ and _public_ keywords, meaning that it can be executed by any user and does not modify the state of the contract. The function returns the owner's address, name, description, image, price, and sale status of the flower.<br>

Next, we add a `buy()` function that will enable users to purchase flowers from the blockchain.
```solidity
function buyFlower(uint _index)public payable{
        require(
            IERC20Token(cUsdTokenAddress).transferFrom(
                msg.sender,
                flowers[_index].owner,
                flowers[_index].price
            ),
            "Transaction can not be performed"
        );
        flowers[_index].forSale = false;
        flowers[_index].owner = payable(msg.sender);
    }
```

The `buy()` function allows a user to purchase a flower that is stored in a mapping called `flowers`. The input argument, `_index`, determines which flower the user wants to buy. The function is marked as _payable_ and _public_, meaning it can be executed by any user and requires payment in Ether.

The function first checks if the required amount can be transferred from the buyer's account to the seller's account using the `transferFrom()` function of an IERC20 token contract at a specified address. If the transfer fails, an error message "Transaction can not be performed" is thrown. If the transfer succeeds, the flower's `forSale` status is set to false and the ownership is transferred to the buyer.

Furthermore, we add a unique function which is the `setForsale()` function.

```solidity
 function setForSale(uint _index)public{
        require(msg.sender == flowers[_index].owner, "Unauthorized");
        flowers[_index].forSale = !flowers[_index].forSale;
    }
```

Basically, this function allows a user to toggle the status of a flower in the array between being available for sale or not available for sale. 

It works by changing the status of a flower to either "for sale" or "not for sale" in an array of flowers.

The function takes one input, which is the index of the flower that needs to be changed.

Then, the function updates the `forSale` property of the flower at that index to the opposite of its current value. For example, if the `forSale` property was `true`, it will now become `false`, and if it was `false`, it will now become `true`.

Finally, we add the `getFlowersLegth()` function that will help users know how many flowers are currently in the array, without having to look through the entire array themselves. It provides a quick and easy way to retrieve this information.

```solidity
 function getFlowerLength() public view returns (uint) {
        return (flowersLength);
    }
```

This function assists in returning the number of flowers in an array of flowers.

The function does not take any inputs and has a return value of an unsigned integer (`uint`).

When the function is called, it simply returns the value of a variable called `flowersLength`, which presumably stores the current number of flowers in the array.

The complete code for this session is provided below.

```solidity
// SPDX-License-Identifier: MIT

pragma solidity >=0.7.0 <0.9.0;

// Token Interface
interface IERC20Token {
  function transfer(address, uint256) external returns (bool);
  function approve(address, uint256) external returns (bool);
  function transferFrom(address, address, uint256) external returns (bool);
  function totalSupply() external view returns (uint256);
  function balanceOf(address) external view returns (uint256);
  function allowance(address, address) external view returns (uint256);
  event Transfer(address indexed from, address indexed to, uint256 value);
  event Approval(address indexed owner, address indexed spender, uint256 value);
}

contract FloralNft{
    struct Flower{
        address payable owner;
        string name;
        string description;
        string image;
        uint price;
        bool forSale;
    }
    address internal cUsdTokenAddress = 0x874069Fa1Eb16D44d622F2e0Ca25eeA172369bC1;
    mapping (uint => Flower) internal flowers;
    uint internal flowersLength = 0;
    
    /// @notice Create new flower object
    function createFlower(
        string memory _name,
        string memory _description,
        string memory _image,
        uint _price,
        bool _isSale
    )public{
        flowers[flowersLength] = Flower(
            payable(msg.sender),
            _name,
            _description,
            _image,
            _price,
            _isSale
        );
        flowersLength++;
    }

    /// @notice Read details of a Flower
    /// @return Details of flower
    /// @param _index Index of flower to read
    function getFlower(uint _index) public view returns(
        address payable,
        string memory,
        string memory,
        string memory,
        uint,
        bool
    ){
        Flower storage flower  = flowers[_index];
        return (
            flower.owner,
            flower.name,
            flower.description,
            flower.image,
            flower.price,
            flower.forSale
        );
    }

    /// @notice Buy flower from contract
    /// @param _index Index of flower to buy
    function buyFlower(uint _index)public payable{
        require(
            IERC20Token(cUsdTokenAddress).transferFrom(
                msg.sender,
                flowers[_index].owner,
                flowers[_index].price
            ),
            "Transaction can not be performed"
        );
        flowers[_index].forSale = false;
        flowers[_index].owner = payable(msg.sender);
    }
    
    /// @notice Gift flower to another user
    /// @param _index Index of flower to gift
    /// @param _address Address of receiver
    function giftFlower(uint _index, address _address)public{
        require(msg.sender == flowers[_index].owner, "Unauthorized");
        flowers[_index].owner = payable(_address);
    }

    /// @notice Toggle flower's forsale value
    /// @param _index Index of flower to toggle
    function setForSale(uint _index)public{
        require(msg.sender == flowers[_index].owner, "Unauthorized");
        flowers[_index].forSale = !flowers[_index].forSale;
    }
    
    /// @notice Return count of all flowers created
    function getFlowerLength() public view returns (uint) {
        return (flowersLength);
    }
}
```

## Contract Deployment

To deploy the contract, three things are required:
- [CeloExtensionWallet](https://chrome.google.com/webstore/detail/celoextensionwallet/kkilomkmpmkbdnfelcpgckmpcaemjcdh?hl=en)
- Celo Faucet [Celo Faucet](https://celo.org/developers/faucet)
- Celo Remix Plugin

Firstly, you can obtain the CeloExtensionWallet from the Google Chrome store by clicking on the provided link above. Once you have downloaded and installed the wallet, create a new wallet and ensure that you keep the key phrase in a safe location to prevent losing your funds permanently.

After setting up your wallet, you will need to fund it using the Celo Faucet. To do this, copy your wallet's address and navigate to the Celo Faucet link provided above. Next, paste your wallet's address in the text field and confirm the transaction.

The next step is to download and activate the Celo plugin from the plugin manager on Remix. Once you have done this, you should connect your wallet to Remix and proceed to deploy your contract.

## Conclusion

Congratulations on your accomplishment! You have done an excellent job of developing a smart contract for the sale of flowers on the Celo blockchain. Well done, and enjoy your success! 🎉

## Next steps 

I trust that you found this tutorial informative and learned a lot from it. If you would like to continue your education, I have provided some helpful links below that you may find useful to explore:

The official Celo documentation: https://docs.celo.org/

Solidity By Example, a website with code examples for learning Solidity: https://solidity-by-example.org/

OpenZeppelin Contracts, a library of secure, tested smart contract code: https://www.openzeppelin.com/contracts/

Solidity documentation for version 0.8.17: https://docs.soliditylang.org/en/v0.8.17/

I hope these resources prove to be useful to you!

## About the author

 I'm Ogoyi Thompson, a web3 developer residing in Nigeria, and I have a strong passion for working with blockchain technology.
