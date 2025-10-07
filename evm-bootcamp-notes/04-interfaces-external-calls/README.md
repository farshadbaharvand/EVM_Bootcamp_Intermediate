# Lesson 4 - Interfaces and external calls

## External calls

* What is a "MethodID"
* Fallback and receive functions
* External calls




# Lesson 4 – Interfaces and External Calls

## Overview

In Solidity, **external calls** allow smart contracts to communicate with **other contracts** or **external accounts** on the blockchain. These interactions are fundamental to building **composable**, **modular**, and **interconnected decentralized applications (dApps)**.

This lesson covers:
- How external calls work in Solidity.
- The concept of a **Method ID**.
- The use of **fallback** and **receive** functions.
- How to **safely and effectively perform external calls**.

---

## 1. What is a Method ID?

When calling a function in another contract, Solidity encodes the function call into **binary data** that the Ethereum Virtual Machine (EVM) can interpret.

### Step 1 – Function Selector

Every function has a **unique identifier**, known as the **Method ID** or **function selector**.  
It is derived as the **first 4 bytes** of the **Keccak-256 hash** of the function signature.

### Formula

$$
\text{MethodID} = \text{first 4 bytes of keccak256("functionName(parameterTypes)")}
$$

### Example

Suppose we have the function:
~~~~solidity
function transfer(address recipient, uint256 amount)
~~~~

The **Method ID** is calculated as:

1. Create the function signature string: `"transfer(address,uint256)"`
2. Hash it with Keccak-256: `keccak256("transfer(address,uint256)")`
3. Take the **first 4 bytes** → `0xa9059cbb`

> 🧠 **Tip:** This is the same selector used in the ERC20 `transfer()` function.

### Low-Level Call Example

~~~~solidity
pragma solidity ^0.8.0;

contract TokenCaller {
    function callTransfer(address token, address to, uint amount) public returns (bool) {
        bytes4 methodId = bytes4(keccak256("transfer(address,uint256)"));
        (bool success, ) = token.call(abi.encodeWithSelector(methodId, to, amount));
        require(success, "Transfer failed");
        return success;
    }
}
~~~~

---

## 2. Fallback and Receive Functions

Contracts in Solidity can have **special functions** that handle **Ether transfers** and **unknown calls**.

| Function | Purpose | Trigger Condition | Keyword |
|-----------|----------|------------------|----------|
| `receive()` | Handles plain Ether transfers (no data). | When a contract receives Ether with empty calldata. | `receive()` |
| `fallback()` | Handles calls with unknown function signatures or non-empty calldata. | When a contract is called with data but no matching function. | `fallback()` |

### Example: Basic Ether Receiver

~~~~solidity
pragma solidity ^0.8.0;

contract Receiver {
    event Received(address sender, uint value);
    event FallbackCalled(address sender, uint value, bytes data);

    receive() external payable {
        emit Received(msg.sender, msg.value);
    }

    fallback() external payable {
        emit FallbackCalled(msg.sender, msg.value, msg.data);
    }
}
~~~~

### Explanation

- **receive()** → triggered when the contract receives plain Ether (`address(this).balance` increases).  
- **fallback()** → triggered when the function selector does not match any defined function.

> ⚠️ **Warning:** Always mark them as `payable` if you want the contract to accept Ether.

---

## 3. External Calls

An **external call** is when one contract executes a function of another contract or sends Ether to it.

### Types of External Calls

| Type | Description | Common Usage |
|------|--------------|---------------|
| `.call()` | Low-level call for arbitrary function execution. Returns (success, data). | Generic calls and interface simulation. |
| `.delegatecall()` | Executes another contract’s code **in the caller’s context**. | Proxy patterns and upgradeable contracts. |
| `.staticcall()` | Executes another contract’s function **without modifying state**. | Read-only calls. |
| `.send()` | Sends Ether with limited gas and returns a boolean. | Simple Ether transfers (discouraged). |
| `.transfer()` | Sends Ether and reverts on failure (deprecated due to gas issues). | Older Ether transfer method. |

---

## 4. Example – Making External Calls

~~~~solidity
pragma solidity ^0.8.0;

interface ICounter {
    function increment() external;
    function getCount() external view returns (uint);
}

contract ExternalCaller {
    function callIncrement(address counter) public {
        ICounter(counter).increment();
    }

    function callGetCount(address counter) public view returns (uint) {
        return ICounter(counter).getCount();
    }
}
~~~~

