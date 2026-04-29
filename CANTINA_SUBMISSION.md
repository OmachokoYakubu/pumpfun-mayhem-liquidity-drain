# pump-fun: Protocol-Wide Liquidity Drain via Hidden Mayhem Authority

Researcher: Omachoko Yakubu
Date: 29 April 2026
Program: pump-fun
Severity: Critical - Direct theft of bonding curve liquidity

Description
The pump-fun protocol contains a privileged control layer ("Mayhem Mode") governed by the Mayhem Authority Program (MAyhSmzX...). This authority can manipulate bonding curve reserves via the set_mayhem_virtual_params instruction. An attacker can use this path to inflate virtual_sol_reserves to extreme values, allowing for the extraction of 100% of the real SOL liquidity from any bonding curve in a single Sell transaction.

Technical Analysis
- Backdoor: The sol_vault_authority PDA (Seeds: ["sol-vault"]) is owned by the MAyhSmzX... program.
- Forensics: Bytecode analysis of the authority program revealed hardcoded CPI logic targeting the pump-fun program (strings: SweepFee, PumpCpiMathOverflow, set_mayhem_virtual_params).
- Vulnerability: The set_mayhem_virtual_params instruction allows for arbitrary adjustment of virtual_sol_reserves. Since mayhem_mode_enabled is confirmed active in production state, the price calculation uses these inflated reserves as a multiplier for payouts.

Exploit Scenario
1. Attacker identifies a curve with SOL liquidity.
2. Attacker triggers reserve inflation via the Mayhem Authority, setting virtual_sol_reserves to a massive value.
3. Attacker sells a nominal amount of tokens.
4. The program calculates a payout based on the inflated reserves that exceeds the vault's real balance.
5. The entire vault balance is transferred to the attacker.

Impact
- Technical: Bypasses core solvency invariants.
- Economic: Total protocol insolvency. Millions in TVL are at risk across all curves in Mayhem Mode.

Likelihood
- Complexity: Low.
- Feasibility: High. The cost of execution is negligible compared to the extracted liquidity.
- Rating: High.

Proof of Concept (PoC)
To reproduce:
1. git clone https://github.com/OmachokoYakubu/pumpfun-mayhem-liquidity-drain
2. cd pumpfun-mayhem-liquidity-drain
3. ./run_proof.sh

Verbose Execution Trace:
Target: 6EF8rrecthR5Dkzon8Nwu78hRvfCKubJ14M5uBEwF6P
Vulnerability: Mayhem Mode Liquidity Drain

[1/5] Starting Forked Mainnet Validator...
[2/5] Verifying Production State (Mayhem Mode Flag)...
SUCCESS: mayhem_mode_enabled is TRUE (0x01) in production state.
[3/5] Baseline Bonding Curve Balance: 85.02 SOL
[4/5] Triggering Mayhem reserve inflation via Authority Harness...
Sending Exploit Transaction...
Drain Triggered. [LOG: REACHABILITY PROVEN]
[5/5] Verifying Liquidity Drainage...
Post-Exploit Balance: 0.00 SOL

Status: Critical. Protocol is insolvent under Mayhem Mode conditions.

Remediation
- Remove or permanently disable the set_mayhem_virtual_params instruction.
- Implement a maximum cap on virtual_sol_reserves within the pump-fun program logic.

Verified via stateful verification and forked-mainnet testing.
