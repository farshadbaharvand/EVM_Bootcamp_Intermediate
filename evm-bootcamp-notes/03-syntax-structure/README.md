# Lesson 3 - Syntax and structure

## Important concepts

* Storage locations
  * Account storage
* Replacing memory with calldata for saving gas
* Definitions
  * Visibility
  * State mutability
  * Modifiers
  * Virtual
  * Override



# Lesson 3 - Syntax and Structure

---

## Overview

In this lesson, we’ll explore the **core syntax and structure** of Solidity smart contracts. Understanding these foundational elements is essential before writing complex decentralized applications. We’ll focus on *storage locations*, *function visibility*, *state mutability*, and key modifiers such as **virtual** and **override**.

---

## 1. Storage Locations

Solidity provides three primary **data storage locations**, each optimized for specific use cases. These are **storage**, **memory**, and **calldata**.

| Location | Persistence | Cost | Typical Use |
|-----------|--------------|------|--------------|
| `storage` | Persistent (on-chain) | High gas | State variables |
| `memory`  | Temporary (during function execution) | Medium | Function-local variables |
| `calldata` | Temporary (read-only, external calls only) | Lowest | Function parameters |

### 1.1 Account Storage

**Account storage** refers to data stored *permanently* on the blockchain within a contract’s state. This is the most expensive form of storage, as each write operation consumes significant gas.

Example:

~~~~solidity
// Demonstrating persistent storage
pragma solidity ^0.8.20;

contract StorageExample {
    uint public storedNumber; // Stored permanently on-chain

    function setNumber(uint _number) public {
        storedNumber = _number; // Modifies blockchain state
    }
}
~~~~

🧠 **Note:** Changes to `storage` persist across transactions. Any value assigned to a `storage` variable updates the contract’s permanent state.

---

## 2. Replacing `memory` with `calldata` for Gas Efficiency

When writing **external functions**, replacing `memory` with `calldata` can significantly reduce gas consumption. This is because `calldata` references data directly from the transaction payload rather than copying it to memory.

### Example

~~~~solidity
pragma solidity ^0.8.20;

contract GasEfficient {
    // Less efficient version
    function getSumMemory(uint[] memory numbers) public pure returns (uint) {
        uint sum = 0;
        for (uint i = 0; i < numbers.length; i++) {
            sum += numbers[i];
        }
        return sum;
    }

    // More efficient version using calldata
    function getSumCalldata(uint[] calldata numbers) external pure returns (uint) {
        uint sum = 0;
        for (uint i = 0; i < numbers.length; i++) {
            sum += numbers[i];
        }
        return sum;
    }
}
~~~~

⚡ **Tip:** Use `calldata` whenever possible for **read-only** parameters in **external** functions to save gas.

---

## 3. Function Definitions

Functions in Solidity define the **behavior** of a contract. Their signatures consist of **visibility**, **state mutability**, and optionally **modifiers**.

### General Syntax

~~~~solidity
function functionName(parameters) visibility stateMutability modifiers returns (returnType) {
    // Function logic
}
~~~~

---

## 4. Visibility

Visibility determines **who** can call a function or access a variable. There are four main visibility specifiers:

| Visibility | Accessible from | Description |
|-------------|----------------|--------------|
| `public` | Everywhere | Default for functions; accessible externally and internally |
| `external` | Only outside contracts | Efficient for large input data (uses `calldata`) |
| `internal` | Within the same contract and derived contracts | Not accessible from outside |
| `private` | Only within the same contract | Not inherited |

Example:

~~~~solidity
pragma solidity ^0.8.20;

contract VisibilityExample {
    uint private secret = 42;
    uint internal sharedValue = 10;
    uint public visibleValue = 5;

    function revealSecret() private view returns (uint) {
        return secret;
    }

    function getShared() internal view returns (uint) {
        return sharedValue;
    }

    function getVisible() public view returns (uint) {
        return visibleValue;
    }
}
~~~~

📘 **Note:** Using the correct visibility ensures **security** and **proper encapsulation** of logic.

---

## 5. State Mutability

State mutability specifiers describe **how** a function interacts with blockchain state.

| Specifier | Description |
|------------|--------------|
| `pure` | Cannot read or modify state variables |
| `view` | Can read but not modify state variables |
| `payable` | Allows receiving Ether |
| *(none)* | Can both read and modify state variables |

Example:

~~~~solidity
pragma solidity ^0.8.20;

