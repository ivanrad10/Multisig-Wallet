# Multisig-Wallet
This project implements a Multisig Wallet on Solana using the Anchor framework. A multisig wallet requires multiple signatures from predefined owners to approve and execute transactions, providing an additional layer of security.

## Table of Contents
1. Programs
2. Backend
3. Frontend
---

# Programs

## Why PDAs (Program Derived Addresses)?

A **PDA (Program Derived Address)** is used for creating a unique address that is controlled by the program and not associated with any private key. In the context of this multisig wallet:
- **Security**: PDAs ensure that only the program has control over the wallet's transactions, preventing unauthorized access or manipulation.
- **Uniqueness**: The PDA is derived using the hashed list of all owner public keys, ensuring a unique address for each multisig wallet based on its owners.
- **Determinism**: PDAs are deterministic, meaning the same input (owners list) will always produce the same PDA, ensuring a consistent wallet address.

The multisig wallet PDA is created using:
```rust
#[account(
    init,
    payer = signer, 
    space = ...,
    seeds = [&hash_owners_pubkey(&owners)],  
    bump
)]
pub multisig_wallet: Account<'info, MultisigWallet>,
```

## Key Functions Explained:
```rust
pub fn initialize_multisig(ctx: Context<InitializeMultisig>, name: String, owners: Vec<Pubkey>, threshold: u8) -> Result<()>
```
- This function initializes a new multisig wallet

```rust
pub fn create_transaction(ctx: Context<CreateTransaction>, amount: u64, recipient: Pubkey) -> Result<()>
```
- This function allows an owner to create a transaction

```rust
pub fn approve_transaction(ctx: Context<SignTransaction>, id: u32) -> Result<()>
```
- Allows an owner to approve a transaction and executes the transaction if the desired threshold is reached

```rust
pub fn revoke_transaction(ctx: Context<SignTransaction>, id: u32) -> Result<()>
```
- Allows an owner to revoke their approval of a transaction

``` rust
pub fn fund_multisig(ctx: Context<FundMultisig>, amount: u64) -> Result<()>
```
- Allows any signer to transfer SOL into the multisig wallet

# Backend

The backend service is responsible for securely hashing the owners' public keys to create a unique seed for the PDA. This process ensures that each multisig wallet on the Solana blockchain has a unique and deterministic address based on its owners, enhancing both security and address consistency.

# Frontend

# Multisig Wallet Frontend

The frontend of this project is designed as an intuitive user interface for interacting with the multisig wallet. It provides the following functionalities:

## Functionalities

### 1. Wallet Connection
The user can connect their Solana wallet using the available wallet providers. Once connected, the interface displays all the multisig wallets that the user is a part of.

### 2. Display of Multisig Wallets
After connecting the wallet, the user can see a list of multisig wallets they have access to, each showing important details such as the wallet name, balance in SOL, threshold for transaction approvals, and the number of owners.

### 3. Multisig Wallet Overview
By selecting a specific multisig wallet, the user is taken to a detailed view showing:
- **Wallet balance**: The current balance in SOL.
- **Owners**: A list of all owners associated with the multisig wallet.
- **Threshold**: The minimum number of signatures required for transaction approval.
- **Transactions**: Separate sections for unapproved, approved, and executed transactions.

### 4. Transaction Management
The user can create, approve and revoke:
- **Send**: Allows an owner to create a new transaction specifying the amount and recipient address.
- **Approve Transactions**: Owners can approve their approval of unapproved transactions(they can select multiple transactions). If the desired threshold is reached, the transaction automatically executes.
- **Revoke Transactions**: Owners can revoke their approval of approved transactions(they can select multiple transactions).

### 5. Funding the Multisig Wallet
The user(signer) can deposit SOL into the multisig wallet through a simple form that specifies the amount to be transferred from their connected wallet.
