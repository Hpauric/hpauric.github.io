---
layout: post
title: How to Query and Monitor Ethereum Contract Events with Web3
published: true
image-attributions: Photo by Giallo from Pexels
image_url: /images/400-300px/time-400-300.png
excerpt: >-
 With web3, you can query and listen for contract events on the Ethereum blockchain.
---

With [web3.js](https://web3js.readthedocs.io), you can query and listen for contract events on the Ethereum blockchain, so that you can specify actions to trigger when certain criteria are met.

In this guide I'll demonstrate the different methods for querying and listening for contract events with web3. I've designed this post so that you can use it as a reference and skip forward to the part you need. If you already know how to set up web3 you can skip to [making an event query](###-Making-a-Query).

Here's what I'll be covering in this post. 

* ToC
{:toc}

## Setting up the Contract

You need to instantiate a contract before you can query events for it.

```javascript
const Web3 = require('web3'); 
const client = require('node-rest-client-promise').Client();
const INFURA_KEY = "SECRET_INFURA_KEY"; // Insert your own key here :)
const ETHERSCAN_API_KEY = "SECRET_ETHERSCAN_KEY";
const web3 = new Web3('wss://mainnet.infura.io/ws/v3/'  +  INFURA_KEY);
const CONTRACT_ADDRESS = "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2";
const etherescan_url = `http://api.etherscan.io/api?module=contract&action=getabi&address=${CONTRACT_ADDRESS}&apikey=${ETHERSCAN_API_KEY}`

async function getContractAbi() {
    const etherescan_response = await client.getPromise(etherescan_url)
    const CONTRACT_ABI = JSON.parse(etherescan_response.data.result);
    return CONTRACT_ABI;
}

async function eventQuery(){
const CONTRACT_ABI = getContractAbi();
const contract = new web3.eth.Contract(CONTRACT_ABI, CONTRACT_ADDRESS);
/*    Code to query events here       */    
}

eventQuery();
```

The contract examples I use in this post all use the [wrapped ether](https://weth.io/) token. Because it's one of the most popular tokens in Ethereum, the contract has a high volume of transactions, making it a great address to use for testing event listening.

## Making a Event Query

There are two primary use cases for interacting with Ethereum contract events. You can:

- query a contract's past events for analysis or storage on a local database

- create an event listener for a contract's events to specify actions you want to trigger in specific circumstances


## Query Past Events with `getPastEvents()`

You can use web3 to query all past events that a contract has ever emitted. The `getPastEvents()` will return an **array** of event objects. However it is currently limited to one thousand results at a time - `getPastEvents()` will return an error if more results would be returned.

The first argument allows you to specify the event type to query, or `allEvents` to return all event types.
The second argument is a filter object that allows you to filter by start[^fn1] and end block as shown below:

```javascript
const START_BLOCK = 7700000;
const END_BLOCK = 7701000;
contract.getPastEvents("allEvents",
    {                               
        fromBlock: START_BLOCK,     
        toBlock: END_BLOCK // You can also specify 'latest'          
    })                              
.then(events => console.log(events))
.catch((err) => console.error(err));
```
You can also specify the type of event your want to query. Let's use `Transfer` , since it is a required event for ERC-20 and ERC-721 tokens:

```javascript
contract.getPastEvents("Transfer",
//...
```
See the sample output for an event in [Appendix A: The content of an event](#appendix-a-the-content-of-an-event)

## Create an Event Listener for a Contract's Events

You can also create an event listening to upcoming events, and specify a callback that will occur when the event is emitted.

You can use `contract.events.allEvents()` to specify a callback for all events.

```javascript
contract.events.allEvents()
.on('data', (event) => {
	console.log(event);
})
.on('error', console.error);
```
See the sample output for an event in [Appendix A: The content of an event](#appendix-a-the-content-of-an-event)

## Create an Event Listener for a Specific Event Type

You can use `contract.events.EventName()` to specify actions for specific event types. For example, `Transfer`:

```javascript
contract.events.Transfer()
.on('data', (event) => {
	console.log(event);
})
.on('error', console.error);
```
See the sample output for an event below.

## Appendix A: The content of an event

### Sample output

```javascript
{ 
  removed: false, 
  logIndex: 53,
  transactionIndex: 65, 
  transactionHash: '0x778b776cb1a4998fb681081a79c4e2e8afea877644747cfc64e6dd36f6fda7f2',
  blockHash: '0x1eab83d59f65ab513681ad1314c4b334cca301def37e29ce098dfb293fd24181',
  blockNumber: 7907793,
  address: '0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2',
  id: 'log_0x03168f825cef626f45228ae375b310303c66b79ed364a46119e7e004794af27c',
  returnValues:
   { '0': '0xad9EB619Ce1033Cc710D9f9806A2330F85875f22',
     '1': '0x39755357759cE0d7f32dC8dC45414CCa409AE24e',
     '2': BigNumber { _hex: '0x01158e460913d00000' },
     src: '0xad9EB619Ce1033Cc710D9f9806A2330F85875f22',
     dst: '0x39755357759cE0d7f32dC8dC45414CCa409AE24e',
     wad: BigNumber { _hex: '0x01158e460913d00000' } },
  event: 'Transfer',
  signature: '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
  raw:
   { data: '0x000000000000000000000000000000000000000000000001158e460913d00000',
     topics:
      [ '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
        '0x000000000000000000000000ad9eb619ce1033cc710d9f9806a2330f85875f22',
        '0x00000000000000000000000039755357759ce0d7f32dc8dc45414cca409ae24e' ] 
   } 
}
```

### Properties

{: .table .table-bordered .table-dark}
| name             | type             | description                                                  |
| ---------------- | ---------------- | ------------------------------------------------------------ |
| id               | String           |                                                              |
| event            | String           | The event name                                               |
| signature        | String\|Null     | The event signature, null if it’s an anonymous event.        |
| address          | String           | Address this event originated from.                          |
| returnValues     | Object           | The return values coming from the event, e.g. `{from: '0x123...', to: '0x234...', amount: 100}`. |
| logIndex         | Number           | Integer of the event index position in the block.            |
| transactionIndex | Number           | Integer of the transaction’s index position the event was created in. |
| transactionHash  | 32 Bytes\|String | Hash of the transaction this event was created in.           |
| blockHash        | 32 Bytes\|String | Hash of the block this event was created in. null when it’s still pending. |
| blockNumber      | Number           | The block number this log was created in. null when still pending. |
| raw.data         | String           | The data containing non-indexed log parameter.               |
| raw.topics       | Array            | An array with max 4 32 Byte topics, topic 1-3 contains indexed parameters of the event. |


[^fn1]:  The `fromBlock` property argument is specified as optional in the [documentation](https://web3js.readthedocs.io/en/1.0/web3-eth-contract.html#id37), but in the current web3 version (1.0.0-beta.55) the method will fail without a filter object argument with this property.

