

# Combinational and Sequential Optimations

 Combinational and sequential optimizations are fundamental techniques in digital circuit design, particularly for improving the performance, power consumption, and area of integrated circuits. Combinational optimization focuses on reducing the complexity of logic functions (like using Karnaugh maps or Quine-McCluskey) to minimize the number of gates and interconnections, which directly impacts the circuit's speed and size. Sequential optimization, on the other hand, deals with the state-holding elements (like flip-flops or latches) and their connectivity, aiming to reduce the number of states, simplify the state transitions, or optimize the clocking scheme to improve overall circuit efficiency and speed while maintaining the correct functionality.

## Content
[1.Combinational Logic Optimisation](#combinational-logic-optimisation)  
[2.Sequential Logic Optimisation](#sequential-logic-optimisation)


### Combinational Logic Optimisation
This overall topic is about finding the most efficient way to implement a combinational logic function.

#### Squeezing the logic to get the most optimised design


Area savings: Using fewer logic gates and interconnections means the circuit takes up less space on the integrated circuit.

Power savings: Fewer gates and reduced switching activity lead to lower power consumption.

#### Constant Propagation

Direct Optimisation: Constant Propagation involves simplifying a circuit by substituting known constant values (like '0' or '1') into the logic gates. For example, an AND gate with one input tied to '0' can be completely eliminated and replaced by a constant '0', as its output will always be '0' regardless of the other input.

#### Boolean Logic Optimisation


K-Map (Karnaugh Map): A graphical method used for simplifying Boolean algebra expressions. It is effective for functions with a small number of variables (typically up to 4 or 5).

Quine-McCluskey: A tabular, algorithmic method for simplifying Boolean expressions. It is more complex than K-Maps but is suitable for functions with a large number of variables and is the basis for many modern Electronic Design Automation (EDA) tools

#### Synthesis of Combinational logic Circuit

##### Code
   
      gvim opt_check* -o
   
![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_12_56_13.png?raw=true)

##### Synthesis

   opt_clean: This is a Yosys pass that removes unused cells and wires from the design. It's used after other optimization passes to tidy up.

-purge: This is an option for opt_clean that instructs the tool to also remove internal nets (wires) even if they have a public name.

    opt_clean -purge

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_12_57_07.png?raw=true)

 Ternary operator expression **(a?b:0)** can be optimized into a single logic operation.

 Optimized logic : **(a.b)**

 ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_12_50_28.png?raw=true)

 Ternary operator expression **(a?1:b)** can be optimized into a single logic operation.

 Optimized logic : **(a+b)**

 ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_12_58_27.png?raw=true)

 Ternary operator expression **(a?(c?b):0)** can be optimized into a single logic operation.

 Optimized logic : **(a.b.)**
 
 ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_13_00_18.png?raw=true)

 Ternary operator expression **(a?(b?(a&c):c):!c)** can be optimized into a single logic operation.

 Optimized logic : **~(a^b)**

##### Code
      gvim multiple_module_opt* -O
   
 ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_25_09_2025_08_29_15.png?raw=true)

###### Synthesis

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_13_57_29.png?raw=true)

 |Instance|Inputs|logic|Output|Result|
 |:-------|:-----|:----|:-----|:-----|
 |u1|a , 1'b1|y=a & 1|n1|n1=a|
 |u2|b , 1'b0|y=b & 1|n2|n2=0|
 |u3|b , d|y=b & d|n3|n3=b.d|

 The entire multiple_module_opt is equivalent to: **y=(a&b)|c**

 ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_13_45_53.png?raw=true)

  |Instance|Inputs|logic|Output|Result|
 |:-------|:-----|:----|:-----|:-----|
 |u1|a , 1'b0|y=a&0|n1|n1=0|
 |u2|b , c|y=b&C|n2|n2=b&c|
 |u3|n2 , d|y=n2&d|n3|n3=b&c&d|
 |U4|n3 , n1|y=n3&n1|y|y=0|

  The entire multiple_module_opt is equivalent to: **y=0**

  ### Sequential Logic Optimisation
  Sequential logic optimization aims to reduce circuit area, power consumption, and improve operational speed (timing/clock frequency) without changing the circuit's overall function.

