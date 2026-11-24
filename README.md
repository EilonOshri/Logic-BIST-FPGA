# STUMPS Logic BIST (LBIST) on FPGA

An end-to-end **Logic Built-In Self-Test (LBIST)** hardware system based on the **STUMPS** architecture, designed in RTL and targeted for **Intel FPGA** implementation. The system autonomously tests digital logic (a pipelined ALU) without external Automated Test Equipment (ATE) and demonstrates real-time hardware fault injection and detection.

---

## Architecture Overview

![STUMPS LBIST Architecture](docs/Block%20diagram.drawio.png)

### Core Subsystems
* **PRPG (16-bit LFSR):** Generates deterministic pseudo-random test patterns using a maximal-length primitive polynomial.
* **Phase Shifter (XOR Network):** Eliminates linear correlation (structural dependency) between adjacent scan chains before shifting data into the DUT.
* **Design Under Test (DUT):** A functional ALU wrapped with **Muxed-D Scan Flip-Flops (SFF)** organized into **4 parallel scan chains × 8 SFF** (32 flip-flops total).
* **MISR (16-bit Compactor):** Compacts parallel `scan_out[3:0]` responses across all test patterns into a single 16-bit digital signature.
* **LBIST Controller (FSM):** Single-process synchronous state machine operating under a unified system clock domain (zero gated clocks) to manage `INIT`, `SHIFT`, `CAPTURE`, and `COMPARE` cycles.
* **Comparator & FPGA I/O:** Compares the final MISR signature against a pre-simulated **Golden Signature**, driving `PASS/FAIL` LEDs and displaying the signature on the board's 7-segment display. Physical switches allow live injection of `Stuck-at-0` and `Stuck-at-1` faults into internal DUT nodes.

---

## Repository Structure

```text
├── RTL/                 # Synthesizable HDL design modules (ALU, Scan DFF, PRPG, Phase Shifter, MISR, Controller)
├── TB/                  # Self-checking simulation testbenches & fault-injection verification
├── FPGA/                # Quartus Prime project files, pin assignments, and SDC timing constraints
└── docs/                # Block diagrams, waveforms, and hardware verification photos
