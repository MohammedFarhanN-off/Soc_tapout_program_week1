
# GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

Gate-Level Simulation (GLS) is a crucial step in digital design verification, where the synthesized netlist is simulated to ensure functional correctness under real hardware conditions. During coding, the choice between blocking (=) and non-blocking (<=) assignments in Verilog plays a key role, as misuse can lead to unexpected behavior. Such issues often contribute to synthesis–simulation mismatch, where a design works correctly in RTL simulation but behaves differently after synthesis or in GLS. Understanding these concepts is essential for writing reliable, synthesis-friendly RTL code.

## Contents
 
 [1.Gate Level Simulation](#gate-level-simulation)

 [2.Synthesis-Simulation Mismatch](#synthesis-simulation-mismatch)
 
## Gate Level Simulation
   Gate-Level Simulation (GLS) is the process of simulating a digital design after synthesis using the generated gate-level netlist. Unlike RTL simulation, which verifies functionality at a higher level, GLS reflects the actual hardware implementation with real gates, timing delays, and optimizations introduced during synthesis. It is mainly used to validate that the synthesized netlist is functionally equivalent to the RTL code, to catch issues like synthesis–simulation mismatches, and to check for problems such as race conditions, timing violations, or initialization errors.
   ![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/Screenshot%202025-09-25%20110800.png?raw=true)

### RTL and Netlist
![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_21_09_05.png?raw=true)
   
   ![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_13_47_35.png?raw=true)

### GLS
  
    iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v 
    ./a.out
    gtkwave tb_ternary_operator_mux.vcd

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_13_44_24.png?raw=true)

## Synthesis-Simulation Mismatch
Synthesis–Simulation Mismatch occurs when the behavior of a design during RTL simulation does not match the behavior after synthesis or during Gate-Level Simulation. This mismatch often arises from using non-synthesizable constructs, improper coding practices, or incorrect use of blocking and non-blocking assignments in Verilog. For example, simulation might initialize signals to default values, while in actual synthesized hardware those signals remain undefined until driven. Such discrepancies can cause functional differences between the simulated model and the real hardware, making it critical to follow synthesis-friendly coding guidelines and verify designs through GLS.

### Missing Sensitivity List
This issue applies to combinational logic written inside an always block (in Verilog).

In Simulation (The Simulator): The simulator executes an always block only when a signal in its sensitivity list (@(...)) changes. If an input signal to the combinational logic is missing from this list, the simulator will fail to update the output when that signal changes, thus exhibiting a behavior that is latched or sequential.

In Synthesis (The Tool): The synthesis tool ignores the sensitivity list for combinational logic. It looks at the logic equations inside the block and infers a pure combinational circuit (a network of gates) that is instantly sensitive to all inputs.

The Mismatch: The simulated code might appear to work but is functionally incorrect (due to the missing trigger), while the synthesized hardware is purely combinational. This difference in behavior is a fatal mismatch.

The Fix: Use always @(*) in Verilog or always_comb in SystemVerilog. These constructs automatically include all right-hand-side (RHS) signals in the sensitivity list, eliminating the manual error.
#### RTL and Netlist
![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_21_09_29.png?raw=true)

The Verilog code for bad_mux exhibits a classic Synthesis-Simulation Mismatch due to an incomplete sensitivity list in the always block. The hardware inferred by the synthesis tool will not match the behavior seen in the RTL simulation.

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_13_59_48.png?raw=true)

#### RTL Simulation
 
 ![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_13_58_13.png?raw=true)
In RTL (pre-synthesis) simulation, the simulator is event-driven and strictly follows the sensitivity list.

**Trigger Condition**: The always block only executes when the signal sel changes.

**Missing Triggers**: The input signals i0 and i1 are used on the right-hand side of the assignments but are NOT in the sensitivity list.

**Resulting Behavior**:
If i0 or i1 changes, the always block does not execute, and the value of y remains unchanged.
The variable y will hold its previous value, even when a new value is available on i0 or i1.