#### Basic Optimisations
##### Sequential Constant Propagation

This is an extension of standard combinational constant propagation.

What it does: It identifies and simplifies parts of the circuit where a sequential element (like a flip-flop) is fed by, or feeds into, a constant logic value (e.g., permanently '0' or '1').

Example: If a flip-flop's data input (D) is permanently wired to '0', the optimizer can remove the flip-flop and replace its output with a constant '0', simplifying all downstream logic.

Result: Reduces the cell count and potentially eliminates wires, leading to a smaller, faster circuit.

#### Advanced Optimisations
These techniques involve more complex analysis and modification of the circuit's timing or state structure.

**State Optimisation**

What it does: This applies to circuits defined by a Finite State Machine (FSM). It analyzes the states and transitions to reduce the total number of states required, or to find a more efficient state encoding (assigning binary codes to states).

Example: Two different states that produce the same outputs and transition to the same next states can be merged into a single state, reducing the number of flip-flops needed.

Result: Significantly reduces the area (flip-flop count) and complexity of the next-state logic.

**Retiming**

What it does: Retiming is a technique that moves flip-flops (registers) across combinational logic blocks while preserving the overall function of the circuit. The goal is to balance the delays in the circuit paths.

Example: If a long path between two flip-flops is slowing down the clock, retiming can move one or more flip-flops into the middle of that path, breaking the long delay into shorter, more balanced segments.

Result: Improves the circuit's maximum operating frequency (clock speed).

**Sequential Logic Cloning**

What it does: This optimization involves duplicating (cloning) flip-flops or small groups of sequential logic.

The term "Floor Plan Aware" means the optimization considers the physical layout (the distance between cells) on the chip.

Example: If a single flip-flop's output drives multiple distant parts of the chip, the long wires required for that single output can introduce significant delay (known as net congestion or high fanout). Cloning the flip-flop and placing the copies closer to the load groups reduces the critical wire lengths.

Result: Reduces interconnect delay and wire congestion, improving overall timing and routability.

#### Synthesis of Sequential logic Circuit

##### Code
 
    gvim dff_const* -O

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_14_32_52.png?raw=true)

##### Synthesis

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_14_34_49.png?raw=true)

##### Logic Analysis: Synthesized DFF (`dff_const1`)

This section explains the final logic implemented by the synthesized D-Flip-Flop, which has its data input permanently tied to '1' and uses an active-low reset derived from the active-high `reset` input.



##### 1. Normal Operation (Clocked Set)

This occurs when the external input `reset` is **LOW (0)**, meaning the flip-flop's internal active-low reset pin ~RESET is **HIGH (inactive)**.

| Logic | Description |
| :--- | :--- |
| **Reset State** | reset= 1|
| **Data Input (D)** | D = 1'b1 |
| **Action** | On the positive clock edge, the output Q is set to D. |
| **Result** | Q_next= 1 |

---

#####  2. Reset Operation (Asynchronous Clear)


This occurs when the external input `reset` is **HIGH (1)**, causing the inverter to drive the flip-flop's internal active-low reset pin ~RESET **LOW (active)**.

| Logic | Description |
| :--- | :--- |
| **Reset State** | reset = 0 |
| **Action** | The output Q is asynchronously forced to the reset state. |
| **Result** | Q_next = 0|

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_14_35_48.png?raw=true)

##### Final Logic: Constant Output Optimization

This section explains the logical behavior of the optimized netlist for the `dff_const2` module, where the synthesis tool completely eliminated the sequential element.

##### 1. Source Logic Review
The original Verilog described a flip-flop where:
* **On Reset:** The output was forced to '1'.
* **On Clock (Normal Operation):** The output was permanently set to '0' (`q <= 1'b0;`).

