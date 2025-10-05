# Lesson 2 - Building HelloWorld.sol in Remix

## Coding HelloWorld.sol contract with Solidity

* Solidity philosophy
* OOP basics of Solidity
* Contract Structure
  * SPDX License Identifier
  * Pragmas
  * Imports
  * Comments
  * Contract definition
* Variables
* Storage areas
  * Account storage
  * Memory
  * Stack
* Constructor function
* Functions
* Visibility
* Typing
* Return values


# Solidity: Variables and Storage Areas



## 🧠 1. Understanding Variables in Solidity

A **variable** in Solidity is a container that holds data. Variables can store numbers, text, addresses, or complex data structures.  
Solidity is a **statically typed language**, meaning that every variable’s type must be declared explicitly.

### 🔹 Common Variable Types

| Type | Description | Example |
|------|--------------|----------|
| **State Variables** | Stored permanently on the blockchain; persist across function calls. | `uint public count;` |
| **Local Variables** | Exist temporarily inside a function; not stored on the blockchain. | `uint temp = 10;` |
| **Global Variables** | Predefined variables that give blockchain-related info. | `msg.sender`, `block.timestamp` |

### Example: Declaring Variables

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VariablesDemo {
    uint public stateVar = 42; // State variable

    function localExample() public pure returns (uint) {
        uint localVar = 5; // Local variable
        return localVar;
    }

    function globalExample() public view returns (address, uint) {
        return (msg.sender, block.timestamp); // Global variables
    }
}
~~~~

---

## ⚙️ 2. Storage Areas Overview

Solidity uses three distinct storage areas to manage data efficiently.  
Each one affects lifetime, accessibility, and gas cost.

| Storage Area | Lifetime | Scope | Gas Cost | Typical Usage |
|--------------|---------|-------|----------|---------------|
| **Storage** | Permanent | Contract-wide | High | State variables |
| **Memory** | Temporary (per function call) | Function | Medium | Temporary data |
| **Stack** | Very short-term | Operation-level | Very Low | Arithmetic & small values |

⚠️ **Tip:** Always choose the most efficient storage area for your needs to minimize gas costs.

---

## 💾 3. Account Storage (Persistent Storage)

Storage in Solidity refers to **permanent storage on the blockchain**.  
All contract state variables are stored here, meaning they persist after a transaction finishes.

### 🧩 How It Works

- Each contract has its own storage space divided into **32-byte slots**.  
- **Writing** or updating data in storage costs gas, as it changes the blockchain state.  
- **Reading** from storage costs less than writing, but still consumes gas.

### Example: Storage in Action

~~~~solidity
pragma solidity ^0.8.0;

contract StorageExample {
    uint256 public counter = 1;  // Stored in slot 0
    address public owner;        // Stored in slot 1

    constructor() {
        owner = msg.sender;
    }

    function increment() public {
        counter += 1; // Updates storage
    }
}
~~~~

### 🧮 Formula for Storage Slots

Each variable occupies one slot, identified as:

$$
\text{slot}_{address} = \text{keccak256}(i)
$$

where $$\(i\)$$ is the index of the variable in contract storage.

### Storage Layout Visualization

| Variable | Slot | Description |
|----------|------|------------|
| counter  | 0    | Unsigned integer value |
| owner    | 1    | Ethereum address of contract owner |

💡 **Optimization Tip:** Pack smaller variables (like `bool` or `uint8`) into a single storage slot to save gas.

---

## ⚡ 4. Memory (Temporary Workspace)

Memory is a **temporary data area** used while executing functions.  
When a function finishes execution, all data stored in memory is cleared.

### 📋 Key Points

- Used mainly for **function parameters** and temporary variables.  
- Does **not persist** between function calls.  
- Cheaper than storage but still costs gas.

### Example: Using Memory for Temporary Data

~~~~solidity
pragma solidity ^0.8.0;

contract MemoryExample {
    function greetUser(string memory name) public pure returns (string memory) {
        return string(abi.encodePacked("Hello, ", name, "!"));
    }
}
~~~~

Here:

- `name` is stored in **memory**.  
- The result string is created in memory and returned temporarily.

### 💡 Gas Cost Relation

$$
G_{\text{storage}} > G_{\text{memory}} > G_{\text{stack}}
$$

⚠️ **Common Pitfall:** Avoid assigning large arrays or strings to storage unless necessary — use memory for temporary data.

---

## 🔢 5. Stack (Fastest but Limited)

The stack stores **small and simple values** for quick access.  
It is **very fast** but limited — only 1024 slots are available.

### 🧠 Key Characteristics

- Used for local variables, function arguments, and intermediate calculations.  
- Operates as **LIFO** (Last In, First Out).  
- Cannot hold complex types like structs or arrays.

