# Lesson 6 - Building unit tests for HelloWorld.sol

## (Review) Coding in VS Code

* Syntax for typescript scripts
* How the project operates
* Writing a test file
* Using Viem library
* Using Hardhat toolbox
* Testing syntax
* Running a test file

### References

<https://viem.sh/>

<https://hardhat.org/hardhat-runner/docs/getting-started#overview>

<https://hardhat.org/hardhat-runner/docs/advanced/using-viem>

<https://mochajs.org/>

<https://hardhat.org/hardhat-chai-matchers/docs/overview>

<https://www.chaijs.com/guide/>

## TDD methodology

* Writing unit tests that fail first
* Filling code that completes the test
* Refactoring
* Measuring code smell
* Measuring coverage
* Iteration process
* How HelloWorld.sol could be tested

### References for TDD

<https://www.agilealliance.org/glossary/tdd/>

<https://www.browserstack.com/guide/what-is-test-driven-development>

## Running tests on HelloWorld.sol

* Contract

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

* Tests

```typescript
// https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment
// https://hardhat.org/hardhat-runner/docs/advanced/using-viem
import { viem } from "hardhat";
// https://www.chaijs.com/guide/styles/#expect
import { expect } from "chai";
// https://hardhat.org/hardhat-network-helpers/docs/overview
// https://hardhat.org/hardhat-network-helpers/docs/reference#fixtures
import { loadFixture } from "@nomicfoundation/hardhat-toolbox-viem/network-helpers";

// https://mochajs.org/#getting-started
describe("HelloWorld", function () {
  async function deployContractFixture() {
    // https://hardhat.org/hardhat-runner/docs/advanced/using-viem#clients
    const publicClient = await viem.getPublicClient();
    const [owner, otherAccount] = await viem.getWalletClients();
    // https://hardhat.org/hardhat-runner/docs/advanced/using-viem#contracts
    const helloWorldContract = await viem.deployContract("HelloWorld");
    // https://www.typescriptlang.org/docs/handbook/2/functions.html#parameter-destructuring
    return {
      publicClient,
      owner,
      otherAccount,
      helloWorldContract,
    };
  }

  it("Should give a Hello World", async function () {
    const { helloWorldContract } = await loadFixture(deployContractFixture);
    // https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-viem#contracts
    const helloWorldText = await helloWorldContract.read.helloWorld();
    // https://www.chaijs.com/api/bdd/#method_equal
    expect(helloWorldText).to.equal("Hello World");
  });

  it("Should set owner to deployer account", async function () {
    const { helloWorldContract, owner } = await loadFixture(
      deployContractFixture
    );
    // https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-viem#contracts
    const contractOwner = await helloWorldContract.read.owner();
    // https://www.chaijs.com/api/bdd/#method_equal
    expect(contractOwner.toLowerCase()).to.equal(owner.account.address);
  });

  it("Should not allow anyone other than owner to call transferOwnership", async function () {
    const { helloWorldContract, otherAccount } = await loadFixture(
      deployContractFixture
    );
    // https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-viem#retrieving-an-existing-contract
    const helloWorldContractAsOtherAccount = await viem.getContractAt(
      "HelloWorld",
      helloWorldContract.address,
      { client: { wallet: otherAccount } }
    );
    // https://www.chaijs.com/plugins/chai-as-promised/
    await expect(
      helloWorldContractAsOtherAccount.write.transferOwnership([
        otherAccount.account.address,
      ])
    ).to.be.rejectedWith("Caller is not the owner");
  });

  it("Should execute transferOwnership correctly", async function () {
    const { publicClient, helloWorldContract, owner, otherAccount } =
      await loadFixture(deployContractFixture);
    // https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-viem#contracts
    const txHash = await helloWorldContract.write.transferOwnership([
      otherAccount.account.address,
    ]);
    // Transactions are instantly mined in the local network, but it is important to remember to await for confirmations when using a public network
    // https://viem.sh/docs/actions/public/getTransactionReceipt#gettransactionreceipt
    const receipt = await publicClient.getTransactionReceipt({ hash: txHash });
    // https://viem.sh/docs/glossary/terms#transaction-receipt
    expect(receipt.status).to.equal("success");
    const contractOwner = await helloWorldContract.read.owner();
    expect(contractOwner.toLowerCase()).to.equal(otherAccount.account.address);
    // It is important to check all relevant indirect effects in your tests
    const helloWorldContractAsPreviousAccount = await viem.getContractAt(
      "HelloWorld",
      helloWorldContract.address,
      { client: { wallet: owner } }
    );
    await expect(
      helloWorldContractAsPreviousAccount.write.transferOwnership([
        owner.account.address,
      ])
    ).to.be.rejectedWith("Caller is not the owner");
  });

  it("Should not allow anyone other than owner to change text", async function () {
    // TODO
    throw Error("Not implemented");
  });

  it("Should change text correctly", async function () {
    // TODO
    throw Error("Not implemented");
  });
});
```

