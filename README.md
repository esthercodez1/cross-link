# CrossLink Protocol

[![Clarity](https://img.shields.io/badge/Clarity-Smart%20Contract-blue)](https://clarity-lang.org/)
[![Stacks](https://img.shields.io/badge/Stacks-Blockchain-orange)](https://stacks.co/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## Overview

CrossLink Protocol is a next-generation cross-chain infrastructure that establishes a trustless, validator-governed bridge between Bitcoin and Stacks ecosystems. Built with institutional-grade security and designed for high-throughput asset migration.

## Features

### 🔒 **Security First**

- **Multi-signature validation system** with configurable thresholds
- **Time-locked emergency mechanisms** for unprecedented security
- **Cryptographic signature verification** for all transactions
- **Comprehensive input validation** and error handling

### ⚡ **Performance & Reliability**

- **Atomic transaction processing** with comprehensive validation
- **Real-time balance synchronization** across chains
- **High-throughput asset migration** capabilities
- **Robust error handling** with detailed event logging

### 🛡️ **Governance & Control**

- **Decentralized validator consensus** with configurable thresholds
- **Enterprise-grade pause/resume** functionality
- **Emergency withdrawal mechanisms** with timelock protection
- **Validator management** with dynamic add/remove capabilities

## Architecture

### Core Components

1. **Validator Network**: Decentralized network of validators ensuring transaction authenticity
2. **Bridge Mechanism**: Secure asset transfer system between Bitcoin and Stacks
3. **Emergency Controls**: Time-locked safety mechanisms for critical situations
4. **Balance Management**: Real-time tracking and synchronization of cross-chain balances

### Security Model

The protocol implements a multi-layered security approach:

- **Minimum 3 validators** required for network operation
- **6 confirmations** required for transaction finality
- **24-hour timelock** on emergency withdrawals
- **Amount validation** with configurable min/max limits

## Smart Contract Interface

### Public Functions

#### Bridge Management

```clarity
(initialize-bridge) -> (response bool uint)
(pause-bridge) -> (response bool uint)
```

#### Validator Management

```clarity
(add-validator (validator principal)) -> (response bool uint)
(remove-validator (validator principal)) -> (response bool uint)
```

#### Transaction Processing

```clarity
(initiate-deposit (tx-hash (buff 32)) (amount uint) (recipient principal) (btc-sender (buff 33))) -> (response bool uint)
(confirm-deposit (tx-hash (buff 32)) (signature (buff 65))) -> (response bool uint)
(withdraw (amount uint) (btc-recipient (buff 34))) -> (response bool uint)
(emergency-withdraw (amount uint) (recipient principal)) -> (response bool uint)
```

### Read-Only Functions

```clarity
(get-validator-status (validator principal)) -> bool
(get-bridge-balance (user principal)) -> uint
(validate-deposit-amount (amount uint)) -> bool
(is-valid-tx-hash (tx-hash (buff 32))) -> bool
(is-valid-signature (signature (buff 65))) -> bool
(is-valid-recipient (recipient principal)) -> bool
```

## Error Codes

| Code | Constant | Description |
|------|----------|-------------|
| 1000 | ERROR-NOT-AUTHORIZED | Caller not authorized for this operation |
| 1001 | ERROR-INVALID-AMOUNT | Amount outside valid range |
| 1002 | ERROR-INSUFFICIENT-BALANCE | Insufficient balance for operation |
| 1003 | ERROR-INVALID-BRIDGE-STATUS | Invalid bridge state for operation |
| 1004 | ERROR-INVALID-SIGNATURE | Invalid cryptographic signature |
| 1005 | ERROR-ALREADY-PROCESSED | Transaction already processed |
| 1006 | ERROR-BRIDGE-PAUSED | Bridge is currently paused |
| 1007 | ERROR-INVALID-VALIDATOR-ADDRESS | Invalid validator address |
| 1008 | ERROR-INVALID-RECIPIENT-ADDRESS | Invalid recipient address |
| 1009 | ERROR-INVALID-BTC-ADDRESS | Invalid Bitcoin address |
| 1010 | ERROR-INVALID-TX-HASH | Invalid transaction hash |
| 1011 | ERROR-INSUFFICIENT-VALIDATORS | Not enough active validators |
| 1012 | ERROR-TIMELOCK-NOT-EXPIRED | Emergency timelock still active |

## Configuration

### Protocol Constants

```clarity
MIN-DEPOSIT-AMOUNT: 100,000 satoshis
MAX-DEPOSIT-AMOUNT: 1,000,000,000 satoshis
REQUIRED-CONFIRMATIONS: 6 blocks
MIN-VALIDATORS: 3 validators
EMERGENCY-TIMELOCK: 144 blocks (~24 hours)
```

## Development Setup

### Prerequisites

- [Clarinet](https://github.com/hirosystems/clarinet) CLI tool
- Node.js (v16 or higher)
- npm or yarn

### Installation

```bash
# Clone the repository
git clone https://github.com/esthercodez1/cross-link.git
cd cross-link

# Install dependencies
npm install

# Initialize Clarinet project (if needed)
clarinet check
```

### Testing

```bash
# Run contract checks
clarinet check

# Run unit tests
npm test

# Run specific test file
npm test -- cross-link.test.ts
```

### Local Development

```bash
# Start local development environment
clarinet integrate

# Deploy to local testnet
clarinet deploy --testnet
```

## Usage Examples

### Initializing the Bridge

```clarity
;; Initialize bridge (deployer only)
(contract-call? .cross-link initialize-bridge)
```

### Adding Validators

```clarity
;; Add a new validator (deployer only)
(contract-call? .cross-link add-validator 'ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM)
```

### Processing Deposits

```clarity
;; Initiate deposit (validator only)
(contract-call? .cross-link initiate-deposit 
  0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
  u1000000
  'ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM
  0x021234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef12)

;; Confirm deposit (validator only)
(contract-call? .cross-link confirm-deposit
  0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
  0x3045022100abcd...signature...1234022067890abcdef...)
```

### Withdrawals

```clarity
;; Regular withdrawal
(contract-call? .cross-link withdraw
  u500000
  0x1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2)

;; Emergency withdrawal (deployer only, after timelock)
(contract-call? .cross-link emergency-withdraw
  u1000000
  'ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM)
```

## Security Considerations

### Validator Security

- Validators must be carefully vetted before addition
- Private keys should be stored in secure, offline environments
- Multi-signature schemes recommended for validator operations

### Emergency Procedures

- Emergency withdrawals have a 24-hour timelock
- Bridge can be paused immediately if threats are detected
- All emergency actions are logged on-chain for transparency

### Best Practices

- Always validate input parameters
- Monitor bridge balance and total bridged amounts
- Implement proper access controls for administrative functions
- Regular security audits recommended

## Events and Logging

The contract emits detailed events for monitoring and debugging:

```clarity
;; Deposit confirmation event
{
  type: "deposit-confirmed",
  tx-hash: (buff 32),
  amount: uint,
  recipient: principal
}

;; Withdrawal event
{
  type: "withdraw",
  sender: principal,
  amount: uint,
  btc-recipient: (buff 34),
  timestamp: uint
}

;; Emergency withdrawal event
{
  type: "emergency-withdraw",
  recipient: principal,
  amount: uint
}
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines

- Follow Clarity best practices and style guide
- Write comprehensive tests for new features
- Update documentation for any API changes
- Ensure all tests pass before submitting PR

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**CrossLink Protocol** - Bridging Bitcoin and Stacks with institutional-grade security.
