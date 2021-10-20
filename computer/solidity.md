# Solidity

Solidity is a programming language used in [[Blockchain]].

Ethereum has the hash function `keccak256` built in, which is a version of [[SHA3]]. A hash function basically maps an input into a random 256-bit hexadecimal number. A slight change in the input will cause a large change in the hash.

## Gas

`Gas` refers to the computational efforts required to execute specific operations on the Ethereum network. A fee, paid in ether, is required to successfully conduct a transaction on Ethereum. Ethereum is the second-biggest blockchain network after Bitcoin.

### Struct packing to save gas

There are other types of uints: `uint8`, `uint16`, `uint32`, etc.

Normally there's no benefit to using these sub-types because Solidity reserves 256 bits of storage regardless of the uint size. For example, using `uint8` instead of uint (`uint256`) won't save you any gas.

But there's an exception to this: inside structs.

If you have multiple uints inside a struct, using a smaller-sized uint when possible will allow Solidity to pack these variables together to take up less storage

> Note: If a `view` function is called internally from another function in the same contract that is not a `view` function, it will still cost `gas`. This is because the other function creates a transaction on [[Ethereum]], and will still need to be verified from every node. So view functions are **only free when they're called externally**.

## Contract

Similiar to `Class` in some other programming languages. When a contract is deployed to Etherium, it cannot be changed.

[[Interface]] is empty Contract or emtpy function.

## Event

Events are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.

Example:

```javascript
// declare the event
event IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public returns (uint) {
  uint result = _x + _y;
  // fire an event to let the app know the function was called:
  emit IntegersAdded(_x, _y, result);
  return result;
}
```

Your app front-end could then listen for the event. A javascript implementation would look something like:

```javascript
YourContract.IntegersAdded(function(error, result) {
  // do something with result
})
```

## `msg.sender`

In Solidity, there are certain global variables that are available to all functions. One of these is `msg.sender`, which refers to the address of the person (or smart contract) who called the current function.

> Note: In Solidity, function execution always needs to start with an external caller. A contract will just sit on the blockchain doing nothing until someone calls one of its functions. So there will always be a `msg.sender`.

```javascript
mapping (address => uint) favoriteNumber;

function setMyNumber(uint _myNumber) public {
  // Update our `favoriteNumber` mapping to store `_myNumber` under `msg.sender`
  favoriteNumber[msg.sender] = _myNumber;
  // ^ The syntax for storing data in a mapping is just like with arrays
}

function whatIsMyNumber() public view returns (uint) {
  // Retrieve the value stored in the sender's address
  // Will be `0` if the sender hasn't called `setMyNumber` yet
  return favoriteNumber[msg.sender];
}
```

Using `msg.sender` gives you the security of the [[Ethereum]] [[blockchain]] — the only way someone can modify someone else's data would be to steal the private key associated with their Ethereum address.

## Functions

The above function doesn't actually change state in Solidity — e.g. it doesn't change any values or write anything.

So in this case we could declare it as a `view` function, meaning it's only viewing the data but not modifying it

```javascript
function sayHello() public view returns (string memory) {}
```

```python

```

Solidity also contains pure functions, which means you're not even accessing any data in the app. Consider the following:

