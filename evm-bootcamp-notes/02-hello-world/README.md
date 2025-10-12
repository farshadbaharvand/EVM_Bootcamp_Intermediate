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


---

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

---


# Solidity: Constructor, Functions, Visibility, Typing, and Return Values



## 🏗 1. Constructor Function

A **constructor** is a special function in Solidity that is executed **only once**, when the contract is deployed. It is used to **initialize state variables** or perform **setup tasks**.

### 🔹 Key Points

- Executed **once** during contract deployment.
- Cannot be called again after deployment.
- Typically used to set **owner addresses**, initial balances, or configuration values.

### Example: Constructor

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ConstructorDemo {
    address public owner;

    constructor(address _owner) {
        owner = _owner; // Initialize the owner
    }
}
~~~~

💡 **Tip:** Always initialize critical variables in the constructor to prevent misconfigurations.

---

## ⚙️ 2. Functions

A **function** in Solidity is a block of code that performs a specific task. Functions can **read or modify state**, accept **parameters**, and return **values**.

### 🔹 Function Structure

1. **Function name**
2. **Parameters** (optional)
3. **Visibility modifier**
4. **State mutability modifier** (optional)
5. **Return type** (optional)
6. **Function body**

### Example: Basic Function

~~~~solidity
pragma solidity ^0.8.0;

contract FunctionDemo {
    uint public counter = 0;

    function increment() public {
        counter += 1;
    }

    function getCounter() public view returns (uint) {
        return counter;
    }
}
~~~~

**Explanation:**

- `increment()` modifies the **state**.
- `getCounter()` only **reads** the state; hence it is marked as `view`.

---

## 🔒 3. Visibility Modifiers

Visibility determines **who can call a function or access a variable**. Solidity supports four main visibility modifiers:

| Modifier | Description | Example |
|----------|-------------|---------|
| **public** | Accessible from anywhere (external or internal) | `function foo() public {}` |
| **private** | Accessible only within the contract | `function bar() private {}` |
| **internal** | Accessible within the contract and derived contracts | `function baz() internal {}` |
| **external** | Accessible only from outside the contract | `function qux() external {}` |

💡 **Tip:** Use the **least permissive visibility** needed to enhance security and minimize unintended access.

### Example: Visibility

~~~~solidity
pragma solidity ^0.8.0;

contract VisibilityDemo {
    uint private secret = 42;
    uint public data = 100;

    function revealSecret() private view returns (uint) {
        return secret;
    }

    function getData() public view returns (uint) {
        return data;
    }
}
~~~~

**Warning:** `private` only restricts access from **other contracts**, not from **reading the blockchain**. Values are still visible on-chain.

---

## 🧩 4. Data Typing

Solidity is **statically typed**, meaning the **type of each variable must be declared**. Common types:

| Type | Description | Example |
|------|-------------|---------|
| `uint` | Unsigned integer | `uint age = 30;` |
| `int` | Signed integer | `int temperature = -5;` |
| `bool` | Boolean value | `bool isActive = true;` |
| `address` | Ethereum address | `address owner = msg.sender;` |
| `string` | Text | `string name = "Alice";` |
| `bytes` | Fixed or dynamic byte array | `bytes32 id;` |

### Example: Typing in Function

~~~~solidity
pragma solidity ^0.8.0;

contract TypingDemo {
    function addNumbers(uint a, uint b) public pure returns (uint) {
        return a + b;
    }

    function isEven(uint x) public pure returns (bool) {
        return x % 2 == 0;
    }
}
~~~~

**Tip:** Choosing the smallest type that fits your data can **save gas** (e.g., `uint8` instead of `uint256` when possible).

---

## 🔄 5. Return Values

Functions in Solidity can **return one or more values**. Return types are declared using `returns()`.

### Single Return Example

~~~~solidity
pragma solidity ^0.8.0;

contract ReturnDemo {
    function square(uint x) public pure returns (uint) {
        return x * x;
    }
}
~~~~

### Multiple Returns Example

~~~~solidity
pragma solidity ^0.8.0;

contract MultipleReturnDemo {
    function getMinMax(uint a, uint b) public pure returns (uint min, uint max) {
        if (a < b) {
            return (a, b);
        } else {
            return (b, a);
        }
    }
}
~~~~

💡 **Tip:** Use descriptive variable names in multiple returns for clarity.

---

## 🧭 6. Combined Example — Constructor, Function, Visibility, Typing, Return

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

/// @title DemoContract
/// @notice Demonstrates constructor, functions, visibility, typing, and return values
contract DemoContract {
    address public owner;       // Storage (permanent)
    uint private counter = 0;   // Private variable

    constructor(address _owner) {
        owner = _owner;         // Initialize owner
    }

    function incrementCounter(uint value) public {
        counter += value;       // Modify state
    }

    function getCounter() public view returns (uint) {
        return counter;         // Read state
    }

    function minMax(uint a, uint b) external pure returns (uint min, uint max) {
        if (a < b) {
            return (a, b);
        } else {
            return (b, a);
        }
    }
}
~~~~

