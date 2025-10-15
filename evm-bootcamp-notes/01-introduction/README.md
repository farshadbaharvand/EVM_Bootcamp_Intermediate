# Lesson 1 - Introduction

## Wallets and transactions

* What is a wallet
* Finding wallet applications
* Using metamasks
* Creating a public-private key pair
* Connecting to Sepolia testnet
* Funding my wallet with a Faucet
* Sending a native token (ETH) transaction

## How ethereum works

* Explaining Etherscan
* Transactions
* Gas
* Blocks
* Consensus and finality
* State changes
* The EVM
* Accounts

## Trying it out

* Using Uniswap as an example
* What is a dApp
* Swap transaction example

## Contract interaction: Token Swap

* What is a state change inside a smart contract operation

## Hands on coding - Remix

* Remix interface (overview)

### References for Remix

<https://remix-ide.readthedocs.io/en/latest/>

## Coding HelloWorld.sol contract with Solidity

* Solidity philosophy
* OOP basics of Solidity
* Contract Structure
  * SPDX License Identifier
  * Pragmas
  * Imports
  * Comments
  * Contract definition
* State Variables
* Contract Storage
* Constructor function
* Functions
* Return values

### Code Reference

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {

    constructor() {}

    function helloWorld() public view returns (string memory) {}

}
```

### References for Solidity

<https://docs.soliditylang.org/en/latest/>

## Compiling and deploying

* Compilation parameters
  * Compiler version
  * EVM Version
  * Optimization
* Bytecode
* ABI
* Deployment parameters
  * Environment
  * Account
  * Gas
  * Contract
* Deploying
* Attaching


---

# Extended Lecture Notes

---



# Lesson 1 – Introduction  

## What is a Wallet?  

A **wallet** is a software or hardware tool that lets you **store, send, and receive cryptocurrency**.  

- Think of a wallet as a **bank account** for the blockchain.  
- Unlike banks, you control your funds entirely with **cryptographic keys**.  


---

## Finding Wallet Applications  

There are many Ethereum wallets:  

| Wallet Type | Examples | Features | Pros | Cons |
|-------------|----------|----------|------|------|
| **Browser Extension** | Metamask, Rabby | Easy to use, quick setup | Convenient, good for dApps | Can be less secure |
| **Mobile Wallets** | Trust Wallet, Argent | Mobile-first | Portable, user-friendly | Less powerful for devs |
| **Hardware Wallets** | Ledger, Trezor | Store keys offline | Very secure | Costs money |

 For this lesson, we'll use **Metamask** (a free browser extension).  
![Ethereum Wallet Concept](/evm-bootcamp-notes/images/wallet.png)
---

## Using Metamask  

1. Install **Metamask** from the official website: [https://metamask.io](https://metamask.io).  
2. Add the extension to your browser.  
3. Create a new wallet and **save your secret recovery phrase** safely.  

**Warning:** Never share your recovery phrase. Whoever has it can access all your funds.  

![Metamask Setup](/evm-bootcamp-notes/images/metamask.png)  

---

## Creating a Public–Private Key Pair  

At the core of a wallet lies a **key pair**:  

- **Private Key**: A secret random number you must keep safe.  
- **Public Key**: Derived from the private key using **elliptic curve cryptography**.  
- **Address**: A shorter, user-friendly representation of the public key.  

### Example Formula  

- Let $$k$$ = private key (random integer).  
- Public key $$K = k \cdot G$$, where $$G$$ is the elliptic curve generator point.  
- Ethereum address = last 20 bytes of $$\text{Keccak256}(K)$$.  

**Example:**

- Private Key:  
  ```
  0x3a1b2c3d4e5f67890123456789abcdef1234567890abcdef1234567890abcdef
  ```  
- Public Key:  
  ```
  0x0461...fae
  ```  
- Ethereum Address:  
  ```
  0x742d35Cc6634C0532925a3b844Bc454e4438f44e
  ```  

---

## Connecting to Sepolia Testnet  

The **Sepolia testnet** is a network where you can experiment without risking real ETH.  

1. Open **Metamask**.  
2. Go to **Settings → Networks → Add Network**.  
3. Enter details:  

| Field | Value |
|-------|-------|
| Network Name | Sepolia Testnet |
| RPC URL | https://rpc.sepolia.org |
| Chain ID | 11155111 |
| Currency Symbol | ETH |
| Block Explorer | https://sepolia.etherscan.io |

4. Save → Now you are connected to Sepolia.  

---

## Funding My Wallet with a Faucet  

A **faucet** gives free test ETH for development.  

Steps:  
1. Visit [Sepolia Faucet](https://sepoliafaucet.com/).  
2. Paste your Ethereum address.  
3. Click **Send me ETH**.  
4. Check Metamask balance → you now have test ETH.  

![Faucet Example](/evm-bootcamp-notes/images/sepolia.png)  

---

##  Sending a Native Token (ETH) Transaction  

Now let's send a transaction!  

1. Open **Metamask**.  
2. Click **Send**.  
3. Enter recipient address.  
4. Enter amount (e.g., `0.1 ETH`).  
5. Confirm → Transaction is broadcast.  

### Behind the Scenes  

When sending ETH:  

- You create a transaction $$T$$ with fields:  

$$
T = \{ \text{nonce}, \ \text{to}, \ \text{value}, \ \text{gasLimit}, \ \text{gasPrice}, \ \text{data} \}
$$



- Your **private key** signs the transaction:  



$$
\text{signature} = \mathrm{ECDSA_{Sign}}(T, k)
$$

- Miners/validators verify your signature with your **public key**.  
- Transaction is included in the blockchain.  

---

## Example Transaction  

| Field | Example Value |
|-------|---------------|
| Nonce | 1 |
| To | `0x742d35Cc6634C0532925a3b844Bc454e4438f44e` |
| Value | 0.1 ETH |
| Gas Limit | 21,000 |
| Gas Price | 20 gwei |
| Data | Empty |

 After confirmation, the recipient wallet balance increases.  

---

## Summary Table  

| Concept | Key Idea | Example |
|---------|----------|---------|
| **Wallet** | Manages keys and transactions | Metamask |
| **Private Key** | Secret number | `0x3a1b...` |
| **Public Key** | Derived from private key | `0x0461...` |
| **Address** | Shortened identifier | `0x742d...` |
| **Faucet** | Provides free test ETH | Sepolia Faucet |
| **Transaction** | Signed message to transfer ETH | Send 0.1 ETH |

---

## Notes & Tips  

- Always back up your **private key** and **recovery phrase**.  
- Testnets are **free** and safe → practice here before mainnet.  
- Gas fees are still required on testnets, but you use **free faucet ETH**.  


---





# How Ethereum Works



## 1. Explaining Etherscan

**Etherscan** is a blockchain explorer for Ethereum. It allows anyone to **view transactions, wallet addresses, blocks, and smart contracts** on the Ethereum network.  

**Why it's useful:**
- Track the **status of your transactions**
- Check **wallet balances**
- Inspect **smart contract code**
- Explore the **history of blocks and transactions**

**Example:**
Searching for a wallet address on [Etherscan.io](https://etherscan.io) shows:

| Info Type | Description |
|-----------|-------------|
| ETH Balance | Current ETH held in the address |
| Transaction History | All sent and received transactions |
| Token Holdings | ERC-20/ERC-721 tokens associated with the address |
| Contract Interactions | Calls made to smart contracts |



**Tip:** Always verify transaction hashes on Etherscan to confirm the transaction was processed.

---

## 2. Transactions

A **transaction** is a **recorded action** on Ethereum. Transactions can either **transfer ETH** or **interact with smart contracts**.

**Key Components of a Transaction:**

| Component | Description |
|-----------|-------------|
| **From** | Sender's Ethereum address |
| **To** | Receiver's Ethereum address or contract address |
| **Value** | Amount of ETH sent |
| **Gas Limit** | Maximum computational effort allowed |
| **Gas Price** | Price per unit of gas (in Gwei) |
| **Data** | Optional field for smart contract interactions |
| **Nonce** | Sequential transaction number for the sender |

**Example Transaction:**
~~~~
From: 0xAlice...
To: 0xBob...
Value: 0.5 ETH
Gas Limit: 21000
Gas Price: 20 Gwei
Nonce: 5
~~~~

**Note:** The **nonce** prevents double-spending by ensuring transactions are executed in order.

---

## 3. Gas

**Gas** measures the **computational work** needed to execute transactions on Ethereum. Every operation consumes gas.

**Why Gas Exists:**
- Prevents network spam
- Ensures users pay for the computation they use

**Transaction Fee Formula:**


$$
\text{Transaction Fee} = \text{Gas Used} \times \text{Gas Price}
$$

**Example Calculation:**
- Gas Used: 21,000  
- Gas Price: 20 Gwei  

$$
\text{Transaction Fee} = 21,000 \times 20 \text{ Gwei} = 420,000 \text{ Gwei} = 0.00042 \text{ ETH}
$$

**Tip:** Set an appropriate **Gas Limit** to avoid failed transactions. Too low will fail, too high is refunded.

---

## 4. Blocks

**Blocks** are bundles of transactions. They form the **blockchain**, ensuring transaction history is immutable.

**Block Structure:**

| Field | Description |
|-------|-------------|
| **Block Number** | Sequential identifier |
| **Parent Hash** | Hash of the previous block |
| **Transactions** | List of transactions in the block |
| **State Root** | Hash representing the blockchain state |
| **Timestamp** | When the block was created |
| **Validator/Miner** | Node that proposed the block |

![Ethereum Block](/evm-bootcamp-notes/images/block.png)  

**Note:** Each block references the previous block, creating an **immutable ledger**.

---

## 5. Consensus and Finality

Ethereum uses **Proof of Stake (PoS)** for consensus.

**Key Concepts:**
- **Validators**: Users who lock ETH to propose and validate blocks
- **Proposing**: A validator creates a new block
- **Attesting**: Validators vote to confirm blocks

**Finality:** A block is **final** when enough validators attest to it. It can no longer be reversed.

**Example:**
1. Validator A proposes Block #100
2. Validators B, C, D attest
3. If >2/3 attest, Block #100 becomes **final**

**Tip:** Blocks become **more secure** over time as more blocks are built on top of them.

---

## 6. State Changes

Ethereum maintains a **global state**, including:
- **Account balances**
- **Contract storage**
- **Nonces** of accounts

**How a transaction changes state:**
1. Ethereum applies the transaction
2. Updates balances, contract storage, and nonce
3. Records a new **state root** in the block

**Example Table:**

| Account | Old Balance | Transaction | New Balance |
|---------|------------|------------|------------|
| Alice   | 5 ETH      | Send 1 ETH to Bob | 4 ETH |
| Bob     | 2 ETH      | Receive 1 ETH from Alice | 3 ETH |

**Tip:** Transactions that fail due to out-of-gas or errors **still consume gas**.

---

## 7. The Ethereum Virtual Machine (EVM)

The **EVM** is the environment where **smart contracts run**.

**Key Features:**
- Turing-complete
- Executes bytecode compiled from Solidity
- **Isolated execution** prevents contracts from interfering with each other

**Example Solidity Contract:**
~~~~
pragma solidity ^0.8.0;

contract SimpleStore {
    uint256 public data;

    function setData(uint256 _data) public {
        data = _data;
    }
}
~~~~

- Calling `setData(42)` updates the contract storage in the EVM to **42**.

**Tip:** Always test smart contracts on a **testnet** before deploying on mainnet.

---

## 8. Accounts

Ethereum has **two main account types**:

1. **Externally Owned Accounts (EOA)**
   - Controlled by a **private key**
   - Can initiate transactions
   - Holds ETH balance

2. **Contract Accounts**
   - Controlled by **code**
   - Cannot start transactions by themselves
   - Can store data and respond to transactions

**Comparison Table:**

| Feature | EOA | Contract Account |
|---------|-----|----------------|
| Controlled by | Private Key | Contract Code |
| Can send transactions | ✅ | ❌ |
| Can receive ETH | ✅ | ✅ |
| Storage | Limited | Contract state (variables) |



**Note:** EOAs are always required to **initiate transactions**, while contracts can **respond to calls** automatically.

---

# What is a **dApp**?

A **decentralized application (dApp)** is like a regular app but:
- It uses a **blockchain** (e.g., Ethereum) for state and logic.
- The **backend logic** often lives inside **smart contracts**.
- The **frontend** is a web UI (React, Vue, etc.) that talks to the smart contracts via a wallet (MetaMask, WalletConnect).

**Key properties of dApps:**
- **Decentralized backend:** data & logic on-chain.
- **Permissionless:** anyone can interact (subject to gas and contract logic).
- **Transparent:** transactions are public (viewable on Etherscan).

**Simple diagram (visual):**

~~~~
[User Browser (React)] --(RPC via ethers.js/wagmi)--> [Wallet (MetaMask)]
        |                                             |
        |                                             V
        |                                        [Ethereum Node / Provider]
        |                                             |
        V                                             V
     UI Actions  ------------------------------> [Smart Contract (on-chain)]
~~~~

---

## Using **Uniswap** as an example (AMM)

**Uniswap** is a **Decentralized Exchange (DEX)** that uses an **Automated Market Maker (AMM)** model instead of an order book.

### The core idea: **Constant product AMM (Uniswap V2)**
Pools hold two tokens with reserves \(x\) and \(y\). The invariant is:

$$
x \cdot y = k
$$

When you swap an amount of token A into the pool, the reserves shift but the product stays (approximately) constant, and the trader receives token B.

**Notation**
- \(x\) = reserve of Token A (e.g., ETH)
- \(y\) = reserve of Token B (e.g., DAI)
- \(dx\) = amount of Token A added (input)
- \(dy\) = amount of Token B removed (output)
- fee (Uniswap v2 typical) = 0.3% = 0.003
- effective input 

$$\(dx_{\text{eff}} = dx \cdot (1 - \text{fee})\)$$

**Swap formula (derived from invariants):**

$$
dy = y - \frac{k}{x + dx_{\text{eff}}}
\qquad\text{where}\quad k = x \cdot y
$$

---

### Example: Swap 1 ETH → DAI (step-by-step numeric)

**Initial reserves:**
- $$\(x = 100\)$$ ETH  
- $$\(y = 20{,}000\)$$ DAI

**Trader swaps:** $$\(dx = 1\)$$ ETH  
**Fee:** \(0.3\% = 0.003\) ⇒ multiplier \(1 - fee = 0.997\)

**Step 1 — effective input:**

$$
dx_{\text{eff}} = dx \times 0.997 = 1 \times 0.997 = 0.997
$$

**Step 2 — constant product \(k\):**

$$
k = x \cdot y = 100 \times 20{,}000 = 2{,}000{,}000
$$

**Step 3 — new \(x\) after input:**

$$
x' = x + dx_{\text{eff}} = 100 + 0.997 = 100.997
$$

**Step 4 — new \(y\) to satisfy invariant:**

$$
y' = \frac{k}{x'} = \frac{2{,}000{,}000}{100.997} \approx 19{,}802.56839312059
$$

**Step 5 — output to trader \(dy\):**

$$
dy = y - y' = 20{,}000 - 19{,}802.56839312059 \approx 197.43160687941054
$$

**Conclusion:** The trader receives **~197.4316 DAI** for 1 ETH in this pool.

**Notes & interpretations:**
- **Price impact**: swapping a non-negligible amount relative to reserves moves the price.
- **Slippage**: if market price moves between submitting and inclusion, the actual output may differ.
- **Tip:** Larger pools (bigger reserves) reduce price impact for the same trade size.

---

### AMM vs Order Book — Quick Comparison

| Feature | AMM (Uniswap) | Order Book |
|---|---:|:---|
| Price formation | Liquidity pool invariant | Matched buy/sell orders |
| Liquidity provider returns | Earn fees + impermanent loss | Spread capture / maker rebates |
| Best for | Continuous liquidity & small trades | Deep liquidity and price discovery for large trades |
| Complexity | Simpler UX, predictable math | Requires order matching, limit orders |



---

## What is a **Swap Transaction** (high-level flow)?

1. **User (EOA)** approves token (if ERC-20) to the router (if swapping ERC-20).
2. **User** calls **Router.swapExactTokensForTokens** (or similar) on the Uniswap Router contract.
3. **Router** transfers tokens into the pair contract, calls pair's `swap()` to update reserves and send tokens out.
4. **EVM** executes the transaction atomically — either all state changes succeed or the entire tx **reverts** (and gas is consumed).
5. **Transaction recorded in a block** — visible on Etherscan.

**Transaction flow ASCII diagram:**

~~~~
[EOA: Alice]
  |
  |--(1) approve(token -> Router)--> [ERC20 token contract]
  |
  |--(2) tx: call Router.swapExactTokensForTokens ---> [Uniswap Router]
                                                   |
                                                   V
                                          [Pair Contract (x,y) reserves]
                                                   |
                                                   V
                                            [EVM executes swap()]
                                                   |
                                                   V
                                         State changes: balances & reserves
~~~~

---

## Swap Transaction Example — gas & fees (Ethereum)

**Gas fee formula (LaTeX):**

$$
\text{Transaction Fee (ETH)} = \text{Gas Used} \times \text{Gas Price (in ETH)}
$$

**Gwei conversion:**  
$$1 \text{ Gwei} = 10^{-9}\ \text{ETH}$$

**Example numbers:**
- Gas Used (estimate for ERC-20 swap): $$\( \text{GasUsed} = 150{,}000 \)$$ gas  
- Gas Price: $$\(20\)$$ Gwei

**Step-by-step calculation:**

$$
\text{Gas Price (ETH)} = 20 \times 10^{-9}\ \text{ETH} = 2.0 \times 10^{-8}\ \text{ETH}
$$

$$
\text{Fee} = 150{,}000 \times 2.0 \times 10^{-8}\ \text{ETH} = 3.0 \times 10^{-3}\ \text{ETH} = 0.003\ \text{ETH}
$$

**Interpretation:** At these values the swap costs **~0.003 ETH** in gas.

**Tip:** For precise gas fees on mainnet, use `provider.getFeeData()` (ethers.js) and estimate gas with `contract.estimateGas.method()`.

---

# Hands on coding - Remix

* Remix interface (overview)

## References for Remix

<https://remix-ide.readthedocs.io/en/latest/>



## Coding HelloWorld.sol contract with Solidity

* Solidity philosophy
* OOP basics of Solidity
* Contract Structure
  * SPDX License Identifier
  * Pragmas
  * Imports
  * Comments
  * Contract definition
* State Variables
* Contract Storage
* Constructor function
* Functions
* Return values




# Coding `HelloWorld.sol` with Solidity

> **Goal:** Step-by-step guide to understanding and coding a basic Solidity contract. Covers Solidity philosophy, OOP concepts, contract structure, state variables, functions, constructors, and return values.

---

![Solidity](/evm-bootcamp-notes/images/remix.png)  

---

## 1. Solidity Philosophy

Solidity is a **high-level, contract-oriented programming language** designed for **Ethereum smart contracts**.

**Key points:**
- **Smart contract-focused:** Solidity allows writing contracts that manage **assets, logic, and rules** on-chain.
- **Statically typed:** All variables and functions must declare their types.
- **Compiled to EVM bytecode:** Contracts run on the **Ethereum Virtual Machine (EVM)**.

**Tip:** Think of Solidity as **JavaScript + C++** for smart contracts — it has JavaScript-like syntax with Ethereum-specific features.

---

## 2. OOP Basics of Solidity

Solidity supports **object-oriented programming concepts**, including:

- **Contracts as classes:** Each contract is like a class containing data and behavior.
- **State variables:** Represent the **attributes** of the contract (like class fields).
- **Functions:** Represent **methods** that manipulate state or perform calculations.
- **Inheritance:** Contracts can inherit from other contracts to reuse logic.
- **Modifiers:** Control function execution (like access control).

**Example analogy:**

| Solidity Term | OOP Analogy | Description |
|---------------|------------|------------|
| Contract      | Class      | Blueprint for smart contract objects |
| State variable| Field      | Data stored in contract storage |
| Function      | Method     | Behavior or action the contract can perform |
| Constructor   | Constructor | Initializes contract state at deployment |

---

## 3. Contract Structure

A typical Solidity contract has several sections. Step-by-step:

### 3.1 SPDX License Identifier
Specifies the license for your code. Helps tools like Solidity compiler understand usage rights.

~~~~solidity
// SPDX-License-Identifier: GPL-3.0
~~~~

> **Tip:** Always include SPDX for clarity and to avoid compiler warnings.

---

### 3.2 Pragmas
Defines the Solidity compiler version.

~~~~solidity
pragma solidity >=0.7.0 <0.9.0;
~~~~

> **Explanation:**  
- `>=0.7.0` → Compatible with version 0.7.0 and above  
- `<0.9.0` → Not compatible with version 0.9.0 and above  
- Helps ensure your code compiles predictably.

---

### 3.3 Imports
You can import other Solidity files or libraries for reuse.

~~~~solidity
import "./SafeMath.sol"; // Example import of a library
~~~~

> **Tip:** Use OpenZeppelin libraries for secure and tested code.

---

### 3.4 Comments
Solidity supports single-line `//` and multi-line `/* ... */` comments.

