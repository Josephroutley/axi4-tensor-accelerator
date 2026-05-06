# AXI4 Tensor Compute Accelerator

![Language](https://img.shields.io/badge/Language-SystemVerilog-blue)
![Bus Protocol](https://img.shields.io/badge/Bus-AXI4%20%7C%20AXI--Lite-orange)
![Status](https://img.shields.io/badge/Status-Planned%20(Pre--Development)-lightgrey)

## Overview
This repository documents a 5-week project to design a custom hardware accelerator capable of massive, parallel math calculations. 

Modern AI models require executing millions of math problems simultaneously, which overwhelms standard computer processors (CPUs). This project solves that bottleneck by building a dedicated, standalone math engine—modeled after modern AI chips like TPUs. The hardware module operates autonomously to fetch data arrays from system memory, execute parallel multiply-accumulate (MAC) operations via a spatial dataflow grid, and write results back to RAM using industry-standard AMBA interconnects.

## Project Roadmap (5-Week Sprint)
- [ ] **Week 1: Tensor Compute Grid**
  - *Goal:* Design a grid of calculation units that pass data efficiently between each other to solve complex equations quickly.
  - *Plan:* Parameterize and implement an N x N array of pipelined Multiply-Accumulate (MAC) units. Verify mathematical correctness of the rhythmic, spatial data flow.
- [ ] **Week 2: AXI-Lite Control Interface**
  - *Goal:* Build the control interface that allows the main computer to securely send "Start," "Stop," and "Check Status" commands to the accelerator.
  - *Plan:* Build a fully compliant AXI-Lite slave interface to expose a memory-mapped register space for host CPU configuration (matrix dimensions, base memory addresses).
- [ ] **Week 3: Clock Domain Crossing**
  - *Goal:* Create specialized data safety nets that allow the high-speed math engine to talk to the slower main computer memory without losing information.
  - *Plan:* Implement and stress-test custom Asynchronous FIFOs with Gray code pointers to safely manage clock boundaries between the high-speed compute core and standard system memory.
- [ ] **Week 4: AXI4 Master DMA Engine**
  - *Goal:* Design an independent manager that can automatically request large chunks of data from the main computer and store the answers when finished.
  - *Plan:* Design custom DMA state machines for autonomous AXI4 burst reads/writes to system RAM, strictly handling 4K boundary constraints, outstanding transactions, and `VALID`/`READY` backpressure.
- [ ] **Week 5: Automated Co-Simulation**
  - *Goal:* Build a testing environment that generates thousands of random math problems, feeds them into the hardware, and automatically verifies the answers are perfect.
  - *Plan:* Develop `cocotb` testbenches utilizing AXI Verification Models (`cocotbext-axi`) and `numpy` to automatically generate, drive, and verify randomized matrix operations against a software reference model.

## Planned Architecture
The datapath is designed to maximize computational density while integrating smoothly into standard SoC environments:
*   **Tensor Compute Grid (The Engine):** A rhythmic, step-by-step spatial dataflow architecture utilizing a 2D grid of MAC units to keep data moving constantly and minimize pipeline stalling.
*   **Clock Domain Crossing (The Buffer):** Robust Asynchronous FIFOs for safe, high-bandwidth data handoffs.
*   **AXI-Lite Slave Interface (The Port):** Control interface for starting/stopping execution and polling accelerator status.
*   **AXI4 Master Interface (The DMA):** Custom Direct Memory Access engine handling complex burst transactions with standard system memory.

## Planned Verification Methodology
Verification will be handled via a high-performance Python co-simulation environment utilizing `Verilator` and `cocotbext-axi`. Automated testing will ensure strict compliance with the AMBA 4 specification while verifying computational accuracy cycle-by-cycle against `numpy` generated reference models to ensure zero mathematical drift.

## Directory Structure
*   `hw/rtl/`: SystemVerilog source files (Compute core, AMBA wrappers, Async FIFOs).
*   `sim/`: `cocotb` testbenches, Python (`numpy`) reference models, and AXI verification configurations.
*   `docs/`: Architecture block diagrams, timing constraints, and memory map documentation.
