# ERC20 Token and Vault Contract

This project contains two Solidity smart contracts: an ERC20 token contract and a Vault contract. The ERC20 contract implements a standard token with features like minting, burning, and allowances, while the Vault contract allows users to deposit and withdraw tokens in exchange for shares representing their stake in the Vault.

## Contracts Overview

### ERC20 Contract
This contract implements a custom ERC20 token with the following attributes:
- **Name**: `nitin`
- **Symbol**: `NTN`
- **Decimals**: 18

#### Code:
  ```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "nitin";
    string public symbol = "NTN";
    uint8 public decimals = 18;

	event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```
#### Explaination

The `ERC20.sol` contract implements a basic ERC20 token with the following key features:

##### 1. **State Variables:**
- **`totalSupply`**: Keeps track of the total supply of the token.
- **`balanceOf`**: A mapping that stores the token balance of each address.
- **`allowance`**: A mapping that defines how much one address is allowed to spend on behalf of another address.
- **`name`**, **`symbol`**, **`decimals`**: These define the token's metadata: the name is set to "nitin", the symbol is "NTN", and it uses 18 decimal places.

##### 2. **Events:**
- **`Transfer`**: Emitted when tokens are transferred from one address to another.
- **`Approval`**: Emitted when an owner approves a spender to spend tokens on their behalf.

##### 3. **Functions:**

###### - `transfer(address recipient, uint amount) external returns (bool)`
Transfers tokens from the caller's address to the `recipient`. It checks that the caller has enough balance and emits a `Transfer` event upon success.

###### - `approve(address spender, uint amount) external returns (bool)`
Allows a `spender` to use up to `amount` of tokens from the caller's balance. It sets the allowance and emits an `Approval` event.

###### - `transferFrom(address sender, address recipient, uint amount) external returns (bool)`
Transfers tokens on behalf of the `sender` to the `recipient`, provided that the `sender` has previously set an allowance for the caller. It decreases the `sender`'s allowance and emits a `Transfer` event.

###### - `mint(uint amount) external`
Creates `amount` of new tokens, increasing the total supply. These tokens are credited to the caller's balance. A `Transfer` event from the zero address (`address(0)`) is emitted to indicate token minting.

###### - `burn(uint amount) external`
Destroys `amount` of tokens from the caller's balance, decreasing the total supply. A `Transfer` event to the zero address is emitted to indicate token burning.

##### 4. **Safety Checks:**
The contract assumes that all necessary balance and allowance checks (e.g., for overflow and underflow) are handled using Solidity 0.8.x's built-in arithmetic safety features.

This ERC20 contract provides the core functionality for creating, transferring, minting, and burning tokens, as well as setting allowances for third-party transfers.


#### Main Features:
- **Minting**: Allows creating new tokens and increasing the total supply.
- **Burning**: Enables token holders to destroy tokens, reducing the total supply.
- **Transfers**: Standard token transfers between addresses.
- **Allowance & Approvals**: Supports authorizing a third party to spend tokens on behalf of the token owner.

### Vault Contract
The Vault contract enables users to deposit and withdraw ERC20 tokens. In exchange for depositing tokens, users receive Vault shares, which represent their proportionate ownership of the deposited tokens. When withdrawing, users redeem their shares to retrieve tokens.

#### Explaination :

The `Vault.sol` contract allows users to deposit ERC20 tokens and receive shares representing their ownership of the Vault's token balance.

##### 1. **State Variables:**
- **`token`**: An instance of the ERC20 token being managed by the Vault.
- **`totalSupply`**: Tracks the total number of Vault shares issued.
- **`balanceOf`**: A mapping that tracks each user's share ownership.

##### 2. **Functions:**

###### - `deposit(uint _amount)`
Allows users to deposit ERC20 tokens into the Vault. In return, users receive shares proportional to the tokens they deposit. If it’s the first deposit, the number of shares equals the amount deposited.

###### - `withdraw(uint _shares)`
Allows users to withdraw ERC20 tokens by redeeming their Vault shares. The amount of tokens withdrawn is proportional to the number of shares redeemed.

##### 3. **Internal Functions:**
- **`_mint(address _to, uint _shares)`**: Mints new shares when tokens are deposited.
- **`_burn(address _from, uint _shares)`**: Burns shares when tokens are withdrawn.

This Vault contract manages ERC20 deposits and withdrawals by minting and burning shares, ensuring users can access their proportionate token ownership.


#### Main Features:
- **Deposits**: Users deposit ERC20 tokens into the Vault and receive shares.
- **Withdrawals**: Users redeem their Vault shares to withdraw a corresponding amount of tokens.
- **Shares**: The Vault tracks each user's share, representing their stake in the Vault.

## Installation
1. Clone the repository.
    ```bash
     git clone https://github.com/your-repo/erc20-vault.git
     cd erc20-vault

2. Install the required dependencies using your preferred Solidity development environment (such as Hardhat or Truffle).
3. Compile the contracts.
    ```bash
    npx hardhat compile
4. Deploy the contracts on a blockchain network of your choice.
   ```bash
   npx hardhat run scripts/deploy.js

## Usage
- **ERC20 Token**: Interact with the token for basic operations like minting, transferring, approving, and burning tokens.
- **Vault**: Users can deposit tokens into the Vault to receive shares, or redeem shares to withdraw their share of tokens.

## License

This project is licensed under the MIT License. See the LICENSE file for more details.