~~~~solidity
// Single-line comment
/* Multi-line
   comment */
~~~~

---

### 3.5 Contract Definition
Contracts are the blueprint of your on-chain program.

~~~~solidity
contract HelloWorld {
    // Contract code goes here
}
~~~~

> **Important:** Contract names are **case-sensitive** and should be **PascalCase**.

---

## 4. State Variables

State variables store **data permanently on-chain**.

~~~~solidity
string public greeting = "Hello, Blockchain!";
~~~~

- `string` → Data type for text  
- `public` → Automatically creates a getter function  
- Stored in **contract storage** (permanent, costs gas to write)

**Note:** Reading state variables (`view`) is free; writing costs gas.

---

## 5. Contract Storage

Solidity has three types of storage:

| Storage Type | Location | Gas Cost | Notes |
|--------------|---------|----------|------|
| **Storage**  | On-chain | High     | Persistent between transactions |
| **Memory**   | Temporary | Low    | Exists only during function execution |
| **Stack**    | EVM stack | Very Low | Used for local function variables |

> **Tip:** Minimize writing to **storage** to save gas.

---

## 6. Constructor Function

The constructor is executed **once at deployment** to initialize contract state.

~~~~solidity
constructor() {
    greeting = "Hello, Ethereum!";
}
~~~~