---


# Lesson 6 - Building Unit Tests for HelloWorld.sol

## Overview

In this lesson, you’ll learn how to write and execute unit tests for a simple Solidity contract using Hardhat, Viem, TypeScript, and Mocha/Chai.
Testing is an essential part of smart contract development — it ensures your code works correctly, securely, and predictably.

## (Review) Coding in VS Code

Before writing tests, make sure you understand how your project structure works and how TypeScript scripts interact with Hardhat.

## Typical Hardhat Project Structure

| Folder              | Description                                       |
| :------------------ | :------------------------------------------------ |
| `contracts/`        | Solidity smart contracts                          |
| `scripts/`          | Automation scripts for deployment or verification |
| `test/`             | Unit and integration tests (usually `.ts` files)  |
| `hardhat.config.ts` | Hardhat configuration file                        |


---

**TypeScript Syntax Basics**

In a TypeScript test file:

1. Import libraries and tools.

2. Define asynchronous test functions using async/await.

3. Use expect() for assertions.

**Example**

~~~~typescript
import { expect } from "chai";

describe("Example", function () {
  it("Should return true", async function () {
    const value = true;
    expect(value).to.be.true;
  });
});

~~~~

## How the Project Operates

Hardhat simulates an Ethereum network locally, compiles your contracts, and deploys them in a sandbox environment.
Using Viem, we can interact with contracts as clients, similar to how front-end apps communicate with deployed contracts.

**Writing a Test File**

Your test files live in the /test folder and usually match your contract name.

**Example:**

~~~~bash
contracts/HelloWorld.sol
test/HelloWorld.ts


~~~~

## Using Viem Library

**Viem**
 is a modern TypeScript library for Ethereum development.
It provides a type-safe, developer-friendly interface for interacting with contracts and the blockchain.

Core functions:

- getPublicClient() — connects to the blockchain node.

- getWalletClients() — retrieves available signers/accounts.

- deployContract() — deploys a compiled Solidity contract.

- getContractAt() — connects to an already deployed contract.

**Using Hardhat Toolbox**

The Hardhat Toolbox
 provides:

- Chai matchers for Solidity (e.g., .to.be.revertedWith).

- Network helpers like loadFixture for efficient testing.

- Integrated Mocha test runner.

**Testing Syntax**

Tests are written using Mocha’s describe and it syntax:
~~~~typescript
describe("ContractName", function () {
  it("Should perform expected behavior", async function () {
    // Test logic
  });
});

~~~~


**Running Test Files**

To execute your tests, use:
~~~~bash
npx hardhat test

~~~~
or run a specific test:
~~~~bash
npx hardhat test test/HelloWorld.ts

~~~~

## TDD Methodology (Test-Driven Development)

Test-Driven Development (TDD) is a software engineering approach that emphasizes writing tests before writing the implementation code.

TDD Cycle

| Step                          | Description                                      |
| :---------------------------- | :----------------------------------------------- |
| **1. Write a failing test**   | Define expected behavior before implementation.  |
| **2. Implement minimal code** | Just enough to make the test pass.               |
| **3. Refactor**               | Improve the code without changing functionality. |
| **4. Repeat**                 | Continue iterating for new features.             |


---

