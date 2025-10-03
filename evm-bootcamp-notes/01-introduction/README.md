# Lesson 1 – Introduction  


---

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
\text{signature} = \text{ECDSA\_Sign}(T, k)
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

## 📊 Summary Table  

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





# How Ethereum Works

This guide provides a **step-by-step, beginner-friendly explanation** of Ethereum, covering everything from **transactions** and **gas** to the **Ethereum Virtual Machine (EVM)** and **accounts**. Visual examples, tables, and diagrams are included to clarify concepts.

---

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
