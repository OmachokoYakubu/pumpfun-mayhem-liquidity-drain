# [CRITICAL] pump-fun: Protocol-Wide Liquidity Drain Proof of Concept
**Lead Security Researcher**: Omachoko Yakubu

---

## 🔍 Executive Summary
This repository contains a **reproducible, forked-mainnet Proof of Concept (PoC)** for a critical liquidity drain vulnerability in the `pump-fun` protocol. The vulnerability allows for the total extraction of SOL liquidity from protocol bonding curves via the manipulation of "Mayhem Mode" virtual reserves.

## 🚀 Quick Start (Reproduction Guide)
To reproduce the 100% liquidity drain on a forked Mainnet-Beta environment, follow these steps:

```bash
# 1. Clone the repository
git clone https://github.com/OmachokoYakubu/pumpfun-mayhem-liquidity-drain
cd pumpfun-mayhem-liquidity-drain

# 2. Execute the irrefutable proof
# This script starts a forked validator, verifies state, and drains the vault.
./run_proof.sh
```

## 🧪 Operational Impact (Forked Mainnet Result)
| Parameter | Baseline | Post-Exploit |
| :--- | :--- | :--- |
| **Bonding Curve SOL Balance** | **85.02 SOL** | **0.00 SOL** |
| **Exploit Status** | Verified | **SUCCESSFUL** |

## 📁 Repository Structure
- `CANTINA_SUBMISSION.md`: The formal technical disclosure.
- `run_proof.sh`: Master execution script (Forked Mainnet PoC).
- `exploit_harness.so`: The authority-impersonation binary.
- `global_state.json`: Production Mainnet-Beta account data.
- `pump_program.so`: Production Mainnet-Beta protocol binary.

---
*Verified via Hans Pipeline v3.1 methodology.*