##### 2. Synthesis Optimization
The netlist you provided represents a subsequent optimization pass where the tool only preserved the **Logic High (1'b1)** connection, likely due to optimization rules or subsequent logic that depended on the reset state being '1'.

The tool performed **Constant Propagation** and **Sequential Logic Elimination**:
* The flip-flop structure, clock (`clk`), and reset logic were all deemed redundant.
* The entire circuit was replaced by a **wire permanently tied to a constant value**.

##### 3. Final Logical Behavior

| Feature | Logic |
| :--- | :--- |
| **Inputs** | clk, reset are **ignored**  |
| **Output State** | The output Q is always **Logic High**. |
| **Boolean Equation** | Q = 1 |

The netlist is a hardware implementation of a **constant '1' generator**, often used to provide power (VCC) to a signal line.

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_14_37_14.png?raw=true)

##### Final Logic: Double-Stage Constant Setter (`dff_const3`)

The original Verilog module implemented a sequential element where the final logic was highly optimized by the synthesis tool.

##### 1. Circuit Structure
The netlist consists of **two cascaded D-Flip-Flops (DFFs)** driven by the clock (`clk`) and controlled by the `reset` signal:

| Stage | Cell Type | Control Feature |
| :--- | :--- | :--- |
| **First Stage** | DFF (Reset-High) | Data Input (**D**) is tied to **`1'b1`** (Logic High). |
| **Second Stage** | DFF (Set-Low) | Data Input (**D**) is tied to the output of the first stage (**Q1**). |

##### 2. Logic Optimization and Simplification
The synthesis tool performed **Sequential Constant Propagation** across both stages:

* **First Stage Logic:** Since its data input is permanently '1', its output Q1 will always be '1' when the reset is inactive.
* **Second Stage Logic:** Since its data input Q1 is always '1' (when not in reset), the final output Q will also always be '1' when the set/reset signals are inactive.

##### 3. Final Logical Behavior

The entire circuit is optimized into a **Constant '1' Generator**. The reset signal only acts as a temporary override.

| Feature | Logic |
| :--- | :--- |
| **Inputs** | The clock (`clk`) only triggers the setting, but the value is always **1**. |
| **Normal State** | The final output Q is permanently **Logic High (1)**. |
| **Boolean Equation** | Q = 1(During normal, non-reset operation) |

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_14_38_14.png?raw=true)

##### Final Logic: Initialized Hold Register (`dff_const4`)

The `dff_const4` module implements a logic element whose state is controlled **only by the asynchronous reset signal**, effectively eliminating the clock logic from its function.

##### 1. Circuit Structure
The Verilog code (likely `if(reset) q1 <= 1'b1; else q1 <= q1;`) instructs the DFF to always hold its current value when the reset is inactive.

##### 2. Logic Optimization
The synthesis tool performs **Logic Elimination** and **Sequential Constant Propagation**:

* **Redundant Clock:** Since the next state Q1_next is always the current state Q1, the clock edge is irrelevant to changing the value (it only *allows* the value to be held).
* **Hold Register:** The tool implements a basic memory element whose only control input is the reset signal.

##### 3. Final Logical Behavior

The element acts as a **Memory Cell** initialized by the reset signal. The inputs clk and input are completely ignored in the steady-state logic.

| Feature | Logic |
| :--- | :--- |
| **Normal State** | The output q1 holds the last value set by the reset or initialization. |
| **Reset Action** | The asynchronous reset forces the value to **Logic High (1)**. |
| **Boolean Equation** | The state Q1 is 1 after reset and remains 1 until the next reset. |

The most common implementation is a basic register whose value is fixed at VCC (`1'b1`) by the reset, and never changes afterward.

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_14_39_39.png?raw=true)
##### Final Logic: Initialized Hold Register (`dff_const5`)

The `dff_const5` module implements a basic memory element whose state is controlled **only by the asynchronous reset signal**, making the clock logic redundant for value changes.

##### 1. Circuit Structure
The Verilog code (likely `if(reset) q <= 1'b0; else q <= q;`) instructs the DFF to always hold its current value when the reset is inactive.

##### 2. Logic Optimization
The synthesis tool performs **Logic Elimination** and **Sequential Constant Propagation**:

* **Redundant Clock:** Since the next state ($\mathbf{q}_{\text{next}}$) is always the current state ($\mathbf{q}$), the clock edge is irrelevant to changing the value (it only *allows* the value to be held).
* **Hold Register:** The tool implements a basic memory element whose only control input is the asynchronous reset signal.