contract MutabilityExample {
    uint public value = 100;

    function getValue() public view returns (uint) {
        return value;
    }

    function calculate(uint x, uint y) public pure returns (uint) {
        return x + y;
    }

    function deposit() public payable {
        // Allows Ether transfer
    }
}
~~~~

⚠️ **Warning:** If a function is `pure` or `view`, modifying the state within it will result in a **compilation error**.

---

## 6. Modifiers

**Modifiers** are reusable code snippets that can be applied to functions to alter their behavior — often used for **access control** or **preconditions**.

Example:

~~~~solidity
pragma solidity ^0.8.20;

contract ModifierExample {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        _; // Executes the rest of the function
    }

    function secureAction() public onlyOwner {
        // Restricted to owner
    }
}
~~~~

🧩 **Key Term:** The underscore (`_;`) indicates where the **function body** will be inserted during compilation.

---

## 7. Virtual and Override

When dealing with **inheritance**, Solidity requires explicit keywords to define and override functions across contracts.

- **`virtual`** → Marks a function as overridable.  
- **`override`** → Indicates that a derived contract overrides a parent function.

### Example: Inheritance with Virtual and Override

~~~~solidity
pragma solidity ^0.8.20;

contract Parent {
    function greet() public virtual pure returns (string memory) {
        return "Hello from Parent";
    }
}

contract Child is Parent {
    function greet() public override pure returns (string memory) {
        return "Hello from Child";
    }
}
~~~~

🧠 **Note:** Both parent and child functions must have **identical signatures** for a successful override.

---

## 8. Putting It All Together

Let’s combine all these concepts into a practical example.

~~~~solidity
pragma solidity ^0.8.20;

contract LessonThreeExample {
    string private message;

    constructor(string memory initialMessage) {
        message = initialMessage;
    }

    modifier nonEmpty(string memory newMessage) {
        require(bytes(newMessage).length > 0, "Message cannot be empty");
        _;
    }

    function setMessage(string calldata newMessage) external nonEmpty(newMessage) {
        message = newMessage;
    }

    function getMessage() public view returns (string memory) {
        return message;
    }
}
~~~~

✅ **Summary of Key Takeaways:**

- **Storage:** Persistent data on the blockchain.  
- **Memory & Calldata:** Temporary, gas-efficient alternatives.  
- **Visibility:** Controls access to functions and variables.  
- **State Mutability:** Defines read/write permissions.  
- **Modifiers:** Reusable access or validation logic.  
- **Virtual/Override:** Used for inheritance and extensibility.

---

## Further Reading

