# RING-OSCILLATOR-PHYSICAL-UNCLONABLE-FUNCTION
Implementation of a Ring Oscillator Physical Unclonable Function (RO-PUF) on FPGA using Verilog HDL and Xilinx Vivado.
# Ring Oscillator PUF on FPGA

## 🔐 Ring Oscillator Physical Unclonable Function using Verilog

This project implements a **Ring Oscillator Physical Unclonable Function (RO-PUF)** on an FPGA using **Verilog HDL** and **Xilinx Vivado**.

The main objective is to generate a unique digital response from the inherent manufacturing variations present in FPGA hardware.

---

## 📌 Project Overview

A **Physical Unclonable Function (PUF)** is a hardware security primitive that uses the physical variations introduced during semiconductor manufacturing to generate a unique response.

In this project, a **Ring Oscillator PUF** is implemented using multiple ring oscillators.

Each ring oscillator generates a signal whose frequency depends on small physical variations in the FPGA.

Two ring oscillators are selected and their oscillation frequencies are compared.

The comparison result produces one PUF response bit.

By performing multiple comparisons, an **8-bit PUF response** can be generated.

---

## 🎯 Objectives

- Design a Ring Oscillator PUF using Verilog HDL.
- Generate oscillations using ring oscillator structures.
- Measure the oscillation frequency of selected ring oscillators.
- Compare the frequencies of two ring oscillators.
- Generate a digital PUF response.
- Implement the design using Xilinx Vivado.
- Study FPGA implementation constraints and combinational feedback.
- Analyze the generated PUF response.
- Understand hardware security applications of PUFs.

---

## 🧠 Why PUF?

Traditional digital systems store secret keys in memory.

However, stored keys can potentially be extracted through physical or invasive attacks.

A PUF provides a way to generate a device-specific response from the physical characteristics of the hardware.

The same design implemented on different FPGA devices can produce different responses because of manufacturing variations.

### Basic idea

```text
Manufacturing Variations
          ↓
 Ring Oscillator Frequency
          ↓
 Frequency Comparison
          ↓
     PUF Response
          ↓
 Unique Device Signature
```

---

# 🏗️ System Architecture

The overall architecture consists of:

1. Ring Oscillators
2. Multiplexers
3. Frequency Counters
4. Frequency Comparator
5. Response Register
6. Control FSM

```text
                  ┌──────────────────┐
 Challenge ──────►│   RO Selection   │
                  └────────┬─────────┘
                           │
                 ┌─────────┴─────────┐
                 │                   │
                 ▼                   ▼
          ┌─────────────┐     ┌─────────────┐
          │ Ring        │     │ Ring        │
          │ Oscillator A│     │ Oscillator B│
          └──────┬──────┘     └──────┬──────┘
                 │                   │
                 ▼                   ▼
          ┌─────────────┐     ┌─────────────┐
          │ Counter A   │     │ Counter B   │
          └──────┬──────┘     └──────┬──────┘
                 │                   │
                 └─────────┬─────────┘
                           ▼
                  ┌─────────────────┐
                  │   Comparator    │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ Response        │
                  │ Register        │
                  └────────┬────────┘
                           │
                           ▼
                     8-bit Response
```

---

# ⚙️ Working Principle

### Step 1 – Ring Oscillator Generation

A ring oscillator consists of an odd number of inverter stages connected in a feedback loop.

```text
        ┌─────┐
   ┌───►│ NOT │───►│ NOT │───►│ NOT │───┐
   │    └─────┘    └─────┘    └─────┘   │
   └─────────────────────────────────────┘
```

The feedback causes the signal to continuously toggle between logic 0 and logic 1.

The oscillation frequency depends on the propagation delay of the inverter stages.

---

### Step 2 – Manufacturing Variations

Due to manufacturing variations, two physically similar ring oscillators do not have exactly the same delay.

Therefore:

```text
RO_A frequency ≠ RO_B frequency
```

These small differences are used as the source of uniqueness.

---

### Step 3 – Frequency Measurement

The output of each selected ring oscillator is connected to a counter.

During a fixed measurement interval:

```text
RO_A → Counter A
RO_B → Counter B
```

The counters record the number of oscillations.

A higher count indicates a higher oscillation frequency.

---

### Step 4 – Frequency Comparison

The two counter values are compared.

For example:

```text
If Count_A > Count_B
        → PUF bit = 1

If Count_A < Count_B
        → PUF bit = 0
```

---

### Step 5 – Response Generation

Multiple ring oscillator comparisons are performed using different oscillator pairs.

Each comparison produces one bit.

For example:

```text
Comparison 1 → 1
Comparison 2 → 0
Comparison 3 → 1
Comparison 4 → 1
Comparison 5 → 0
Comparison 6 → 0
Comparison 7 → 1
Comparison 8 → 0

Final PUF Response = 10110010
```

---

# 🔢 Project Configuration

The current design uses:

| Parameter | Value |
|---|---:|
| FPGA | Xilinx FPGA |
| HDL | Verilog |
| Development Tool | Xilinx Vivado |
| Number of Ring Oscillators | 8 |
| Number of Counters | 2 |
| Comparator | 1 |
| Response Width | 8-bit |
| Control | FSM |
| Output | PUF Response |

---

# 🔄 Control FSM

The PUF operation is controlled using a finite state machine.

