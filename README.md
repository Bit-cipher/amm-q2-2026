# amm-q2-2026
A constant-product Automated Market Maker (AMM) built with Anchor on Solana.
This project implements the classic x * y = k invariant and supports:

Token swaps
Liquidity deposits
Liquidity withdrawals
LP token minting
Fee-based trading

# Program ID
DTZViu3qWWCJiCS6sMu58gGECdrQSJSBE2yhxjj5oWEq

# Overview

This AMM allows users to:

create liquidity pools
provide liquidity
earn LP tokens
swap between two assets
withdraw liquidity proportionally

The pricing logic follows the constant-product formula:
x * y = k

where:

x = reserve of token X
y = reserve of token Y
k = invariant constant

As swaps happen, token reserves rebalance automatically while preserving the invariant.

# Features

Constant-product swap curve
Slippage protection
LP token minting and burning
Configurable swap fees
Token-2022 support
PDA-based vault architecture
Fast local testing using LiteSVM
Fully written in Anchor

# Architecture
## Config Account

Each liquidity pool has a single Config PDA that stores all pool metadata.

| Field       | Type           | Description                               |
| ----------- | -------------- | ----------------------------------------- |
| seed        | u64            | Unique seed used to derive the config PDA |
| authority   | Option<Pubkey> | Optional admin authority                  |
| mint_x      | Pubkey         | Token X mint                              |
| mint_y      | Pubkey         | Token Y mint                              |
| fee         | u16            | Swap fee in basis points                  |
| locked      | bool           | Pauses swaps/deposits if true             |
| config_bump | u8             | PDA bump for config                       |
| lp_bump     | u8             | PDA bump for LP mint                      |


# PDA Structure

| Account     | Seeds                            |
| ----------- | -------------------------------- |
| Config PDA  | `["config", seed.to_le_bytes()]` |
| LP Mint PDA | `["lp", config.key()]`           |
| Vault X     | ATA owned by config PDA          |
| Vault Y     | ATA owned by config PDA          |

The vault token accounts are controlled entirely by the program.

# Constant Product Formula
x * y = k
After each trade:

one reserve increases
the other decreases
the invariant remains preserved

Swap fees are deducted before output calculation.

# Tech Stack

| Technology             | Purpose                        |
| ---------------------- | ------------------------------ |
| Anchor                 | Solana framework               |
| anchor-spl             | SPL token utilities            |
| token_interface        | SPL + Token-2022 compatibility |
| constant-product-curve | Pricing math                   |
| LiteSVM                | Fast local testing             |
| Rust                   | Smart contract development     |

# Testing

The project uses LiteSVM for fast in-process testing without requiring a local validator.

Current test coverage includes:
test test_initialize ... ok
test test_deposit    ... ok
test test_withdraw   ... ok
test test_swap       ... ok


# Project Structure

programs/amm/
├── src/
│   ├── lib.rs
│   ├── state.rs
│   ├── error.rs
│   ├── constants.rs
│   └── instructions/
│       ├── initialize.rs
│       ├── deposit.rs
│       ├── withdraw.rs
│       └── swap.rs
└── tests/
    ├── tests.rs
    └── ix_handlers/
        ├── init.rs
        ├── deposit.rs
        ├── withdraw.rs
        └── swap.rs