**Explanation:**  
- No return value  
- Can take arguments to initialize variables dynamically  

**Example with parameter:**

~~~~solidity
constructor(string memory _greeting) {
    greeting = _greeting;
}
~~~~

---

## 7. Functions

Functions define **behavior** of the contract.

- **Public / external:** Accessible by users or other contracts
- **Internal / private:** Only accessible within the contract or derived contracts
- **View / pure:** Does not modify state (view can read; pure cannot even read state)

### Example:

~~~~solidity
function helloWorld() public view returns (string memory) {
    return greeting;
}
~~~~

**Explanation:**
- `public` → Anyone can call
- `view` → Reads state but does not modify it
- `returns (string memory)` → Returns a string stored in memory

---

## 8. Return Values

- Functions can **return values** to the caller.
- Memory variables exist **temporarily** during function execution.

**Example:**

~~~~solidity
function getGreeting() public view returns (string memory) {
    return greeting;
}
~~~~

> Calling this function returns the current greeting string.

---

## 9. Full HelloWorld Contract Example

~~~~solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {

    string public greeting;

    // Constructor executed once at deployment
    constructor(string memory _greeting) {
        greeting = _greeting;
    }

    // Returns the greeting message
    function helloWorld() public view returns (string memory) {
        return greeting;
    }
}
~~~~

