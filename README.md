# ➕ 4-Bit Ripple Carry Adder (RCA)

A 4-bit Ripple Carry Adder built from scratch using structural modeling and gate-level primitives. I built this foundational project to deeply understand combinational logic design, the `generate` block instantiation in Verilog, and the self-checking verification flow. My main focus was on achieving 100% functional and code coverage through exhaustive testing.

### 📦 Technologies

* **RTL Design:** Verilog
* **Architecture:** Structural Modeling, Gate-Level Primitives
* **Verification:** QuestaSim, ModelSim
* **Environment & OS:** Linux (Ubuntu), VIM
* **Methodology:** Exhaustive Testing, Golden Model Self-Checking, Coverage-Driven Verification, Makefiles

### ⚙️ IP Features

Here's what this Adder block can do:
* **Structural Architecture:** Instantiates 4 individual 1-bit Full Adder sub-modules to create the continuous ripple chain.
* **Gate-Level Implementation:** The core `full_adder` is built using optimized boolean logic equations with bitwise operators (`^`, `&`, `|`) instead of behavioral `+` operators, simulating real hardware synthesis.
* **Scalability:** Uses `generate` `for` loops, making the architecture highly scalable to N-bit configurations.
* **Full Carry Support:** Fully supports Carry-in (`cin`) and Carry-out (`cout`) for cascading into larger ALUs.

### 📐 Hardware Specifications (Spec)

The 4-bit Ripple Carry Adder is designed with the following port interfaces and functional constraints:

**1. Input/Output Ports:**

| Port Name | Direction | Width | Description |
| :--- | :--- | :--- | :--- |
| `a` | Input | 4-bit | First operand |
| `b` | Input | 4-bit | Second operand |
| `cin` | Input | 1-bit | Carry-in from a previous lower-order stage |
| `sum` | Output | 4-bit | Result of the addition |
| `cout` | Output | 1-bit | Carry-out to a next higher-order stage |

**2. Functional Description:**
* Computes the arithmetic sum of two 4-bit inputs and a 1-bit carry-in: `Result = a + b + cin`.
* The maximum possible theoretical value is `15 + 15 + 1 = 31`.
* Output mapping: The LSBs (bits 3:0) are assigned to the `sum` port, while the MSB (bit 4, representing the value 16) is assigned to the `cout` port.

**3. Architecture Block Diagram:**
<p align="center">
  <img src="./images/4-bit-Ripple-Carry-Adder.drawio.png" alt="Block Diagram">
</p>

**4. Module Descriptions:**
To implement the structural architecture shown above, the design is hierarchically partitioned into two main levels:
* **Sub-Module: 1-Bit Full Adder (`full_adder`)**
    * **Role:** The fundamental building block of the design. It computes the arithmetic addition of two single data bits (`a`, `b`) and a carry-in bit (`cin`).
    * **Implementation:** Built entirely using continuous assignments with bitwise logic operators to represent standard Gate-Level primitives:
        * $Sum = a \oplus b \oplus cin$
        * $C_{out} = (a \cdot b) + (cin \cdot (a \oplus b))$
* **Top Module: 4-Bit RCA Top (`rca_4bit`)**
    * **Role:** The top-level wrapper that constructs the 4-bit parallel adder.
    * **Implementation:** Utilizes **Structural Modeling**. It instantiates four discrete `full_adder` blocks and routes the internal wire connections. The critical path is formed by chaining the carry bits: the `cout` of stage $i$ is directly hardwired to the `cin` of stage $i+1$. This demonstrates the characteristic "ripple" effect, where the Most Significant Bit (MSB) must wait for the carry signal to propagate through all lower-order stages.

### 🦉 The Process

I started by analyzing the gate-level specification and drawing the hardware architecture to trace the `cout` to `cin` ripple path. Then, I wrote the RTL code in Verilog, ensuring the boolean logic was purely combinational and clean.

Next, I focused heavily on the verification phase. Instead of relying on manual "eyeball verification," I built a **Self-Checking Testbench**. I designed a Golden Model using Verilog's built-in math operator (`a + b + cin`) to calculate the expected 5-bit result.

Since the input space was relatively small (4 bits for A + 4 bits for B + 1 bit for Cin = 9 bits total), I implemented an **Exhaustive Testing** strategy. I wrote nested `for` loops to automatically inject all 512 possible input combinations into the DUT, complete with appropriate propagation delays (`#10`) to mimic physical gate latency.

Finally, I automated the simulation flow using a Linux `Makefile`. The automated checker ran through all 512 cases flawlessly, hitting the golden metric: 100% Code Coverage across the board with zero mismatches against the theoretical model.

### 📚 What I Learned

During this project, I significantly improved my hardware thinking and debugging skills:
* **Simulation Mechanics:** I deeply understood the concept of propagation delay in combinational logic and how to use delay statements in a testbench to avoid race conditions.
* **Exhaustive Verification:** I learned the power of Golden Models and automated self-checking mechanisms over manual waveform inspection.
* **Structural Instantiation:** Mastered the use of `generate` blocks and internal wire arrays to dynamically instantiate and connect sub-modules without hardcoding.
* **Linux & VIM Power:** Writing, debugging code, and running simulations entirely in a Linux terminal using VIM and Makefiles drastically improved my productivity.

### 📋 Verification Plan (VPLAN) Summary

Instead of uploading a massive Excel sheet, here is a high-level extraction of my VPLAN covering the core scenarios I tested to achieve 100% coverage:

| Item | Sub Item | Method | Pass Condition | Result |
| :--- | :--- | :--- | :--- | :--- |
| **Basic Addition** | `test_all_zeros` | Direct Stimulus | `{cout, sum} == 5'b00000` | ✅ PASS |
| **Max Values** | `test_all_ones` | Direct Stimulus | `{cout, sum} == 5'b11111` | ✅ PASS |
| **Carry Logic** | `test_carry_prop` | Direct Stimulus | Carry bit ripples correctly across all stages | ✅ PASS |
| **Full Space** | `test_exhaustive` | Nested Loops | DUT output strictly matches `(a + b + cin)` across all 512 cases | ✅ PASS |

### 📊 Verification Results & Artifacts

1. **Simulation Regression Log** - Snippet showing the testbench sweeping through 512 cases perfectly.
<p align="center">
  <img src="./images/WAVEFORM.png" alt="Terminal Log">
</p>

2. **Regression Test Report** - Automated script successfully verified all core scenarios without a single mismatch.
<p align="center">
  <img src="./images/PASS.png" alt="Regression Report">
</p>

3. **Code Coverage Report (100%)** - Achieved 100% in Statement, Expression, and Toggle coverage (38/38 bins).
<p align="center">
  <img src="./images/COVERAGE.png" alt="Coverage Report">
</p>

### 💭 How can it be improved?

* Upgrade the architecture from a Ripple Carry Adder (RCA) to a Carry Look-Ahead Adder (CLA) to significantly reduce propagation delay.
* Fully parameterize the module to dynamically generate an N-bit adder using macros.