### Example: Stack Operations

~~~~solidity
pragma solidity ^0.8.0;

contract StackExample {
    function add(uint a, uint b) public pure returns (uint) {
        uint result = a + b; // Stored in stack
        return result;
    }
}
~~~~

⚠️ **Stack Overflow Warning:**  
When the stack exceeds 1024 items, the compiler throws an error:

~~~~text
Stack too deep, try removing local variables.
~~~~

🧩 **Tip:** Use fewer local variables or restructure your code if you encounter this issue.

---

## 🔬 6. Comparing Storage, Memory, and Stack

| Feature | Storage | Memory | Stack |
|---------|--------|--------|-------|
| Lifetime | Permanent | Temporary | Operation-level |
| Persistence | Yes (on blockchain) | No | No |
| Gas Cost | High | Medium | Very Low |
| Scope | Contract | Function | Internal |
| Typical Use | State variables | Function parameters | Calculations |
| Data Type | Complex & persistent | Temporary | Simple values |

💡 **Summary:**  
Use **storage** for persistent state, **memory** for temporary data, and **stack** for calculations.

---

## 🧩 7. Practical Example — Combining All Three

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

/// @title DataAreas - Demonstrating storage, memory, and stack usage
contract DataAreas {
    string public storedData = "Persistent Data"; // Storage

    function processData(string memory input) public view returns (string memory) {
        string memory result = string(abi.encodePacked(storedData, " + ", input)); // Memory
        return result;
    }

    function add(uint a, uint b) public pure returns (uint) {
        uint result = a + b; // Stack
        return result;
    }
}
~~~~

- `storedData` → stored permanently (storage).  
- `input` and `result` → temporary during execution (memory).  
- `a`, `b`, `result` → computed instantly (stack).

---

## 🧮 8. Gas Cost Equation Summary

Let:

$$
C_s = \text{cost of storage operation} \\
C_m = \text{cost of memory operation} \\
C_t = \text{cost of stack operation}
$$

Then:

$$
C_s \gg C_m \gg C_t
$$

Efficient smart contracts minimize \(C_s\) whenever possible.

---

## 🧭 9. Key Takeaways

| Concept | Summary |
|---------|--------|
| Storage | Permanent, costly, ideal for persistent contract state |
| Memory  | Temporary, cheaper, used for local computations |
| Stack   | Very fast, limited, used for small data |
| Optimization Rule | Minimize writes to storage, prefer memory for temporary data |

✅ **Pro Tip:** Use memory or stack for data that doesn’t need to persist after execution to save gas.

---

## 🧩 10. Visual Summary

~~~~text
+--------------------------------------------------------+
|                        STORAGE                         |
| (Permanent - Blockchain state variables)               |
+--------------------------------------------------------+
|                         MEMORY                         |
| (Temporary - Function execution data)                  |
+--------------------------------------------------------+
|                          STACK                         |
| (Instant - Calculations and local values)              |
+--------------------------------------------------------+
~~~~

---

🏁 **Conclusion**

By mastering **variables** and **storage areas**, you now understand:

- How Solidity organizes and manages data.  
- The cost implications of storage choices.  
- Best practices for efficient, gas-optimized code.

Next, we’ll explore **functions**, **visibility**, and **OOP structures** in Solidity.


## Detailed contract structure

* (Review) Contract structure
* (Review) Variables
* (Review) Storage
* What are objects (contracts and interfaces)
* Multiple objects per file

### References for Solidity and Smart Contracts

<https://docs.soliditylang.org/en/latest/grammar.html>

<https://docs.soliditylang.org/en/latest/structure-of-a-contract.html>

<https://docs.soliditylang.org/en/latest/contracts.html#visibility-and-getters>

<https://docs.soliditylang.org/en/latest/contracts.html#functions>

<https://docs.soliditylang.org/en/latest/contracts.html#interfaces>

### Code Reference

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

    function setText(string memory newText) public {
        text = newText;
    }
}
```

## Contract interaction

### Part 1

* Contract interactions using virtual environments in Remix
* Viewing state changes through functions

### Part 2

* State changing calls in public testnets
* Using interfaces to interact with other contracts

```solidity
interface HelloWorldInterface {
    function helloWorld() external view returns (string memory);
    function setText(string memory newText) external;
}

contract HelloWorld is HelloWorldInterface {
    ...
}
```

### Part 3

* Payable functions
* Experimenting with payable calls

```solidity
function setText(string memory newText) public payable {
    text = newText;
}
```

## Clean code and documentation

* The Style guide
* Documentation
* NatSpec Format

### References for clean code and documentation

<https://docs.soliditylang.org/en/latest/style-guide.html>

<https://docs.soliditylang.org/en/latest/natspec-format.html#natspec>