##### 3. Final Logical Behavior

The element acts as a **Memory Cell** initialized by the reset signal. The inputs $\text{clk}$ and $\text{input}$ are completely ignored in the steady-state logic.

| Feature | Logic |
| :--- | :--- |
| **Normal State** | The output $\mathbf{q}$ holds the last value (usually '0' if initialized by reset). |
| **Reset Action** | The asynchronous reset forces the value to **Logic Low (0)**. |
| **Boolean Equation** | The state $\mathbf{q}$ is $\mathbf{0}$ after reset and holds its value until the next reset. |

The final implementation is an **initialized constant register** whose value is fixed by the asynchronous reset, as its input is never allowed to change.

#### Counter

##### Code
gvim counter_opt* -O
![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_16_48_22.png?raw=true)

##### Synthesis
![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_16_50_25.png?raw=true)

##### Final Logic: Divide-by-2 Counter Optimization

This netlist shows the synthesized and optimized circuit for a simple **Toggle Flip-Flop** or **Divide-by-2 Counter** (`counter_opt`).

##### 1. Circuit Structure
The core of the circuit is a single **D-Flip-Flop (DFF)** with logic configured for state toggling.

| Component | Function | Detail |
| :--- | :--- | :--- |
| **Sequential Element** | D-Flip-Flop (`dfrbp`) | Stores the current counter state (Q). |
| **Control Logic** | Inverter (`clkinv`) | Used to convert the active-high reset into the active-low ~RESET_B required by the DFF. |

---

##### 2. Logic Implementation

The circuit implements a **toggle function** through feedback:

* **Feedback/Toggle Logic:** The Data Input (**D**) of the DFF is driven by the **inverted output ~Q of the flip-flop**.
    * This connection implements the required toggle logic: D = Q.
* **Final Output:** The signal Q is generated by inverting the flip-flop's primary output Q.
    * This implements the logic: D = Q.

##### 3. Functional Behavior

The circuit acts as a **Toggle Flip-Flop**:

* **Operation:** On every positive clock edge, the DFF loads the opposite of its current state, causing its output (Q) to **toggle** (flip state).
* **Result:** The output signal **q** toggles at exactly **half the frequency** of the input clock clk.

##### 4.Boolean Equation
The core sequential logic is:

Q_next= Q

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-3/Images/VirtualBox_Ubuntu_24_09_2025_16_56_04.png?raw=true)

##### Final Logic: Multi-Bit Synchronous Counter

This netlist shows the synthesized and optimized implementation of a **Multi-Bit Synchronous Counter** (likely 2-bit) using standard logic cells.

##### 1. Circuit Structure
The circuit uses at least **two cascaded D-Flip-Flops (DFFs)** to store the counter's state (Q0, Q1, etc.) and a complex block of combinational gates to calculate the next state.

| Component | Function | Detail |
| :--- | :--- | :--- |
| **Sequential Elements** | Two D-Flip-Flops (`dffrp`) | Stores the current state Q0 and Q1. |
| **Combinational Block** | OAI21, AND2, etc. | Calculates the next state inputs D0, D1 based on the current state. |
| **Control Logic** | Inverter Chain | Converts the active-high reset into the active-low  ~RESET_B signal. |

---

##### 2. Logic Implementation

The complexity comes from the fact that the next state of a higher bit depends on the current state of all lower bits.

* **LSB Logic (Bit 0):** This bit is implemented with simple toggle logic, similar to the single-bit counter.
    D0 = Q0
* **MSB Logic (Bit 1):** The next state is calculated by a more complex function that only allows the MSB to toggle when the LSB is '1'. The logic utilizes gates like OAI21 and AND2 to implement the function, typically an Exclusive-OR (XOR):
    D1 = Q1 Q0

##### 3. Functional Behavior

The circuit acts as an **N-bit Synchronous Up-Counter**:

* **Synchronization:** All state bits Q0, Q1, etc. update simultaneously on the positive edge of the clk.
* **Counting:** The state transitions sequentially (00  01 10  11  00...).
* **Reset:** The entire counter is cleared to 00 when the reset signal is active.










 






  
  