**Explanation:**

- `owner` → initialized in **constructor**.
- `counter` → private, modified via `incrementCounter`.
- `getCounter` → public, reads state.
- `minMax` → external, pure function with multiple return values.

---

## ✅ 7. Key Takeaways

| Concept | Summary |
|---------|---------|
| Constructor | Initializes contract state once at deployment |
| Functions | Encapsulate logic; can read or modify state |
| Visibility | Controls access: public, private, internal, external |
| Typing | Solidity is statically typed; choose efficient types |
| Return Values | Functions can return single or multiple values |

**Pro Tip:**  
- Minimize **state writes** to save gas.  
- Use **private/internal** visibility for sensitive logic.  
- Keep **functions modular and descriptive** for readability.

---

## 🖼 8. Visual Diagram: Function and Constructor Flow

![Function Flow](https://i.imgur.com/8Kj4JqP.png)  
*Alt text: Constructor initializes state, functions modify or read state.*

---






## Detailed contract structure

* (Review) Contract structure
* (Review) Variables
* (Review) Storage
* What are objects (contracts and interfaces)
* Multiple objects per file




---

## 1. Contract Structure (Review)

A **contract** in Solidity is similar to a class in OOP.  
It encapsulates **state variables**, **functions**, and optionally **inheritance**.

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MyContract {
    uint public count;

    constructor() {
        count = 0;
    }

    function increment() public {
        count += 1;
    }
}
~~~~

**Notes:**
- **constructor** runs once when the contract is deployed.
- **public** functions and variables are accessible externally.

---

## 2. Variables (Review)

Variables hold data. Solidity has **state**, **local**, and **global** variables.

| Type | Scope | Persistence | Example |
|------|-------|-------------|---------|
| State variable | Contract | Stored on blockchain | `uint public total;` |
| Local variable | Function | Temporary | `uint temp = 5;` |
| Global variable | Blockchain context | Read-only | `msg.sender`, `block.timestamp` |

~~~~solidity
function localExample() public pure returns (uint) {
    uint temp = 10;
    return temp;
}
~~~~

---

## 3. Storage (Review)

**Storage** refers to blockchain-persistent storage.  
**Memory** is temporary for function calls.  
**Stack** is for quick, small calculations.

$$
G_{storage} > G_{memory} > G_{stack}
$$

**Tip:** Minimize writes to storage to reduce gas.

---

## 4. Objects: Contracts and Interfaces

### Contracts
- Encapsulate **state** and **behavior**.
- Can inherit from other contracts.

### Interfaces
- Define **function signatures** without implementation.
- Used to interact with external contracts.

~~~~solidity
interface IToken {
    function transfer(address to, uint amount) external returns (bool);
}
~~~~

---

## 5. Multiple Objects Per File

You can define **multiple contracts or interfaces** in the same Solidity file.  
**Example:**

~~~~solidity
pragma solidity ^0.8.0;

contract FirstContract {
    uint public a;
}

contract SecondContract {
    uint public b;
}
~~~~

**Tip:** Use clear naming conventions and separate logical units for readability.

---

## 6. Summary Table

| Concept | Description | Example |
|---------|-------------|---------|
| Contract | Encapsulates state & behavior | `contract MyContract {...}` |
| Interface | Function signatures only | `interface IToken {...}` |
| State Variable | Persistent on blockchain | `uint public count;` |
| Local Variable | Temporary in function | `uint temp = 5;` |
| Storage | Permanent | Writes cost gas |
| Memory | Temporary | Function parameters |
| Stack | Quick small calculations | Local uints |

---

✅ By mastering these concepts, you can structure Solidity files efficiently, manage multiple objects, and optimize gas usage.
"""




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


# Contract Interaction and Clean Code Guide




## 🧩 Part 1: Contract Interactions Using Virtual Environments in Remix

### Step 1: Setting Up the Virtual Environment

When using **Remix IDE**, the environment selector (top-right corner) allows you to choose between:
- **JavaScript VM** (a local blockchain simulated in your browser)
- **Injected Provider - MetaMask** (connects to MetaMask testnets)
- **Web3 Provider** (custom RPC connection)

> **Note:** The *JavaScript VM* is ideal for safe, isolated testing, as all data is reset when the page reloads.

### Step 2: Deploying and Interacting with a Contract

Once you deploy a contract in Remix:
1. Select the contract under the **Deployed Contracts** section.
2. Expand it to view all its public and external functions.
3. Interact with **view** and **pure** functions without spending gas.

Example:

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract HelloWorld {
    string public text = "Hello World";

    function helloWorld() public view returns (string memory) {
        return text;
    }
}
~~~~

You can call `helloWorld()` directly — no transaction or gas fee is required because it’s a **read-only** operation.

---

## 🔍 Viewing State Changes Through Functions

When you modify the state of the blockchain, the change must be recorded in a new transaction.

For example:

~~~~solidity
function setText(string memory newText) public {
    text = newText;
}
~~~~

Calling `setText("Hi!")` changes the value of `text` in the contract’s storage.  
Remix automatically shows **“pending transaction”** and **“confirmed transaction”** logs.

> ⚠️ **Warning:** State-changing functions consume gas and cannot be executed in “view” mode.

---

## 🌐 Part 2: State Changing Calls in Public Testnets

Once you’re ready to deploy outside Remix’s virtual machine, you can use public testnets such as **Sepolia** or **Goerli**.

### Steps to Deploy on Testnet

1. Connect **MetaMask** to the testnet (e.g., Sepolia).  
2. Switch Remix’s environment to **Injected Provider - MetaMask**.  
3. Deploy your contract — MetaMask will prompt you to sign the transaction.  
4. Once deployed, you can interact with it exactly like in the local VM, but now transactions appear on **Etherscan testnet explorers**.

> 💡 **Tip:** Use faucets (like https://sepoliafaucet.com) to get free ETH for testnet experiments.

---

## 🤝 Using Interfaces to Interact with Other Contracts

Interfaces define **function signatures** that another contract can call — even if it doesn’t own the implementation.

Example:

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface HelloWorldInterface {
    function helloWorld() external view returns (string memory);
    function setText(string memory newText) external;
}

contract HelloWorld is HelloWorldInterface {
    string public text;

    function helloWorld() external view override returns (string memory) {
        return text;
    }

    function setText(string memory newText) external override {
        text = newText;
    }
}
~~~~

> 🧠 **Key Concept:** Interfaces allow contracts to **communicate** without sharing internal logic — essential for modular and reusable code.

---

## 💰 Part 3: Payable Functions

### What Are Payable Functions?

A **payable function** allows Ether to be sent alongside a function call. Without the `payable` keyword, any Ether sent will cause the transaction to fail.

Example:

~~~~solidity
pragma solidity ^0.8.0;

contract HelloWorld {
    string public text;

    function setText(string memory newText) public payable {
        text = newText;
    }

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
~~~~

### How to Experiment with Payable Calls in Remix

1. In Remix, open the **Deployed Contracts** section.
2. Locate the **Value** input field (above the function list).
3. Enter a small amount of ETH (e.g., `0.01 ether`).
4. Call `setText("New Message")` — the contract will now receive ETH.

> ✅ **Check:** Call `getBalance()` to confirm the new contract balance.

> ⚠️ **Warning:** Always validate payable inputs to avoid accidental loss of funds.

---

## 🧹 Clean Code and Documentation

### Why Clean Code Matters

Writing clean code ensures:
- Easier **readability**
- Improved **security audits**
- Better **maintainability**
- Team **collaboration** and **scalability**

### Solidity Style Guide Essentials

| Principle | Description |
|------------|-------------|
| **Naming** | Use descriptive names (e.g., `totalSupply`, not `x`). |
| **Formatting** | Use consistent indentation and spacing. |
| **Comments** | Use NatSpec for function documentation. |
| **Error Handling** | Use `require()` for validation, not raw `assert()`. |

---

## 🧾 Documentation with NatSpec Format

NatSpec (Ethereum Natural Specification Format) is used to describe the purpose, behavior, and expected results of Solidity functions.

Example:

~~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

/// @title HelloWorld Contract
/// @author Alice
/// @notice This contract allows storing and retrieving a text string.
/// @dev Example of NatSpec documentation format.
contract HelloWorld {
    string public text;

    /// @notice Returns the current stored text.
    /// @return The current text stored in the contract.
    function helloWorld() public view returns (string memory) {
        return text;
    }

    /// @notice Updates the stored text.
    /// @param newText The new text to store.
    function setText(string memory newText) public {
        text = newText;
    }
}
~~~~

> 💬 **Tip:** Tools like Doxygen or documentation generators can parse NatSpec comments directly from Solidity files.

---


## ✅ Summary

| Topic | Key Takeaway |
|-------|---------------|
| Virtual Environments | Safe local testing with Remix’s JavaScript VM |
| State-Changing Calls | Require transactions and gas fees |
| Interfaces | Allow modular contract interaction |
| Payable Functions | Enable ETH transfers to contracts |
| Clean Code | Essential for security and maintainability |
| NatSpec | Standardized function documentation format |

---

### References for clean code and documentation

<https://docs.soliditylang.org/en/latest/style-guide.html>

<https://docs.soliditylang.org/en/latest/natspec-format.html#natspec>