---

## 10. Step-by-Step Execution Flow

1. **Deployment:**  
   - Constructor runs, sets `greeting`
2. **State:**  
   - `greeting` stored in contract storage
3. **Function Call:**  
   - `helloWorld()` reads the `greeting` variable
4. **Return:**  
   - Returns `"Hello, Ethereum!"` (or the value passed in constructor)

---

## 11. Tips and Best Practices

- Always include **SPDX License** and **pragma**.  
- Use **PascalCase** for contract names.  
- Minimize storage writes to save gas.  
- Use `public` for state variables to auto-generate getters.  
- Use **view / pure** functions whenever no state modification is needed.  
- Comment your code for clarity.  

---

## 12. Summary Table

| Component | Purpose | Example |
|-----------|--------|--------|
| SPDX License | License info | `// SPDX-License-Identifier: GPL-3.0` |
| Pragma | Compiler version | `pragma solidity >=0.7.0 <0.9.0;` |
| Import | External libraries | `import "./SafeMath.sol";` |
| State Variable | Store on-chain data | `string public greeting;` |
| Constructor | Initialize state | `constructor(string memory _greeting) { ... }` |
| Function | Define behavior | `function helloWorld() public view returns(string memory)` |

---

## 13. Visual Diagram of Contract Structure

~~~~
Contract HelloWorld
├── State Variables
│    └── greeting : string (storage)
├── Constructor(_greeting)
│    └── sets greeting
└── Function helloWorld()
     └── returns greeting
