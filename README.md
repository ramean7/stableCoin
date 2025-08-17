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

🏦 Decentralized Stablecoin Engine (DSCEngine)

The DSCEngine is a decentralized stablecoin protocol that allows users to mint, burn, redeem, and liquidate a collateral-backed stablecoin (DSC). It leverages ERC20 tokens as collateral and Chainlink price feeds for secure and reliable on-chain valuations.

Core Functions

Deposit Collateral and Mint DSC

dscEngine.depositCollateralAndMintDsc(tokenAddress, collateralAmount, dscAmount);


Users deposit an ERC20 token as collateral and mint DSC against it.

Redeem Collateral for DSC

dscEngine.redeemCollateralForDsc(tokenAddress, collateralAmount, dscAmountToBurn);


Users burn DSC to unlock and redeem their collateral.

Burn DSC Without Redeeming Collateral

dscEngine.burnDsc(dscAmount);


Users can burn DSC directly to reduce their debt without withdrawing collateral.

Liquidate an Undercollateralized User

dscEngine.liquidate(collateralToken, user, debtToCover);


If a user’s position falls below the required collateral ratio, they can be liquidated. Liquidators cover part of the user’s debt and receive collateral at a discount.

Health Factor

The health factor determines whether a user’s position is safe or at risk:

healthFactor = (collateralValue * LIQUIDATION_THRESHOLD) / totalDscMinted


healthFactor < 1 → the user can be liquidated.

healthFactor >= 1 → the position is safe.

Collateral Tokens

Only approved ERC20 tokens can be used as collateral.

Each collateral token must have an associated Chainlink price feed.

Constructor:

constructor(address[] memory tokenAddresses, address[] memory priceFeeds, address dscAddress) {}

Security

ReentrancyGuard: prevents reentrancy attacks on deposits, withdrawals, and liquidations.

Input Validation: ensures zero amounts or unsupported tokens cannot be used.

Health Factor Enforcement: disallows minting or redeeming if it would violate collateral requirements.

License

This project is licensed under the MIT License.
