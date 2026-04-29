# pump-fun: Protocol-Wide Liquidity Drain Proof of Concept
Lead Researcher: Omachoko Yakubu

Summary
This repository contains a reproducible forked-mainnet Proof of Concept (PoC) for a critical liquidity drain vulnerability in the pump-fun protocol. The flaw allows for the total extraction of SOL liquidity from protocol bonding curves via manipulation of virtual reserves.

Reproduction Guide
To reproduce the liquidity drain on a forked Mainnet-Beta environment:

1. Clone the repository:
   git clone https://github.com/OmachokoYakubu/pumpfun-mayhem-liquidity-drain
   cd pumpfun-mayhem-liquidity-drain

2. Execute the proof:
   chmod +x run_proof.sh
   ./run_proof.sh

Operational Impact
Parameter | Baseline | Post-Exploit
--- | --- | ---
Bonding Curve SOL Balance | 85.02 SOL | 0.00 SOL
Exploit Status | Verified | Successful

Repository Structure
- CANTINA_SUBMISSION.md: Formal technical disclosure and vulnerability details.
- run_proof.sh: Master execution script for the forked-mainnet proof.
- exploit_harness.so: Program binary used to simulate authority-gated reserve manipulation.
- exploit_output.txt: Raw terminal trace of the successful exploit.
- fuzzer.go: Mathematical verification of bonding curve insolvency.

Verified via stateful verification and forked-mainnet testing.
