# Decentralized Stablecoin (DSC) System

## Overview

This project implements a **Decentralized Stablecoin (DSC)** system, designed to maintain a 1:1 USD peg using crypto-collateralized assets. The system is **overcollateralized**, algorithmically stable, and minimalistic, similar to MakerDAO’s DAI but without governance or fees.  

The core contract is [`DSCEngine.sol`](./DSCEngine.sol), which handles:

- Depositing and redeeming collateral (WETH, WBTC, or other allowed ERC20 tokens)  
- Minting and burning DSC tokens  
- Liquidating undercollateralized accounts  
- Maintaining health factor to prevent insolvency  

---

## Key Features

- **Dollar-Pegged:** DSC aims to maintain $1 value per token.  
- **Exogenously Collateralized:** Users deposit ERC20 tokens like WETH/WBTC as collateral.  
- **Overcollateralization:** Ensures system solvency. Collateral must always exceed minted DSC value.  
- **Liquidation Mechanism:** Undercollateralized positions can be liquidated with a bonus incentive.  
- **Secure Design:** Uses OpenZeppelin’s `ReentrancyGuard` and checks for zero amounts, allowed tokens, and health factor violations.

---

## Contracts

### DSCEngine.sol

This is the core contract controlling the DSC system. It integrates:

- **Collateral Management:** Deposit and redeem collateral tokens.
- **DSC Minting & Burning:** Safely mint and burn DSC tokens with health factor checks.
- **Liquidation:** Partial liquidation of undercollateralized users with bonus for liquidators.
- **Health Factor Calculations:** Ensures all users maintain minimum collateralization.

#### Important Constants

| Constant                     | Description |
|-------------------------------|-------------|
| `LIQUIDATION_THRESHOLD`       | Collateral must be 200% of debt (50% threshold). |
| `LIQUIDATION_BONUS`           | Liquidators receive 10% bonus collateral. |
| `MIN_HEALTH_FACTOR`           | Minimum acceptable health factor (1e18). |
| `PRECISION`                   | Internal precision for calculations (1e18). |
| `ADDITIONAL_FEED_PRECISION`   | Used to normalize Chainlink price feeds (1e10). |

---

### DecentralizedStableCoin.sol

- ERC20 token representing the stablecoin.
- Minted and burned exclusively by the `DSCEngine`.
- 1 DSC = $1 USD (pegged).

---

## Usage

### Deposit Collateral & Mint DSC

```solidity
dscEngine.depositCollateralAndMintDsc(tokenAddress, collateralAmount, dscAmount);
Redeem Collateral for DSC
solidity
Copy
Edit
dscEngine.redeemCollateralForDsc(tokenAddress, collateralAmount, dscAmountToBurn);
Burn DSC Without Redeeming Collateral
solidity
Copy
Edit
dscEngine.burnDsc(dscAmount);
Liquidate an Undercollateralized User
solidity
Copy
Edit
dscEngine.liquidate(collateralToken, user, debtToCover);
Health Factor
The health factor ensures users maintain sufficient collateral:

ini
Copy
Edit
healthFactor = (collateralValue * LIQUIDATION_THRESHOLD) / totalDscMinted
healthFactor < 1 → user can be liquidated

healthFactor >= 1 → safe

Collateral Tokens
Supported ERC20 tokens must have a linked Chainlink price feed.

The constructor takes arrays of token addresses and their corresponding price feeds.

solidity
Copy
Edit
constructor(address[] memory tokenAddresses, address[] memory priceFeeds, address dscAddress) {}
Security
ReentrancyGuard: Prevents reentrancy attacks on deposits, withdrawals, and liquidation.

Input Validations: Checks for zero amounts and allowed tokens.

Health Factor Enforcement: Users cannot mint or redeem in ways that violate minimum collateral requirements.

License
This project is licensed under the MIT License.