### Explanation

1. We define an **interface** `ICounter` that describes the external contract functions.
2. We call the external contract via the interface using `ICounter(address).functionName()`.
3. Solidity automatically handles encoding, Method ID creation, and decoding of returned data.

---

## 5. Low-Level External Calls

You can also use **low-level calls** for more control:

~~~~solidity
pragma solidity ^0.8.0;

contract LowLevelCaller {
    function safeExternalCall(address target, bytes memory data) public returns (bool, bytes memory) {
        (bool success, bytes memory result) = target.call(data);
        require(success, "External call failed");
        return (success, result);
    }
}
~~~~

> ⚠️ **Warning:** Low-level calls **bypass type checking** and can be **dangerous**.  
> Always validate inputs and outputs to prevent reentrancy or data corruption.

---

## 6. Visual Diagram – External Call Flow

```
┌──────────────────────┐
│   Contract A (Caller) │
│  ───────────────────  │
│  Calls →              │
│  target.call(data)    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Contract B (Target) │
│  Receives call        │
│  Executes function    │
│  Returns data/status  │
└──────────────────────┘
```

---

## 7. Best Practices for External Calls

### ✅ Do:
- Use **interfaces** for external contract interactions.
- Handle **return values** from `.call()` safely.
- Always validate **inputs** and **outputs**.

### ❌ Don’t:
- Don’t use `tx.origin` for authorization.
- Don’t rely on `transfer()` (use `call{value: amount}("")` instead).
- Don’t assume external calls are safe — they can trigger **reentrancy**.

---

## 8. Example – Safe Ether Transfer via External Call

~~~~solidity
pragma solidity ^0.8.0;

contract SafeTransfer {
    function sendEther(address payable recipient) public payable {
        (bool success, ) = recipient.call{value: msg.value}("");
        require(success, "Transfer failed");
    }
}
~~~~

> 💡 **Tip:** Using `.call{value: ...}("")` is the recommended modern way to transfer Ether safely.  
> It provides **full gas forwarding** and is **compatible with most smart contracts**.

---

## 9. Summary Table

| Feature | Description | Key Variables / Methods |
|----------|--------------|--------------------------|
| **Method ID** | 4-byte function identifier | `keccak256(signature)` |
| **receive()** | Handles plain Ether transfers | `msg.value`, `msg.sender` |
| **fallback()** | Handles unknown or invalid calls | `msg.data`, `msg.sig` |
| **.call()** | Low-level function call | `(bool success, bytes data)` |
| **Interface Call** | High-level typed external call | `Interface(address).func()` |

---

## Final Notes

External calls are **essential for contract interoperability** in Ethereum.  
Mastering them allows developers to build modular, reusable, and secure systems.

> ⚠️ **Security Reminder:** Always protect your contracts against **reentrancy** and **malicious external calls** using checks-effects-interactions pattern or the `ReentrancyGuard` modifier.


--
### External Call References

<https://solidity-by-example.org/function-selector/>

<https://docs.soliditylang.org/en/latest/contracts.html#receive-ether-function>

<https://docs.soliditylang.org/en/latest/control-structures.html#external-function-calls>



# Guide: Using Interfaces in Solidity

---

## Table of Contents

