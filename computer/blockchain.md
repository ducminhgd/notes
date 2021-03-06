# Blockchain

- [[MetaMask]] uses [[Infura]] as a node provider.
- [Ethereum Improvement Proposals](https://eips.ethereum.org/)

## What is ABI?

[[ABI]] (Application Binary Interface) in the context of computer science is an interface between two program modules, often between operating systems and user programs.

[[EVM]] ([[Ethereum]] Virtual Machine) is the core component of the Ethereum network, and smart contract is pieces of code stored on the Ethereum blockchain which are executed on EVM. Smart contracts written in high-level languages like [[Solidity]] or [[Vyper]] need to be compiled in EVM executable bytecode; when a smart contract is deployed, this bytecode is stored on the blockchain and is associated with an address

The [[JSON]] format of a contract's ABI is given by various functions and/or events descriptions.

Following are the elements present in the ABI description of a function:

- **type**: Defines the type of function. It can be one of the following, 'function', 'constructor', 'receive' (for receive ether function), or 'fallback' (for default function).
- **name**: Defines the name of the function.
- **inputs**: It is an array of objects which defines parameters; each object has:
  - **name**: Defines the name of the parameters.
  - **type**: Defines the canonical types of the parameters. For example, `uint256`.
  - **components**: Used to define tuple types, if a tuple type is reached, it is represented as type = tuple [other properties of tuple elements like name, type goes here].
- **outputs**: It is an array of output objects similar to inputs.
- **stateMutability**: Defines the mutability of a function. It can be one of the following values: '[[pure]]' (specified not to read or write blockchain state), '[[view]]' (specified when blockchain state is to be read, but no modification can be done), '[[nonpayable]]' (this is the default mutability and doesn't need to be mentioned while writing a function in code, this means a function does not accept Ether; using this we can read and write blockchain state), `payable` (mentioning this means a function accepts Ether and can read/write blockchain state).
- **payable**: true if function accepts Ether, false otherwise;
- **constant**: true if function is either `pure` or `view`, false otherwise.

**How to get/generate ABI?**

One of the most common ways is to copy the ABI using the ABI button under compile tab of [Ethereum REMIX IDE](https://remix.ethereum.org/) after the smart contract has complied. 

[//begin]: # "Autogenerated link references for markdown compatibility"
[Solidity]: solidity "Solidity"
[//end]: # "Autogenerated link references"