Typical states include:

```text
             ┌───────┐
             │ IDLE  │
             └───┬───┘
                 │ Start
                 ▼
           ┌────────────┐
           │   SELECT   │
           └─────┬──────┘
                 │
                 ▼
           ┌────────────┐
           │  MEASURE   │
           └─────┬──────┘
                 │
                 ▼
           ┌────────────┐
           │  COMPARE   │
           └─────┬──────┘
                 │
                 ▼
           ┌────────────┐
           │   STORE    │
           └─────┬──────┘
                 │
                 ▼
           ┌────────────┐
           │   FINISH   │
           └────────────┘
```

---

# 📂 Repository Structure

```text
ring-oscillator-puf-fpga/
│
├── README.md
│
├── rtl/
│   ├── ring_oscillator.v
│   ├── ro_puf.v
│   ├── counter.v
│   ├── comparator.v
│   ├── response_register.v
│   └── puf_fsm.v
│
├── simulation/
│   ├── tb_ring_oscillator.v
│   └── tb_ro_puf.v
│
├── constraints/
│   └── puf_constraints.xdc
│
├── docs/
│   ├── architecture.png
│   ├── block_diagram.png
│   └── results.md
│
└── results/
    ├── waveform.png
    ├── utilization.png
    └── timing.png
```

---

# 💻 Tools Used

- **Verilog HDL**
- **Xilinx Vivado**
- **FPGA Development Board**
- **Simulation / RTL Analysis**
- **Xilinx Design Constraints (XDC)**

---

# 🧪 Simulation

The design is first verified through RTL simulation.

The testbench verifies:

- Reset operation
- Start signal
- Ring oscillator selection
- Counter operation
- Frequency comparison
- Response generation
- FSM state transitions
- Done signal

Example expected behavior:

```text
RESET
  ↓
IDLE
  ↓
START
  ↓
SELECT RO PAIR
  ↓
MEASURE FREQUENCY
  ↓
COMPARE
  ↓
STORE RESPONSE BIT
  ↓
NEXT RO PAIR
  ↓
8-BIT RESPONSE
  ↓
DONE
```

---

# 🔧 FPGA Implementation

The design is synthesized and implemented using **Xilinx Vivado**.

The implementation flow is:

```text
Verilog RTL
     ↓
Elaboration
     ↓
Synthesis
     ↓
Implementation
     ↓
Timing Analysis
     ↓
Bitstream Generation
     ↓
FPGA Programming
```

Special attention is required during FPGA implementation because ring oscillators contain **combinational feedback loops**.

Vivado may attempt to optimize or remove these structures during synthesis.

Therefore, appropriate synthesis and implementation constraints may be required to preserve the intended ring oscillator structure.

---

# ⚠️ Combinational Loop Consideration

Ring oscillators intentionally contain a feedback loop.

For example:

```text
INV1 → INV2 → INV3
 ↑             ↓
 └─────────────┘
```

This is different from normal synchronous digital logic.

During synthesis, FPGA tools may identify the feedback path as an unwanted combinational loop and optimize it.

Therefore, implementation constraints and synthesis attributes may be required depending on the FPGA architecture and Vivado version.

---

# 📊 Expected Results

The project aims to obtain:

- Stable oscillation from ring oscillators.
- Different oscillation frequencies between physically different ROs.
- Correct frequency counting.
- Correct comparison results.
- An 8-bit PUF response.
- Successful FPGA implementation.
- Repeatable responses under similar operating conditions.

Example:

```text
RO Pair      Count A      Count B      PUF Bit
------------------------------------------------
RO0 vs RO1     8421         8175          1
RO2 vs RO3     7910         8234          0
RO4 vs RO5     8652         8310          1
RO6 vs RO7     8102         7988          1

Example Response = 1011....
```

The actual response depends on the physical FPGA device and implementation.

---

# 🔐 Applications

RO-PUFs can be used in:

- Hardware authentication
- Device identification
- Cryptographic key generation
- Secure IoT devices
- Anti-counterfeiting
- Secure FPGA systems
- Hardware security
- Chip identification

---

# 🚀 Future Improvements

Possible extensions include:

- Increase the number of ring oscillators.
- Generate larger PUF responses.
- Evaluate uniqueness between different FPGA devices.
- Measure reliability under voltage and temperature variations.
- Generate Challenge-Response Pairs (CRPs).
- Perform statistical analysis of PUF responses.
- Calculate Hamming distance.
- Calculate uniqueness and reliability.
- Implement lightweight key generation.
- Compare RO-PUF with SRAM-PUF and Arbiter-PUF.

---

# 📚 Key Concepts

This project demonstrates practical understanding of:

- Verilog RTL design
- Digital logic design
- Ring oscillators
- FPGA architecture
- Finite State Machines
- Counters
- Multiplexers
- Comparators
- Physical Unclonable Functions
- Hardware security
- Xilinx Vivado
- FPGA synthesis and implementation
- Timing and implementation constraints

---

# 👩‍💻 Author

**Divya Sree**

B.Tech – Electronics and Communication Engineering

JNTUH, Hyderabad

---

# ⭐ Acknowledgement

This project was developed as an academic FPGA/VLSI project to study the implementation of hardware security primitives using FPGA technology.

---

## 📜 License

This project is intended for educational and research purposes.
