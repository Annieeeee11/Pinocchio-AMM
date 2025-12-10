# Pinocchio AMM
[![Rust](https://img.shields.io/badge/Rust-%23000000.svg?e&logo=rust&logoColor=white)](#)
[![Solana](https://img.shields.io/badge/Solana-9945FF?logo=solana&logoColor=fff)](#)

This project is my implementation of an Automated Market Maker (AMM) built using Pinocchio, following the [Blueshift Pinocchio AMM Challenge](https://learn.blueshift.gg/en/challenges/pinocchio-amm).

## What I Learned

### 1. **What is an AMM?**
An Automated Market Maker (AMM) is like a self operating liquidity pool. Instead of needing buyers and sellers to match orders, users can swap tokens directly with a smart contract. The AMM uses a mathematical formula to automatically determine prices and facilitate trades.

### 2. **The Constant Product Formula**
The core of this AMM uses the constant product curve: `x * y = k`

- `x` = amount of token X in the pool
- `y` = amount of token Y in the pool  
- `k` = a constant that never changes

When someone swaps tokens, the pool adjusts so that `k` stays the same. This creates automatic price discovery based on supply and demand.

### 3. **Key Concepts from Pinocchio**

#### **Program Structure**
- Programs are organized into modules (instructions, state)
- Entry point uses a discriminator pattern to route instructions
- Each instruction has its own module for better organization

#### **State Management**
- Used `#[repr(C)]` to ensure predictable memory layout for on-chain data
- Stored multi byte values (u64, u16) as byte arrays to avoid alignment issues
- Created safe getter/setter methods with validation
- Used `Ref` and `RefMut` for safe borrowing of account data

#### **Account Validation**
- Always validate account data length
- Always check account ownership
- Use PDAs (Program Derived Addresses) for deterministic account addresses

### 4. **The Four Core Instructions**

#### **Initialize**
- Sets up the AMM by creating a config account
- Mints the LP (Liquidity Provider) token
- Stores important data like token mints, fees, and authority
- Uses PDAs with seeds for deterministic addresses

#### **Deposit**
- Allows users to add both tokens to the pool
- Users receive LP tokens proportional to their contribution
- First liquidity provider sets the initial ratio
- Subsequent providers get LP tokens based on their share

#### **Withdraw**
- Users burn LP tokens to get back their share of both tokens
- Also receive a share of accumulated fees
- Removes liquidity from the pool

#### **Swap**
- Users can trade one token for another
- Uses the constant product formula to calculate output amounts
- Charges a fee (in basis points) that goes to liquidity providers
- Fee is added back to the pool, increasing LP token value over time

### 5. **Important Technical Learnings**

#### **Memory Safety**
- Used unsafe blocks carefully for byte to struct conversion
- Always validated data before unsafe operations
- Used `Ref` and `RefMut` to prevent data races

#### **Fee System**
- Fees are stored in basis points (1 basis point = 0.01%)
- Maximum fee is 10,000 basis points (100%)
- Fees are collected on swaps and distributed to LPs

#### **State Management**
- AMM can be in different states: Uninitialized, Initialized, Disabled, WithdrawOnly
- Authority can be set or made immutable (all zeros)
- State transitions are validated

#### **PDA (Program Derived Address) Usage**
- Config account uses seeds: `["config", seed, mint_x, mint_y, bump]`
- LP mint uses seeds: `["mint_lp", config_key, bump]`
- PDAs allow programs to sign for accounts they own

## Running the Program

To build:

```bash
cargo build-sbf
```
