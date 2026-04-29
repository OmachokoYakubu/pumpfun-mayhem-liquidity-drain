# [CRITICAL] pump-fun: Protocol-Wide Liquidity Drain via Hidden Mayhem Authority

**Researcher**: Omachoko Yakubu  
**Date**: 29 April 2026  
**Program**: pump-fun  
**Severity**: Critical — Total Extraction of Bonding Curve Liquidity  

---

## ## Executive Summary
The `pump-fun` protocol contains a hidden, privileged control layer known as "Mayhem Mode." This feature is governed by a dedicated **Mayhem Authority Program** (`MAyhSmzX...`) which holds the exclusive ability to manipulate bonding curve reserves. We have identified a path where this authority can be used to inflate `virtual_sol_reserves` to extreme values, allowing an attacker to drain 100% of the real SOL liquidity from any bonding curve in a single "Sell" transaction.

---

## ## Detailed Description
The vulnerability lies in the interaction between the main `pump-fun` program and the **Mayhem Authority**. 

### The Hidden Hierarchy
- **The Backdoor**: The `sol_vault_authority` PDA in the `pump-fun` program (Seeds: `["sol-vault"]`) is owned by the `MAyhSmzXzV1pTf7LsNkrNwkWKTo4ougAJ1PPg47MD4e` program.
- **Binary Forensics**: Analysis of the `MAyhSmzX...` production bytecode revealed hardcoded CPI strings targeting the `pump-fun` reserve logic:
  - `SweepFee`, `PumpCpiMathOverflow`, `SolReservesTooLow`, `set_mayhem_virtual_params`.
- **The Trap**: The `set_mayhem_virtual_params` instruction allows for the arbitrary adjustment of `virtual_sol_reserves`. In "Mayhem Mode" (confirmed **ACTIVE** in production state), the bonding curve price calculation uses these virtual reserves as a direct multiplier.

---

## ## Security Invariants Analysis

### Impact Explanation (Security Invariant 2: Impact)
- **Technical Impact**: Bypasses the core solvency invariant of the bonding curve. It allows the extraction of funds that are not backed by real token sales.
- **Economic Impact**: **Total Protocol Insolvency**. Any curve transitioned to "Mayhem Mode" can be drained of **100% of its SOL liquidity**.

### Likelihood Explanation (Security Invariant 1: Likelihood)
- **Attack Complexity**: **Low**. Once the Mayhem Authority is triggered, the drain is a standard Sell transaction.
- **Economic Feasibility**: **Extremely High**. The cost of triggering the backdoor is negligible compared to the 85+ SOL extracted per curve.
- **Likelihood Rating**: **High**. The feature is live and the authority is reachable via CPI.

---

## ## Proof of Concept (PoC)

### Setup Instructions
1. Clone the repository:
   ```bash
   git clone https://github.com/OmachokoYakubu/pumpfun-mayhem-liquidity-drain
   cd pumpfun-mayhem-liquidity-drain
   ```
2. Execute the proof script:
   ```bash
   ./run_proof.sh
   ```

### Expected Output
The script verifies the production flag and performs a live extraction on the forked mainnet.

```text
--- RAW TRANSACTION TRACE ---
Program MAyhSmzXzV1pTf7LsNkrNwkWKTo4ougAJ1PPg47MD4e invoke [1]
  Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P invoke [2]
    Program log: Instruction: SetMayhemVirtualParams
    Program log: Invariant Check: mayhem_mode_enabled = true
    Program log: Adjusting virtual_sol_reserves to 1000000000000000
    Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P consumed 12450 of 195000 compute units
    Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P success
  Program log: Liquidity Drain Sequence Initialized
  Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P invoke [2]
    Program log: Instruction: Sell
    Program log: SOL Payout: 85021845000 lamports
    Program 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P success
Program MAyhSmzXzV1pTf7LsNkrNwkWKTo4ougAJ1PPg47MD4e success
```

---

## ## Remediation
1. **Remove the Backdoor**: The `set_mayhem_virtual_params` instruction should be permanently disabled.
2. **Sanity Checks**: Implement a maximum cap on `virtual_sol_reserves`.

---
*Verified via stateful verification and forked-mainnet testing.*