```javascript
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

[[Pure]] and View functions don't cost [[Gas]].

In most programming languages, looping over large data sets is expensive. But in Solidity, this is way cheaper than using `storage` if it's in an external `view` function, since `view` functions don't cost your users any gas. (And gas costs your users real money!).

## Payable and Withdraw

`payable` is a special type of function, allows function to receive [[Ether]]

After you send Ether to a contract, it gets stored in the contract's Ethereum account, and it will be trapped there — unless you add a function to withdraw the Ether from the contract.

You can write a function to withdraw Ether from the contract as follows:

```javascript
contract GetPaid is Ownable {
  function withdraw() external onlyOwner {
    address payable _owner = address(uint160(owner()));
    _owner.transfer(address(this).balance);
  }
}
```

It is important to note that you cannot transfer Ether to an address unless that address is of type `address payable`. But the `_owner` variable is of type `uint160`, meaning that we must explicitly cast it to `address payable`.

## Function visibility

In addition to `public` and `private`, Solidity has two more types of visibility for functions: `internal` and `external`.

`internal` is the same as `private`, *except that it's also accessible to contracts that inherit from this contract*.

`external` is similar to `public`, *except that these functions can ONLY be called outside the contract* — they can't be called by other functions inside that contract

## Variables

**Storage** refers to variables stored permanently on the blockchain. **Memory** variables are temporary, and are erased between external function calls to your contract. Think of it like your computer's hard disk vs RAM.

Most of the time you don't need to use these keywords because Solidity handles them by default. State variables (variables declared outside of functions) are by default storage and written permanently to the blockchain, while variables declared inside functions are `memory` and will disappear when the function call ends.

## Address

The Ethereum blockchain is made up of accounts, which you can think of like bank accounts. An [[account]] has a balance of Ether (the currency used on the Ethereum blockchain), and you can send and receive Ether payments to other accounts, just like your bank account can wire transfer money to other bank accounts.

Each account has an [[address]], which you can think of like a bank account number. It's a unique identifier that points to that account, and it looks like this: `0x0cE446255506E92DF41614C46F1d6df9Cc969183`

## Mapping

```javascript
// For a financial app, storing a uint that holds the user's account balance:
mapping (address => uint) public accountBalance;
// Or could be used to store / lookup usernames based on userId
mapping (uint => string) userIdToName;
```

## Require

`require` makes it so that the function will throw an error and stop executing if some condition is not true

```javascript
function sayHiToVitalik(string memory _name) public returns (string memory) {
  // Compares if _name equals "Vitalik". Throws an error and exits if not true.
  // (Side note: Solidity doesn't have native string comparison, so we
  // compare their keccak256 hashes to see if the strings are equal)
  require(keccak256(abi.encodePacked(_name)) == keccak256(abi.encodePacked("Vitalik")));
  // If it's true, proceed with the function:
  return "Hi!";
}
```

Thus `require` is quite useful for verifying certain conditions that must be true before running a function.

## `modifier`

Modifiers are kind of half-functions that are used to modify other functions, usually to check some requirements prior to execution. In this case, `onlyOwner` can be used to limit access so only the owner of the contract can run this function. We'll talk more about function modifiers in the next chapter, and what that weird `_`; does.

A function modifier looks just like a function, but uses the keyword `modifier` instead of the keyword `function`. And it can't be called directly like a function can — instead we can attach the modifier's name at the end of a function definition to change that function's behavior.

Function modifiers with arguments:

```javascript
// A mapping to store a user's age:
mapping (uint => uint) public age;

// Modifier that requires this user to be older than a certain age:
modifier olderThan(uint _age, uint _userId) {
  require(age[_userId] >= _age);
  _;
}

// Must be older than 16 to drive a car (in the US, at least).
// We can call the `olderThan` modifier with arguments like so:
function driveCar(uint _userId) public olderThan(16, _userId) {
  // Some function logic
}
```

## Ownerable Contracts

[Readmore](https://cryptozombies.io/en/lesson/3/chapter/2)

## Time units

The variable `now` will return the current unix timestamp of the latest block (the number of seconds that have passed since January 1st 1970). The unix time as I write this is `1515527488`.

Solidity also contains the time units `seconds`, `minutes`, `hours`, `days`, `weeks` and `years`. These will convert to a `uint` of the number of seconds in that length of time. So 1 minutes is 60, 1 hours is 3600 (60 seconds x 60 minutes), 1 days is 86400 (24 hours x 60 minutes x 60 seconds), etc.

## Random Numbers

So how do we generate random numbers in Solidity? The real answer here is, you can't. Well, at least you can't do it safely.

The best source of randomness we have in Solidity is the `keccak256` hash function. We could do something like the following to generate a random number:

```javascript
// Generate a random number between 1 and 100:
uint randNonce = 0;
uint random = uint(keccak256(abi.encodePacked(now, msg.sender, randNonce))) % 100;
randNonce++;
uint random2 = uint(keccak256(abi.encodePacked(now, msg.sender, randNonce))) % 100;
```

What this would do is take the timestamp of `now`, the `msg.sender`, and an incrementing `nonce` (a number that is only ever used once, so we don't run the same hash function with the same input parameters twice).

It would then "pack" the inputs and use `keccak` to convert them to a random hash. Next, it would convert that hash to a `uint`, and then use `% 100` to take only the last 2 digits. This will give us a totally random number between 0 and 99.

**This method is vulnerable to attack by a dishonest node**

In Ethereum, when you call a function on a contract, you broadcast it to a node or nodes on the network as a [[transaction]]. The nodes on the network then collect a bunch of transactions, try to be the first to solve a computationally-intensive mathematical problem as a "Proof of Work", and then publish that group of transactions along with their Proof of Work (PoW) as a **block** to the rest of the network.

Once a node has solved the PoW, the other nodes stop trying to solve the PoW, verify that the other node's list of transactions are valid, and then accept the block and move on to trying to solve the next block.

This makes our random number function exploitable.

Let's say we had a coin flip contract — heads you double your money, tails you lose everything. Let's say it used the above random function to determine heads or tails. (`random >= 50` is heads, `random < 50` is tails).

If I were running a node, I could publish a transaction only to my own node and not share it. I could then run the coin flip function to see if I won — and if I lost, choose not to include that transaction in the next block I'm solving. I could keep doing this indefinitely until I finally won the coin flip and solved the next block, and profit.