**Hardware Implication**: The simulator behaves as if a latch (a form of memory) is inferred. The output y is only updated when sel changes, and it holds the old data when i0 or i1 changes.

#### GLS

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_14_01_29.png?raw=true)

In Synthesis (post-synthesis), the tool's goal is to create physical hardware based on the logic described in the always block.

**Sensitivity List is Ignored**: For combinational logic, the synthesis tool generally ignores the sensitivity list (or issues a warning) and looks at all the signals used to calculate the output.

**Inferred Hardware**: The code structure (if-else assigning to y with an implicit assignment to y in both branches) clearly describes a 2-to-1 Multiplexer (MUX), which is pure combinational logic.

**The logic is**: y=(sel⋅i1)+(~sel⋅i0)

**Resulting Behavior**:The synthesized circuit is purely combinational. The output y will change immediately whenever sel, i0, or i1 changes, just like a real MUX.

**Hardware Implication**: The synthesis tool correctly infers a 2-to-1 Multiplexer (built from logic gates).


### Blocking vs Non-Blocking Assignments
Blocking (=) executes statements sequentially within an always block, which can lead to unintended behavior if used in sequential (clocked) logic. In simulation, blocking assignments may update variables immediately, but in synthesis, the hardware doesn’t behave in the same order, causing mismatches.

Non-blocking (<=) allows all assignments in a clocked block to happen in parallel, matching the behavior of flip-flops in real hardware. Using non-blocking in sequential logic ensures that simulation and synthesized hardware remain consistent.

#### RTL and Netlist
![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_21_09_05.png?raw=true)
The provided Verilog code, module blocking_caveat, uses blocking assignments (=) within a combinational always @(*) block, which usually results in a synthesis-simulation mismatch only when the order of assignments creates a data dependency that the simulator interprets differently from the synthesizer.

However, in this specific code, the assignments are:

d = x & c;

x = a | b;
![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_14_07_27.png?raw=true)

This structure has a potential issue, but because it's combinational logic and is handled as a single step, the results usually match—though the process is interpreted very differently.

#### RTL Simulation
 
 ![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_14_04_27.png?raw=true)
 In RTL (pre-synthesis) simulation, the blocking assignments (=) are executed sequentially and immediately.

The simulator enters the always @(*) block when any input (a, b, c) changes.

Line 1 (d = x & c;): The simulator evaluates the expression using the previous (old) value of x (from the last time the always block ran) and the current value of c. The new value of d is calculated and immediately updated.

Line 2 (x = a | b;): The simulator then calculates the new value of x using the current values of a and b. The new value of x is immediately updated.

The Caveat: Because d is calculated using the old value of x, the value of d in the simulation is incorrect because it has one logic-level delay on the path from a and b. If the simulator were to execute this code, the output d would behave as if there were a latch on x and would only reflect the change from a or b on the next simulation pass triggered by an input change.

Resulting Logic (Simulated): The logic for d is dependent on the previous state of the x register, meaning the code simulates a circuit with an unintentional register/latch.

#### GLS

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-4/Images/VirtualBox_Ubuntu_25_09_2025_14_06_15.png?raw=true)

In Synthesis (post-synthesis), the tool analyzes the entire always @(*) block as a definition of pure combinational logic.

**Sequential Order is Ignored**: The synthesizer disregards the line-by-line execution order. It treats the block as a single set of simultaneous equations.

**Structural Simplification**: The synthesizer determines the final logic for each output variable (d and x in this case). It realizes that x is simply a | b, and it can substitute this expression directly into the equation for d.

**Final Logic**:
x=a+b
d=x.c
d=(a.b).c

**Resulting Logic (Synthesized)**:
 The tool infers a purely combinational circuit where the outputs x and d change immediately whenever a, b, or c changes. No registers or latches are created for this logic.













  