1. [Introduction](#introduction)
2. [Interfaces and External Calls](#interfaces-and-external-calls)
3. [Fallback and Receive Functions](#fallback-and-receive-functions)
4. [Attaching “Mismatched” Contracts](#attaching-mismatched-contracts)
5. [Understanding Method IDs](#understanding-method-ids)
6. [Access Control](#access-control)
7. [Example: HelloWorld Contract](#example-helloworld-contract)
8. [Contract Interaction Walkthrough](#contract-interaction-walkthrough)
9. [Extra Reference Material](#extra-reference-material)
10. [Homework](#homework)
11. [Weekend Project](#weekend-project)

---

## Introduction

In Solidity, **interfaces** define the *external function signatures* that a contract must implement — similar to an abstract contract, but with stricter rules.  
They enable **interaction with other contracts** without needing the entire contract’s source code.

> 💡 **Key Idea:** Interfaces make smart contracts interoperable and modular.

---

## Interfaces and External Calls

An **interface** allows you to interact with another contract by specifying only its external functions.

### Example: Creating an Interface

~~~~solidity
interface IHelloWorld {
    function helloWorld() external view returns (string memory);
    function setText(string calldata newText) external;
    function transferOwnership(address newOwner) external;
}
~~~~

You can use this interface to call a deployed contract:

~~~~solidity
contract InterfaceCaller {
    IHelloWorld public hw;

    constructor(address _helloWorldAddress) {
        hw = IHelloWorld(_helloWorldAddress);
    }

    function readText() public view returns (string memory) {
        return hw.helloWorld();
    }

    function changeText(string calldata newText) public {
        hw.setText(newText);
    }
}
~~~~

> ⚙️ **Note:** The interface must exactly match the function signatures of the deployed contract. Otherwise, the transaction may revert or hit a fallback function.

---

## Fallback and Receive Functions

When your contract receives a call that **does not match any existing function signature**, the **fallback function** is triggered.

### Example

~~~~solidity
contract FallbackExample {
    event Log(string message, address sender, uint amount);

    fallback() external payable {
        emit Log("Fallback triggered", msg.sender, msg.value);
    }

    receive() external payable {
        emit Log("Receive triggered", msg.sender, msg.value);
    }
}
~~~~

| Function | Trigger Condition | Can Receive ETH? | Gas Cost |
|-----------|------------------|------------------|----------|
| `receive()` | When `msg.data` is empty | ✅ Yes | Low |
| `fallback()` | When `msg.data` is not empty or `receive()` not present | ✅ Yes | Slightly higher |

> ⚠️ **Warning:** Avoid using fallback for complex logic — keep it minimal to prevent reentrancy or denial-of-service attacks.

---

## Attaching “Mismatched” Contracts

Sometimes you may attach an interface to a contract that **does not fully match**.  
This happens, for example, when an interface expects a function that does not exist in the target contract.

~~~~solidity
interface IMismatch {
    function notExist() external;
}

contract AttachMismatch {
    function test(address target) public {
        IMismatch wrong = IMismatch(target);
        wrong.notExist(); // This will revert at runtime!
    }
}
~~~~

> 🧩 **Concept:** Solidity uses the **Method ID** (first 4 bytes of keccak256 hash of the function signature) to route calls. If no match is found, fallback is executed.

---

## Understanding Method IDs

Every function in Solidity generates a **unique identifier**, called a *Method ID* or *Function Selector*.

$$
\text{MethodID} = \text{first 4 bytes of } keccak256(\text{functionSignature})
$$

Example:

~~~~js
keccak256("setText(string)") = 0x47b5c4f4a9b...
MethodID = 0x47b5c4f4
~~~~

If you call a function with a **wrong signature**, Ethereum tries to execute it using this MethodID — and if it doesn’t exist, the fallback function will handle it.

---

## Access Control

Solidity provides various **access restriction patterns** for contract security.

Common patterns include:

- **Ownable** (restricting functions to an owner)
- **Role-based access control**
- **Modifier-based control**

See:  
👉 [Solidity Access Control Patterns](https://docs.soliditylang.org/en/latest/common-patterns.html#restricting-access)

And for transaction-related variables:  
👉 [Block and Transaction Properties](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#block-and-transaction-properties)

---

## Example: HelloWorld Contract

~~~~solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {
    string private text;
    address public owner;

    modifier onlyOwner()
    {
        require (msg.sender == owner, "Caller is not the owner");
        _;
    }

    constructor() {
        text = "Hello World";
        owner = msg.sender;
    }

    function helloWorld() public view returns (string memory) {
        return text;
    }

    function setText(string calldata newText) public onlyOwner {
        text = newText;
    }

    function transferOwnership(address newOwner) public onlyOwner {
        owner = newOwner;
    }
}
~~~~

---

## Contract Interaction Walkthrough

### Step 1 — Deploy HelloWorld

1. Deploy `HelloWorld.sol`.
2. Copy its deployed address.

### Step 2 — Use an Interface to Interact

~~~~solidity
interface IHelloWorld {
    function helloWorld() external view returns (string memory);
    function setText(string calldata newText) external;
}

contract InteractHW {
    IHelloWorld public hw;

    constructor(address _addr) {
        hw = IHelloWorld(_addr);
    }

    function update(string calldata s) public {
        hw.setText(s);
    }
}
~~~~

### Step 3 — Observe Method IDs

Using the wrong function signature (for instance, `setTxt` instead of `setText`) will fail because the **Method ID** does not exist in the target contract.

### Step 4 — Observe Fallback Behavior

If the called Method ID doesn’t match, the contract’s **fallback** function executes instead — showing how Solidity routes external calls.

> 🔍 **Tip:** You can use [Etherscan](https://etherscan.io/) or Hardhat’s `console.log` to trace failed transactions and inspect revert reasons.

---

## Extra Reference Material

| Resource | Description |
|-----------|--------------|
| [Solidity Cheatsheet](https://docs.soliditylang.org/en/latest/cheatsheet.html) | Core syntax and reference sheet |
| [Solidity Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html) | Official best practices for style |
| [Learn Solidity in Y Minutes](https://learnxinyminutes.com/docs/solidity/) | Quick language overview |
| [Learn Typescript in Y Minutes](https://learnxinyminutes.com/docs/typescript/) | For frontend integration |

---

## Homework

Prepare your environment for next week:

- Install [Node v20 LTS](https://nodejs.org/en/docs/guides/getting-started-guide/)
- Install [NPM](https://docs.npmjs.com/cli/v9/configuring-npm/install)
- Install [Yarn](https://yarnpkg.com/getting-started/install) and/or [Bun](https://bun.sh/docs/installation)
- Install [Git CLI](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
- Install [VS Code](https://code.visualstudio.com/docs/setup/setup-overview)
- Create free accounts on:
  - [Infura](https://infura.io/)
  - [Alchemy](https://www.alchemy.com/)
  - [Etherscan](https://etherscan.io/register)

---

## Weekend Project

🎯 **Objective:** Interact with “HelloWorld.sol” in your group.

1. Call `setText()` to change the message string.
2. Use `transferOwnership()` to change ownership.
3. Observe transaction hashes and gas costs.
4. Analyze revert reasons for failed transactions.
5. Document your observations (what worked, what failed, why).

---




## Using interfaces

* Fallback and receive functions
* External calls
* Attaching "mismatched" contracts
* Why some functions work and others don't because of the "MethodID"

### Access Control References

<https://docs.soliditylang.org/en/latest/common-patterns.html#restricting-access>

<https://docs.soliditylang.org/en/latest/units-and-global-variables.html#block-and-transaction-properties>

### Implementation Examples

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {
    string private text;
    address public owner;

    modifier onlyOwner()
    {
        require (msg.sender == owner, "Caller is not the owner");
        _;
    }

    constructor() {
        text = "Hello World";
        owner = msg.sender;
    }

    function helloWorld() public view returns (string memory) {
        return text;
    }

    function setText(string calldata newText) public onlyOwner {
        text = newText;
    }

    function transferOwnership(address newOwner) public onlyOwner {
        owner = newOwner;
    }
}
```

## Contract interaction

* Interacting with previously deployed contracts using interfaces
* Inspecting "MethodId" mismatch and fallback functions
* Inspecting execution errors based on assertions
* Fixing the interfaces
* Interacting with other peoples contract

## Extra reference material

* Handy reference sheet: [Solidity Cheatsheet](https://docs.soliditylang.org/en/latest/cheatsheet.html)
* Overall style guide: [Solidity Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html)
* Solidity quick guide: [Learn Solidity in Y minutes](https://learnxinyminutes.com/docs/solidity/)
* Typescript quick guide: [Learn Typescript in Y minutes](https://learnxinyminutes.com/docs/typescript/)

---

## Homework

* Prepare your environment for next week:
  * Install [Node](https://nodejs.org/en/docs/guides/getting-started-guide/) v20 LTS
  * [NPM](https://docs.npmjs.com/cli/v9/configuring-npm/install)
  * [Yarn](https://yarnpkg.com/getting-started/install) and/or [Bun](https://bun.sh/docs/installation)
  * [Git CLI](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
  * [VS Code](https://code.visualstudio.com/docs/setup/setup-overview)
* Create a free account on [Infura](https://infura.io/)
* Create a free account on [Alchemy](https://www.alchemy.com/)
* Create a free account on [Etherscan](https://etherscan.io/register)

## Weekend Project

* Interact with "HelloWorld.sol" within your group to change message strings and change owners
* Consider each function execution and the transaction hash, if successful, or the revert reason, if failed