~~~~


### Code Reference

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {

    constructor() {}

    function helloWorld() public view returns (string memory) {}

}
```

### References for Solidity

<https://docs.soliditylang.org/en/latest/>

## Compiling and deploying

* Compilation parameters
  * Compiler version
  * EVM Version
  * Optimization
* Bytecode
* ABI
* Deployment parameters
  * Environment
  * Account
  * Gas
  * Contract
* Deploying
* Attaching


# Compiling and Deploying Solidity Contracts

---

## 1. Compilation Parameters

Compiling a Solidity contract converts human-readable code into **EVM bytecode** and generates an **ABI**.

### 1.1 Compiler Version

- Solidity evolves rapidly; compiler versions can introduce **breaking changes**.  
- Example: `pragma solidity >=0.8.0 <0.9.0;` ensures compatibility with 0.8.x releases.

**Tip:** Match the compiler version in your IDE (Remix, Hardhat, Foundry) with the pragma.

### 1.2 EVM Version

- **EVM version** determines which Ethereum Virtual Machine features are available.  
- Common options: `istanbul`, `berlin`, `london`, `paris`.  
- In Hardhat/Remix:

~~~~
EVM Version: lLondon
~~~~

> **Warning:** Using an older EVM may cause newer opcodes (like `CREATE2`) to fail.

### 1.3 Optimization

- **Optimization** reduces gas consumption but may increase compilation time.  
- Example: `optimizer: { enabled: true, runs: 200 }`  

**Tip:** Use higher `runs` for contracts called frequently.

---

## 2. Bytecode

- **Bytecode** is the compiled **machine code executed by the EVM**.  
- Every Solidity contract generates **two types of bytecode**:
  1. **Creation bytecode:** Runs once at deployment to initialize the contract.
  2. **Runtime bytecode:** Persisted on-chain and executed for transactions.

**Example:**  

~~~~
0x608060405234801561001057600080fd5b506040516101003803806101008339810160409081528151...
~~~~

> **Tip:** Never manually edit bytecode — use compiler outputs.

---

## 3. ABI (Application Binary Interface)

- **ABI** defines how to interact with a smart contract.  
- Maps function calls and event signatures to **EVM-compatible data**.

**Example ABI for HelloWorld contract:**

~~~~
[
  {
    "inputs": [],
    "name": "helloWorld",
    "outputs": [
      { "internalType": "string", "name": "", "type": "string" }
    ],
    "stateMutability": "view",
    "type": "function"
  }
]
~~~~

**Explanation:**  
- `inputs` → function arguments  
- `outputs` → return values  
- `stateMutability` → `view`, `pure`, `payable`, or `nonpayable`

> **Tip:** Tools like **ethers.js** or **web3.js** require ABI for calling contract functions.

---

## 4. Deployment Parameters

### 4.1 Environment

- **Local:** Ganache, Hardhat Network, Foundry node  
- **Testnet:** Goerli, Sepolia  
- **Mainnet:** Ethereum main network

> **Tip:** Always deploy first on **testnet** before mainnet to avoid losing real ETH.

### 4.2 Account

- Deployment requires an **EOA with sufficient ETH** to pay for gas.  
- Example in Hardhat:

~~~~
const [deployer] = await ethers.getSigners();
console.log("Deploying from:", deployer.address);
~~~~

### 4.3 Gas

- Gas depends on **bytecode size and complexity**.  
- Formula:

$$
\text{Transaction Fee (ETH)} = \text{Gas Used} \times \text{Gas Price (ETH)}
$$

**Tip:** Estimate gas using provider tools: `estimateGas()`.

### 4.4 Contract

- Specify which contract in the project you want to deploy if multiple exist:

~~~~
const HelloWorld = await ethers.getContractFactory("HelloWorld");
~~~~

---

## 5. Deploying the Contract

### 5.1 Using ethers.js

~~~~
const HelloWorld = await ethers.getContractFactory("HelloWorld");
const hello = await HelloWorld.deploy("Hello, Ethereum!");
await hello.deployed();
console.log("Contract deployed at:", hello.address);
~~~~

**Explanation:**  
1. `getContractFactory` loads the compiled contract (bytecode + ABI)  
2. `deploy()` sends creation bytecode to blockchain  
3. `deployed()` waits for mining confirmation  
4. `hello.address` gives the on-chain address

---

### 5.2 Using Remix IDE

1. Compile contract (check compiler version and optimization)  
2. Select **Injected Web3** environment to use MetaMask  
3. Click **Deploy** and confirm transaction in wallet  
4. Once mined, view contract address in Remix console

---

## 6. Attaching to an Existing Contract

- Sometimes you want to **interact with an already deployed contract**.

### Example using ethers.js

~~~~
const address = "0xDeployedContractAddress";
const abi = [ /* ABI here */ ];
const hello = new ethers.Contract(address, abi, signer);

const message = await hello.helloWorld();
console.log("Greeting:", message);
~~~~

> **Tip:** Ensure ABI and contract address match the deployed contract; otherwise calls will fail.

---

## 7. Visual Deployment Flow Diagram

~~~~
[Solidity Contract: HelloWorld.sol]
           |
           V
     Compilation (Solc)
           |
           V
  Bytecode + ABI Generated
           |
           V
   Deployment Transaction
           |
           V
      Ethereum Network
           |
           V
  Contract Address Assigned
           |
           V
     Users Interact via ABI
~~~~

---

## 8. Notes, Tips, and Pitfalls

- **Always verify compiler version** matches `pragma`.  
- **Estimate gas** before deployment to avoid failed transactions.  
- **Test on testnets first**.  
- **Keep deployment private keys safe**; loss = loss of contract control.  
- **Check contract bytecode size** — max contract size is ~24KB.  
- **Use deterministic deployment** (CREATE2) if planning upgradeable contracts.  

---

## 9. Summary Table

| Step | Description | Example / Notes |
|------|------------|----------------|
| Compiler Version | Solidity version to use | `pragma solidity >=0.8.0 <0.9.0;` |
| EVM Version | Target Ethereum VM | `istanbul`, `london` |
| Optimization | Gas efficiency | `optimizer: { enabled: true, runs: 200 }` |
| Bytecode | Machine code for EVM | `0x6080604052...` |
| ABI | Interface for function calls | JSON array of function/event descriptions |
| Environment | Network for deployment | Local, Testnet, Mainnet |
| Account | EOA paying gas | MetaMask, Hardhat signer |
| Gas | Transaction cost | `Gas Used x Gas Price` |
| Deployment | Sending creation bytecode | `HelloWorld.deploy("Hello!")` |
| Attach | Interact with deployed contract | `new ethers.Contract(address, abi, signer)` |

---

## 10. Final Tips

- Compile and test **incrementally**.  
- Save ABI and bytecode for frontend integration.  
- Monitor gas and transaction status using **Etherscan** or testnet explorers.  
- For multiple contracts, consider using **Hardhat deployments scripts** for automation.  
