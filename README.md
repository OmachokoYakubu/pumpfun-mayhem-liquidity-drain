# pump-fun Bonding Curve Liquidity Drain (Zero-Day PoC)

**Researcher**: Omachoko Yakubu  
**Date**: 29 April 2026  
**Program**: pump-fun  
**Severity**: Critical — Direct Theft of Protocol Liquidity  

---

## Overview

This repository contains a high-fidelity Proof-of-Concept (PoC) for a Critical liquidity drain vulnerability discovered in the pump-fun protocol. 

The vulnerability resides in the "Mayhem Mode" privileged control layer. By manipulating virtual reserves via the Mayhem Authority (`MAyhSmzX...`), an attacker can arbitrarily re-price any bonding curve. This allows for the extraction of 100% of the SOL liquidity from the curve's vault in a single transaction, resulting in total protocol insolvency.

**Target Program**: `6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P` (Solana Mainnet-Beta)

---

## Repository Contents

| File | Description |
|------|-------------|
| `CANTINA_SUBMISSION.md` | Formal technical disclosure covering vulnerability mechanics, impact analysis, and remediation. |
| `run_proof.sh` | Master execution script demonstrating the operational exploit on a forked mainnet. |
| `exploit_harness.so` | SBF program binary used to simulate the authority-gated reserve manipulation. |
| `exploit_output.txt` | Raw, verbose terminal trace of the successful exploit execution. |
| `fuzzer.go` | Mathematical verification tool for testing bonding curve insolvency under extreme reserve states. |
| `global_state.json` | Captured production state for forked-mainnet verification. |

---

## Setup & Reproduction

### Prerequisites
- [Solana CLI](https://docs.solana.com/cli/install-solana-cli-tools) installed.
- [Node.js](https://nodejs.org/) installed (for exploit execution).
- Internet connection (to fork live Mainnet-Beta state).

### Clone and Run
```bash
# 1. Clone this repository
git clone https://github.com/OmachokoYakubu/pumpfun-mayhem-liquidity-drain

# 2. Navigate into the project directory
cd pumpfun-mayhem-liquidity-drain

# 3. Install required node dependencies
npm install @solana/web3.js bn.js

# 4. Run the operational proof
./run_proof.sh
```

---

## PoC Breakdown

The testing sequence performs the following atomic steps on a Mainnet-Beta fork:
1. **Validator Initialization**: Stands up a local validator with the production `pump-fun` program and the `exploit_harness`.
2. **State Verification**: Confirms that the `mayhem_mode_enabled` flag is active (`0x01`) in the production global state.
3. **Reserve Manipulation**: Triggers the `set_mayhem_virtual_params` instruction via the authority harness to inflate virtual SOL reserves to $10^{15}$.
4. **Liquidity Drainage**: Executes a sell transaction that extracts the entire SOL vault balance based on the manipulated price calculation.
5. **Impact Logging**: Captures the final balance of the bonding curve vault (0.00 SOL).

---

## Remediation Strategy

The vulnerability is rooted in the lack of sanity checks on virtual reserve adjustments within the privileged Mayhem Authority path. The proposed remediation involves:
1. **Instruction Deactivation**: Permanently disabling the `set_mayhem_virtual_params` instruction in production.
2. **Reserve Caps**: Implementing strict upper bounds on `virtual_sol_reserves` to ensure they cannot exceed 2x the initial bonding curve parameters.

---

*Omachoko Yakubu, Security Researcher*
