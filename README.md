# Yaroslav Staking Vault – Ethereum DeFi Staking Smart Contracts

**Production-ready Ethereum staking vault** for the Yaroslav (YARO) token with rewards in Yaroslav Reward Token (rYARO). Stake YARO, earn rYARO. Built with Solidity, Hardhat, TypeScript, and Next.js.

[![Solidity](https://img.shields.io/badge/Solidity-^0.8.20-blue)](https://soliditylang.org/)
[![Hardhat](https://img.shields.io/badge/Hardhat-2.19-yellow)](https://hardhat.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

---

## Author & Contact

| | |
|---|---|
| **Author** | **KuchikiRenji** |
| **Email** | [KuchikiRenji@outlook.com](mailto:KuchikiRenji@outlook.com) |
| **GitHub** | [github.com/KuchikiRenji](https://github.com/KuchikiRenji) |
| **Discord** | `kuchiki_renji` |

For questions, collaboration, or support, reach out via email or Discord.

---

## Table of Contents

- [What Is This Project?](#what-is-this-project)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Development](#development)
- [Deployment](#deployment)
- [Frontend](#frontend)
- [Testing](#testing)
- [Gas Optimization](#gas-optimization)
- [Security](#security)
- [Architecture & Docs](#architecture--docs)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## What Is This Project?

**Yaroslav Staking Vault** is a **DeFi staking protocol** on Ethereum that lets users:

- **Stake** YARO (ERC-20) tokens in a secure vault
- **Earn** rYARO rewards from a configurable reward rate (accumulator-based, gas-efficient)
- **Withdraw** staked tokens and **claim** rewards anytime
- Use an **admin panel** (owner-only) for vault management: reward rate, funding, pause

The smart contracts use **OpenZeppelin** (ReentrancyGuard, Pausable, Ownable, SafeERC20), include a full **test suite** with high coverage, and ship with a **Next.js + Wagmi** frontend for Sepolia testnet.

---

## Features

- **ERC-20 staking & reward tokens**: YARO (stake) and rYARO (rewards)
- **Initial token distribution** on deploy (deployer, treasury, liquidity pool)
- **Efficient reward math** via accumulator pattern (MasterChef-style)
- **Security**: ReentrancyGuard, Pausable, Ownable, SafeERC20
- **Tests**: Comprehensive suite, >90% coverage
- **Frontend**: Next.js app with Wagmi; also a `dapp/` with RainbowKit
- **Deployment**: Scripts for Sepolia; optional Etherscan verification
- **Gas-optimized** design and minimal storage writes

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| **Smart contracts** | Solidity ^0.8.20, OpenZeppelin Contracts |
| **Dev & build** | Hardhat, TypeScript, Ethers v6 |
| **Testing** | Mocha, Chai, Hardhat Network Helpers |
| **Frontend** | Next.js 14, React, Wagmi, Viem (and dapp: RainbowKit) |
| **Code quality** | ESLint, Prettier |

---

## Project Structure

```
staking-vault-contracts/
├── contracts/
│   ├── Yaroslav.sol              # Staking token (YARO)
│   ├── rYaroslav.sol             # Reward token (rYARO)
│   └── YaroslavStakingVault.sol  # Main staking vault
├── scripts/
│   ├── deploy.ts                 # Deploy YARO, rYARO, vault
│   ├── fundRewards.ts            # Fund vault with rYARO
│   └── verify.ts                 # Contract verification
├── test/
│   ├── staking.test.ts           # Staking & vault tests
│   └── reward-model.test.ts      # Reward calculation tests
├── frontend/                     # Next.js staking UI (Wagmi)
│   ├── pages/                    # index, admin
│   └── components/               # WalletConnect, StakingCard
├── dapp/                         # Next.js 14 + RainbowKit DApp
│   ├── app/                      # App Router, dashboard
│   └── components/               # ConnectWallet, StakingCard, RewardsCard
├── docs/
│   ├── architecture.md           # System & contract design
│   ├── security.md               # Security considerations
│   └── token-distribution.md     # Token distribution guide
├── hardhat.config.ts
├── package.json
└── README.md
```

---

## Quick Start

```bash
git clone https://github.com/KuchikiRenji/staking-vault-contracts.git
cd staking-vault-contracts
npm install
cp env.example .env
# Edit .env with SEPOLIA_RPC_URL, DEPLOYER_PRIVATE_KEY, etc.
npm run compile
npm run test
npx hardhat run scripts/deploy.ts --network sepolia
```

Then run the frontend (see [Frontend](#frontend)).

---

## Installation

### Prerequisites

- **Node.js** ≥ 20
- **npm** or yarn
- **Ethereum wallet** with Sepolia ETH (for deployment)

### Steps

1. **Clone the repo**
   ```bash
   git clone https://github.com/KuchikiRenji/staking-vault-contracts.git
   cd staking-vault-contracts
   ```

2. **Install root dependencies**
   ```bash
   npm install
   ```

3. **Install frontend dependencies** (if using `frontend/` or `dapp/`)
   ```bash
   cd frontend && npm install && cd ..
   # and/or: cd dapp && npm install && cd ..
   ```

4. **Environment**
   ```bash
   cp env.example .env
   ```
   Fill `.env`:
   ```env
   SEPOLIA_RPC_URL=https://sepolia.infura.io/v3/YOUR_INFURA_KEY
   DEPLOYER_PRIVATE_KEY=your_private_key_here
   ETHERSCAN_API_KEY=your_etherscan_api_key_here
   NEXT_PUBLIC_RPC_URL=https://sepolia.infura.io/v3/YOUR_INFURA_KEY
   NEXT_PUBLIC_CONTRACT_ADDRESSES='{"Yaroslav":"0x...","rYaroslav":"0x...","YaroslavStakingVault":"0x..."}'
   ```
   Use the addresses printed after deployment for `NEXT_PUBLIC_CONTRACT_ADDRESSES`.

---

## Development

### Compile contracts

```bash
npm run compile
```

### Run tests

```bash
npm run test
```

### Test coverage

```bash
npm run coverage
```
Target: **≥90% coverage**.

### Gas report

```bash
REPORT_GAS=true npm run test
```
Output is written to `gas-report.txt` when the gas reporter is enabled.

---

## Deployment

### Deploy to Sepolia

1. Set `SEPOLIA_RPC_URL` and `DEPLOYER_PRIVATE_KEY` in `.env`.
2. Ensure the deployer wallet has Sepolia ETH.
3. Optional: set `TREASURY_ADDRESS` and `LIQUIDITY_POOL_ADDRESS` for custom distribution.

4. Deploy:
   ```bash
   npx hardhat run scripts/deploy.ts --network sepolia
   ```
   The script deploys YARO (1B supply: 40% deployer, 30% treasury, 30% liquidity), rYARO (100M: 50% deployer, 50% treasury), and **YaroslavStakingVault**, then prints addresses and a ready-to-use `NEXT_PUBLIC_CONTRACT_ADDRESSES` line.

5. **Fund the vault** with rYARO:
   ```bash
   RYAROSLAV_ADDRESS=0x... VAULT_ADDRESS=0x... npx hardhat run scripts/fundRewards.ts --network sepolia
   ```

6. **Frontend**: Copy the printed contract addresses into `frontend/.env.local` or `dapp/.env.local` as `NEXT_PUBLIC_CONTRACT_ADDRESSES` and `NEXT_PUBLIC_RPC_URL`.

Details: [docs/token-distribution.md](./docs/token-distribution.md).

### Verify on Etherscan

If `ETHERSCAN_API_KEY` is set, the deploy script can verify contracts automatically. For manual verification:

```bash
npx hardhat verify --network sepolia <CONTRACT_ADDRESS> <CONSTRUCTOR_ARGS>
```

---

## Frontend

### Run locally (`frontend/`)

```bash
cd frontend
# Create .env.local with NEXT_PUBLIC_RPC_URL and NEXT_PUBLIC_CONTRACT_ADDRESSES
npm run dev
```
Open [http://localhost:3000](http://localhost:3000).

### Run DApp (`dapp/`)

```bash
cd dapp
# Create .env.local (see dapp/README.md for variables)
npm run dev
```

### Features

- **Dashboard**: Connect wallet, view staked balance and pending rewards, deposit / withdraw / claim.
- **Admin**: Set reward rate, fund rewards (owner only).

### Deploy to Vercel

Push to GitHub, import the repo in Vercel, add the same env vars, and deploy. See `dapp/VERCEL_DEPLOYMENT.md` for details.

---

## Testing

- **Unit tests**: `npm run test` — deposit, withdraw, claim, reward math, admin, pause, edge cases.
- **Coverage**: `npm run coverage` — aim for ≥90%.

**Manual flow**: Deploy to Sepolia → mint YARO to a test wallet → connect in the frontend → approve → deposit → wait → claim → withdraw.

---

## Gas Optimization

- Accumulator updated only when needed.
- Single reward calculation pattern; auto-claim on deposit/withdraw to avoid extra claims.
- Run `REPORT_GAS=true npm run test` for a gas report.

---

## Security

See [docs/security.md](./docs/security.md) for full details.

Highlights:

- **ReentrancyGuard** on state-changing functions
- **Pausable** for emergency stop
- **Ownable** for admin-only functions
- **SafeERC20** for token transfers
- Input validation and checks-effects-interactions

**Disclaimer:** This is testnet-oriented. For mainnet, get a professional audit and use a multi-sig for the owner.

---

## Architecture & Docs

- [docs/architecture.md](./docs/architecture.md) — system design, reward formula, data flow
- [docs/security.md](./docs/security.md) — security model and recommendations
- [docs/token-distribution.md](./docs/token-distribution.md) — initial distribution and customization
- [DEPLOYMENT_NOTES.md](./DEPLOYMENT_NOTES.md) — deployment checklist and troubleshooting

---

## Troubleshooting

| Issue | What to check |
|-------|----------------|
| **Insufficient funds** | Deployer has Sepolia ETH; check gas price. |
| **Frontend can’t see contracts** | Correct `NEXT_PUBLIC_CONTRACT_ADDRESSES` (valid JSON), correct RPC, browser console. |
| **Tests fail** | Run `npm run compile`, reinstall deps, check Hardhat config. |
| **Verification fails** | Wait 30–60 s after deploy; constructor args must match exactly; valid Etherscan API key. |

---

## Contributing

1. Fork the repository.
2. Create a feature branch.
3. Make changes and add tests for new behavior.
4. Ensure tests pass and coverage stays ≥90%.
5. Open a pull request.

---

## License

MIT

---

## Support

- **Author**: KuchikiRenji — [KuchikiRenji@outlook.com](mailto:KuchikiRenji@outlook.com), [GitHub](https://github.com/KuchikiRenji), Discord: `kuchiki_renji`
- Open an issue on GitHub for bugs or feature requests.
- See the `docs/` folder and test files for usage examples.

---

**Keywords:** Ethereum staking, DeFi staking vault, YARO token, rYARO rewards, Solidity staking contract, Hardhat, ERC-20 staking, Yaroslav Staking Vault, blockchain staking, Sepolia testnet.