**Example Flow for HelloWorld.sol**

1. Write a test expecting the contract to return "Hello World".

2. Implement the contract’s helloWorld() function.

3. Run tests and verify they pass.

4. Add more tests for setText() and transferOwnership().

---

## Running Tests on HelloWorld.sol

Below is the smart contract and corresponding unit test.

~~~~solidity

// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {
    string private text;
    address public owner;

    modifier onlyOwner() {
        require(msg.sender == owner, "Caller is not the owner");
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

## Test File: HelloWorld.ts

This file tests all critical functionalities of the contract.


~~~~ts
import { viem } from "hardhat";
import { expect } from "chai";
import { loadFixture } from "@nomicfoundation/hardhat-toolbox-viem/network-helpers";

describe("HelloWorld", function () {
  async function deployContractFixture() {
    const publicClient = await viem.getPublicClient();
    const [owner, otherAccount] = await viem.getWalletClients();
    const helloWorldContract = await viem.deployContract("HelloWorld");

    return {
      publicClient,
      owner,
      otherAccount,
      helloWorldContract,
    };
  }

  it("Should give a Hello World", async function () {
    const { helloWorldContract } = await loadFixture(deployContractFixture);
    const helloWorldText = await helloWorldContract.read.helloWorld();
    expect(helloWorldText).to.equal("Hello World");
  });

  it("Should set owner to deployer account", async function () {
    const { helloWorldContract, owner } = await loadFixture(deployContractFixture);
    const contractOwner = await helloWorldContract.read.owner();
    expect(contractOwner.toLowerCase()).to.equal(owner.account.address);
  });

  it("Should not allow anyone other than owner to call transferOwnership", async function () {
    const { helloWorldContract, otherAccount } = await loadFixture(deployContractFixture);
    const helloWorldContractAsOtherAccount = await viem.getContractAt(
      "HelloWorld",
      helloWorldContract.address,
      { client: { wallet: otherAccount } }
    );
    await expect(
      helloWorldContractAsOtherAccount.write.transferOwnership([
        otherAccount.account.address,
      ])
    ).to.be.rejectedWith("Caller is not the owner");
  });

  it("Should execute transferOwnership correctly", async function () {
    const { publicClient, helloWorldContract, owner, otherAccount } =
      await loadFixture(deployContractFixture);
    const txHash = await helloWorldContract.write.transferOwnership([
      otherAccount.account.address,
    ]);
    const receipt = await publicClient.getTransactionReceipt({ hash: txHash });
    expect(receipt.status).to.equal("success");

    const contractOwner = await helloWorldContract.read.owner();
    expect(contractOwner.toLowerCase()).to.equal(otherAccount.account.address);

    const helloWorldContractAsPreviousAccount = await viem.getContractAt(
      "HelloWorld",
      helloWorldContract.address,
      { client: { wallet: owner } }
    );
    await expect(
      helloWorldContractAsPreviousAccount.write.transferOwnership([
        owner.account.address,
      ])
    ).to.be.rejectedWith("Caller is not the owner");
  });

  it("Should not allow anyone other than owner to change text", async function () {
    // TODO: Implement test
    throw Error("Not implemented");
  });

  it("Should change text correctly", async function () {
    // TODO: Implement test
    throw Error("Not implemented");
  });
});


~~~~


## Notes, Tips & Warnings

**Tip:**
Use loadFixture() to reuse the same blockchain state between tests and speed up execution.

**Warning:**
Always check require() messages and revert reasons — they are crucial for identifying access control vulnerabilities.

**Note:**
Mocha’s default timeout can be increased using:


~~~~ts
this.timeout(50000);
~~~~

---

## Summary

| Concept                 | Description                                                     |
| :---------------------- | :-------------------------------------------------------------- |
| **Framework**           | Hardhat + Viem                                                  |
| **Test Runner**         | Mocha                                                           |
| **Assertions**          | Chai                                                            |
| **Pattern**             | TDD                                                             |
| **Contract Under Test** | `HelloWorld.sol`                                                |
| **Goals**               | Validate owner-only functions, ownership transfer, text updates |


---