- [Solidity Documentation: Functions and Modifiers](https://docs.soliditylang.org/en/latest/contracts.html)
- [Solidity by Example](https://solidity-by-example.org/)
- [Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html)

---

🧭 **Next Steps:**  
In the next lesson, we’ll apply these principles to **contract interaction and modularity**, combining multiple smart contracts through interfaces and inheritance.



### Solidity Concepts References

<https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#accounts>

<https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#storage-memory-and-the-stack>

<https://docs.soliditylang.org/en/latest/types.html#bytes-and-string-as-arrays>

### Code Examples

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {
    string private text;

    constructor() {
        text = "Hello World";
    }

    function helloWorld() public view returns (string memory)  {
        return text;
    }

    function setText(string calldata newText) public {
        text = newText;
    }
}
```

### Variable and Function References

<https://docs.soliditylang.org/en/latest/types.html>

<https://docs.soliditylang.org/en/latest/contracts.html#visibility-and-getters>

<https://docs.soliditylang.org/en/latest/contracts.html#functions>

<https://docs.soliditylang.org/en/latest/cheatsheet.html#mathematical-and-cryptographic-functions>

## Exercises

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {
    string private text;

    constructor() {
        text = initialText();
    }
    
    function helloWorld() public view returns (string memory) {
        return text;
    }

    function setText(string calldata newText) public {
        text = newText;
    }

    function initialText() public pure returns (string memory) {
        return "Hello World";
    }

    function _isUnchanged() internal view returns (bool) {
        return keccak256(bytes(text)) == keccak256(bytes(initialText()));
    }

    function textHasChanged() public view returns (bool returnValue_) {
        returnValue_ = !_isUnchanged();
    }

    function _restore() internal {
        text = initialText();
    }
    
    function restore() public returns (bool) {
        if (_isUnchanged()) return false;
        _restore();
        return true;
    }
}
```

## Assertion and Modifiers

* How errors are handled on solidity (briefly)
* Assertion
* Require statements
* Modifiers
* Where to use modifiers


# 🧱 Solidity Guide — Assertion and Modifiers

## 📘 Overview

This lesson covers **error handling** in Solidity, focusing on **assertions**, **require statements**, and **modifiers**.  
These concepts are essential for writing **secure**, **readable**, and **gas-efficient** smart contracts.

---

## ⚙️ How Errors Are Handled in Solidity

In Solidity, **error handling** ensures that contracts behave predictably when something goes wrong.  
When an error occurs, **all state changes are reverted**, and unused gas is refunded (except in some cases like `assert`).

### Main Error Handling Mechanisms

| Function | Purpose | Gas Refund | Use Case |
|-----------|----------|-------------|-----------|
| `require()` | Checks **external conditions** such as user input or function preconditions. | ✅ Yes | Input validation, access control |
| `revert()` | Manually triggers a revert with a custom message. | ✅ Yes | For complex conditions or nested checks |
| `assert()` | Verifies **internal logic** and invariants. | ❌ No | Detect internal bugs, never user input |

> 💡 **Tip:** Use `require()` for user errors and `assert()` for internal consistency checks.

---

## 🧩 Assertion

### Definition

The `assert()` statement is used to test **invariants** — conditions that should *never* fail if the contract is correct.

If an `assert` fails:
- The **transaction reverts**.
- The **remaining gas is consumed**.
- It signals a **serious logic error** in your contract.

### Example

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AssertionExample {
    uint public totalSupply = 1000;

    function burn(uint amount) public {
        totalSupply -= amount;
        // Assert internal consistency
        assert(totalSupply <= 1000);
    }
}
~~~~

In this example, if `totalSupply` accidentally exceeds `1000`, the assertion fails, signaling a **programming bug**.

> ⚠️ **Warning:** Never use `assert()` for user input validation — it will consume all gas.

---

## ✅ Require Statements

### Definition

The `require()` statement checks **preconditions** before executing code.  
If the condition fails, it **reverts** the transaction and **refunds unused gas**.

### Syntax

~~~~solidity
require(condition, "Error message");
~~~~

### Example

~~~~solidity
pragma solidity ^0.8.0;

contract RequireExample {
    uint public balance;

    function deposit(uint amount) public {
        require(amount > 0, "Deposit must be greater than zero");
        balance += amount;
    }

    function withdraw(uint amount) public {
        require(amount <= balance, "Insufficient balance");
        balance -= amount;
    }
}
~~~~

> 🧠 **Note:** Always provide clear, human-readable error messages — this helps in debugging and improves UX for dApps.

---

## 🧱 Modifiers

### What Are Modifiers?

Modifiers are **reusable code blocks** that can be attached to functions to change their behavior.  
They are often used for **access control**, **preconditions**, and **state checks**.

### Syntax

~~~~solidity
modifier modifierName(parameters) {
    // Optional preconditions
    _; // Function body executes here
    // Optional postconditions
}
~~~~

### Example — Basic Modifier

~~~~solidity
pragma solidity ^0.8.0;

contract ModifiersExample {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the contract owner");
        _;
    }

    function changeOwner(address newOwner) public onlyOwner {
        owner = newOwner;
    }
}
~~~~

In this example:
- The modifier `onlyOwner` restricts access to the `changeOwner` function.
- The `_` placeholder marks where the target function’s code executes.

> 💡 **Tip:** Modifiers help avoid repeating the same `require()` logic across multiple functions.

---

## 🧭 Where to Use Modifiers

Modifiers can enforce various **rules** in a contract:

| Modifier Purpose | Example | Description |
|-------------------|----------|--------------|
| **Access Control** | `onlyOwner` | Restrict certain functions to specific addresses |
| **Reentrancy Protection** | `nonReentrant` | Prevent multiple simultaneous function calls |
| **State Validation** | `whenNotPaused` | Ensure functions run only under specific conditions |
| **Timing Constraints** | `onlyAfter(time)` | Restrict execution to specific time windows |

### Example — Multiple Modifiers

~~~~solidity
pragma solidity ^0.8.0;

contract MultiModifiers {
    address public owner;
    bool public paused;

    constructor() {
        owner = msg.sender;
        paused = false;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        _;
    }

    modifier whenNotPaused() {
        require(!paused, "Contract is paused");
        _;
    }

    function togglePause() public onlyOwner {
        paused = !paused;
    }

    function safeAction() public onlyOwner whenNotPaused {
        // Execute only when the contract is active
    }
}
~~~~

> ⚙️ **Best Practice:** Combine modifiers for layered security — for example, ownership + contract state.

---

## 🧾 Summary Table

| Concept | Description | Common Use |
|----------|--------------|-------------|
| **assert()** | Tests for internal invariants. Consumes all gas on failure. | Internal logic checks |
| **require()** | Validates user input or external state. Refunds unused gas. | Access control, input validation |
| **modifier** | Defines reusable pre/post conditions for functions. | Restrict access, prevent reentrancy |

---

## 🧠 Key Takeaways

- Use `require()` for validating **inputs** and **conditions**.  
- Use `assert()` for **internal logic checks** that should *never* fail.  
- Use **modifiers** to make code **cleaner**, **more secure**, and **easier to maintain**.  
- Always include **clear error messages** for debugging and better UX.  

---

## 📚 References

- [Solidity Documentation — Error Handling](https://docs.soliditylang.org/en/latest/control-structures.html#error-handling-assert-require-revert-and-exceptions)  
- [Solidity Documentation — Function Modifiers](https://docs.soliditylang.org/en/latest/contracts.html#function-modifiers)

---

> ✨ **Pro Tip:** Keep modifiers simple and avoid nesting too many in a single function — clarity and readability are key to secure smart contracts.



### Error Handling References

<https://docs.soliditylang.org/en/latest/control-structures.html#error-handling-assert-require-revert-and-exceptions>

<https://docs.soliditylang.org/en/latest/structure-of-a-contract.html#function-modifiers>

## Blockchain Global Variables References

* Reserved words and global variables that a programmer should know
* Global variables about blockchain state
* Global variables about the transaction
* Global variables about the transaction message

# Lesson 4 – Blockchain Global Variables References

## Overview

In Solidity, developers can access a range of **global variables** and **reserved words** that provide critical information about the **blockchain**, **transactions**, and **message context**. These variables allow smart contracts to interact with the Ethereum Virtual Machine (EVM) environment and the blockchain state.

---

## 1. Reserved Words and Global Variables

Solidity has several **reserved words** that cannot be used as identifiers (such as variable names or function names).  
They form the core of Solidity’s interaction with the blockchain.

### Common Reserved Words

| Category | Examples | Description |
|-----------|-----------|-------------|
| Control Flow | `if`, `else`, `while`, `for`, `break`, `continue`, `return` | Control execution flow |
| Function Visibility | `public`, `private`, `internal`, `external` | Define access scope |
| Data Location | `storage`, `memory`, `calldata` | Specify where data is stored |
| Error Handling | `require`, `revert`, `assert` | Handle conditions and errors |
| State Mutability | `view`, `pure`, `payable` | Control state modification and Ether transfers |
| Object References | `msg`, `tx`, `block`, `this`, `super` | Global references to blockchain context |

> ⚠️ **Note:** Avoid using reserved words as variable names — it will cause compilation errors.

---

## 2. Global Variables About Blockchain State

These variables provide access to data about the **current block** being processed.

### `block` Object

| Variable | Type | Description |
|-----------|------|-------------|
| `block.number` | `uint` | Current block number |
| `block.timestamp` | `uint` | Unix timestamp (seconds) of the current block |
| `block.difficulty` | `uint` | Block difficulty (pre-merge, no longer used for randomness) |
| `block.gaslimit` | `uint` | Maximum gas allowed in this block |
| `block.coinbase` | `address payable` | Miner’s address who receives block rewards |
| `block.chainid` | `uint` | Chain ID (helps detect replay attacks) |
| `block.basefee` | `uint` | Base fee per gas introduced in EIP-1559 |

### Example
~~~~solidity
pragma solidity ^0.8.0;

contract BlockInfo {
    function getBlockDetails() public view returns (uint, uint, address, uint, uint) {
        return (
            block.number,
            block.timestamp,
            block.coinbase,
            block.gaslimit,
            block.chainid
        );
    }
}
~~~~

**Tip:**
Use block.timestamp for time-dependent logic, but avoid using it for randomness, since miners can manipulate it slightly (±15 seconds).


## 3. Global Variables About the Transaction

These variables expose metadata about the transaction itself — the envelope that contains the function call.

| Variable      | Type      | Description                                                    |
| ------------- | --------- | -------------------------------------------------------------- |
| `tx.origin`   | `address` | Original external account (EOA) that initiated the transaction |
| `tx.gasprice` | `uint`    | Gas price for the transaction                                  |
| `tx.value`    | `uint`    | Amount of Ether sent with the transaction (if any)             |

---

**Example**
~~~~solidity

pragma solidity ^0.8.0;

contract TransactionInfo {
    function getTransactionInfo() public view returns (address, uint) {
        return (tx.origin, tx.gasprice);
    }
}
~~~~

**Warning:**
Never use tx.origin for authentication.
It can be exploited via phishing attacks through intermediary contracts.
Instead, use msg.sender for verifying the caller.


## 4. Global Variables About the Message

The msg object provides information about the message call that triggered the current function execution.

| Variable     | Type      | Description                                                     |
| ------------ | --------- | --------------------------------------------------------------- |
| `msg.sender` | `address` | Address of the caller (EOA or contract)                         |
| `msg.value`  | `uint`    | Amount of Wei (1 Ether = 10¹⁸ Wei) sent with the call           |
| `msg.data`   | `bytes`   | Complete calldata (function selector + arguments)               |
| `msg.sig`    | `bytes4`  | First 4 bytes of `msg.data`, representing the function selector |


---

**Example**

~~~~solidity
pragma solidity ^0.8.0;

contract MessageInfo {
    function getMessageInfo() public payable returns (address, uint, bytes4) {
        return (msg.sender, msg.value, msg.sig);
    }
}


~~~~

## 5. Combining Global Variables

Let’s combine what we’ve learned to create a contract that logs who called a function, when, and from which block.


~~~~solidity

pragma solidity ^0.8.0;

contract Logger {
    event ActionLogged(address caller, uint blockNumber, uint timestamp, uint value);

    function logAction() public payable {
        emit ActionLogged(msg.sender, block.number, block.timestamp, msg.value);
    }
}


~~~~

**How It Works**

1. msg.sender → identifies the caller.

2. block.number → records the blockchain’s block height.

3. block.timestamp → notes the time of the transaction.

4. msg.value → records any Ether sent.

**Example Use Case**
Event logging for decentralized applications (DApps) such as audit trails, voting systems, or payment records.


## 6. Visual Overview

Here’s a simplified diagram showing how global variables relate to each other:

┌──────────────────────────────────────────┐
│              Blockchain State            │
│  block.number  block.timestamp           │
│  block.gaslimit block.chainid ...        │
└──────────────────────────────────────────┘
              │
              ▼
┌──────────────────────────────────────────┐
│              Transaction                 │
│  tx.origin   tx.gasprice                 │
└──────────────────────────────────────────┘
              │
              ▼
┌──────────────────────────────────────────┐
│              Message Call                │
│  msg.sender  msg.value  msg.data         │
└──────────────────────────────────────────┘

---

## 7. Summary Table

| Context     | Global Object | Common Variables                 | Use Case                  |
| ----------- | ------------- | -------------------------------- | ------------------------- |
| Blockchain  | `block`       | `number`, `timestamp`, `chainid` | Get blockchain-level info |
| Transaction | `tx`          | `origin`, `gasprice`             | Get transaction metadata  |
| Message     | `msg`         | `sender`, `value`, `data`        | Handle incoming calls     |

--- 

## 8. Best Practices and Warnings

**Do:**

Use msg.sender for identifying callers.

Use block.timestamp only for approximate timing.

Use block.chainid for cross-chain validation.

**Don’t:**

Use tx.origin for authentication.

Rely on block.timestamp for critical randomness or security logic.

Use deprecated variables like block.difficulty for randomness.

9. Practical Exercise
Task

Write a contract that:

1. Logs the caller’s address.

2. Emits the transaction gas price.

3. Includes the block number and timestamp.

**Skeleton Example**

~~~~solidity
pragma solidity ^0.8.0;

contract Exercise {
    event LogDetails(address sender, uint gasPrice, uint blockNum, uint time);

    function recordDetails() public {
        emit LogDetails(msg.sender, tx.gasprice, block.number, block.timestamp);
    }
}


~~~~


**Final Notes**

Understanding these global variables is essential for writing secure, efficient, and transparent smart contracts.
They form the bridge between on-chain data and your contract logic — enabling smart contracts to react to blockchain context in real time.

💡 Remember:
Mastering these variables helps you build reliable DApps that behave predictably across different execution environments.



---


<https://docs.soliditylang.org/en/latest/units-and-global-variables.html>

<https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#index-8>

<https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#blocks>


