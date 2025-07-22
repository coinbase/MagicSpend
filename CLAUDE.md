# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

MagicSpend is an ERC-4337 compliant paymaster smart contract that allows onchain accounts to present valid Withdraw Requests and receive funds. It's part of Coinbase's broader MagicSpend product enabling seamless asset usage onchain.

The core contract (`src/MagicSpend.sol`) implements:
- ERC-4337 paymaster functionality (EntryPoint v0.6 compatible)
- Withdraw request validation with EIP-191 compliant signatures
- Gas payment capabilities for smart contract wallets
- Fund transfers during execution

## Development Commands

This is a Foundry-based Solidity project. Common development commands:

### Testing
```bash
# Run all tests
forge test

# Run specific test file
forge test --match-path test/MagicSpend.t.sol

# Run tests with gas reporting
forge test --gas-report

# Run echidna fuzzing tests
make echidna-test
```

### Building and Deployment
```bash
# Build contracts
forge build

# Deploy (requires .env file with RPC_URL and PRIVATE_KEY)
make deploy
```

## Architecture

### Core Contract Structure
- `src/MagicSpend.sol` - Main contract implementing IPaymaster and Ownable
- Key struct: `WithdrawRequest` containing signature, asset, amount, nonce, and expiry
- Uses Solady libraries for optimized operations (SignatureCheckerLib, SafeTransferLib)
- Integrates with account-abstraction interfaces

### Test Organization
- `test/` contains comprehensive unit tests for each contract function
- `test/PaymasterMagicSpendBase.sol` - Base test contract with common setup
- `test/echidna/` contains fuzzing tests using Echidna
- Tests cover three main flows: gas payment only, fund transfer only, and combined

### Dependencies
- **Solady** - Gas-optimized library for auth, signatures, and transfers
- **OpenZeppelin Contracts** - Security utilities
- **Account Abstraction** - ERC-4337 interfaces and UserOperation types
- **Smart Wallet** - Coinbase's smart contract wallet implementation
- **Forge Std** - Foundry testing framework

### Key Configuration
- `foundry.toml` - Optimized for 999999 runs, custom remappings for dependencies
- `echidna-config.yaml` - Configuration for property-based fuzzing tests
- Maximum withdraw denominator prevents excessive withdrawals affecting paymaster reputation

### Development Notes
- Contract uses Solidity 0.8.23
- Heavy focus on gas optimization (999999 optimizer runs)
- Comprehensive test coverage including integration and property-based tests
- EIP-191 signature validation for withdraw requests
- Nonce tracking prevents replay attacks