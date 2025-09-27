
# Optimization in synthesis
In digital synthesis, control constructs like if, case, and for play a crucial role in defining hardware structure, and synthesis tools optimize them to generate efficient logic. An if statement is typically mapped into priority multiplexers, which the synthesizer simplifies by removing unreachable conditions or constant branches. A case statement, on the other hand, is generally implemented as parallel multiplexers, offering faster and more balanced logic compared to nested if chains, and synthesis tools optimize it into compact decoders or multiplexers depending on the encoding. Meanwhile, a for loop does not behave like a software loop but is fully unrolled during synthesis, creating replicated hardware for each iteration, with further optimizations such as logic sharing or parallel structures applied when possible. Together, these constructs allow designers to write concise RTL while relying on the synthesizer to perform constant propagation, dead code elimination, and resource sharing for efficient hardware implementation.

## Contents

[1.If Constructs](#if-constructs)

[2.Case Constructs](#case-constructs)

[3.For Loop Constructs](#for-loop-constructs)

## If Constructs

In synthesis, if constructs are used to describe conditional behavior and are typically translated into priority multiplexers. The synthesizer evaluates each condition in sequence, meaning that the first true condition takes precedence, which can impact both logic structure and timing. During optimization, redundant or constant branches are removed through constant propagation and dead code elimination, reducing unnecessary hardware. Nested if-else statements may also be simplified into Boolean equations or implemented as priority encoders, depending on the design intent. Thus, if constructs provide flexibility for conditional control while synthesis tools ensure the resulting hardware is optimized for area, speed, or power.

### Caveat in If Constructs

When you use an if without else if or else, the synthesizer interprets it as “assign only under certain conditions”. For all other conditions, the signal retains its previous value, which leads to **latch inference** unless the signal is assigned a value in every possible path. Latches are generally undesirable because they increase timing complexity, can cause glitches, and are not always supported in synchronous FPGA/ASIC design flows. To avoid this, designers should ensure that every signal inside an always block is fully assigned either by using a default assignment at the start of the block or by including an else branch to cover all conditions.

#### Verilog file: incomp_if.v

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_09_31_44.png?raw=true)

Why a Latch is Inferred?
Combinational Logic Expectation: An always @(*) block is used to describe combinational logic. A fundamental rule of combinational logic is that the outputs must always be a direct function of the current inputs. There should be no memory.

Incomplete Specification: 



    if (i0)
      y <= i1;

This translates to: "If i0 is true (or 1), then y should get the value of i1."

The Missing else: The code does not specify what should happen to y when i0 is false (or 0). To handle this ambiguity, the synthesis tool must create a circuit that remembers or holds the last value of y. The digital circuit element that does this is a latch.

In hardware terms, the inferred latch behaves like this:

When i0 is 1 (the "enable" signal), the latch is transparent, and the value of i1 passes through to y.

When i0 is 0, the latch closes and holds whatever value y had previously.

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_09_34_36.png?raw=true)

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_09_37_54.png?raw=true)

#### Verilog file: incomp_if2.v

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_09_36_56.png?raw=true)

Why a Latch is Inferred?
Incomplete Condition Coverage: 
always @(*) block:

If i0 is true: y is assigned the value of i1. (This case is covered).

Else if i2 is true: This branch is only evaluated if i0 is false. So, if i0 is false AND i2 is true, y is assigned the value of i3. (This case is also covered).

The Missing Case: What happens if both i0 is false AND i2 is false? Your code provides no instruction for what y should be in this scenario.

Because this specific condition is undefined, the synthesis tool must infer a memory element to hold or "latch" the previous value of y whenever that condition occurs. This is an inferred latch.

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_09_36_13.png?raw=true)

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_09_38_45.png?raw=true)

## Case Constructs

In synthesis, case constructs are used to describe conditional branching where one of several possible choices is selected based on the value of an expression. Unlike if constructs, which create priority logic, a case statement is usually synthesized as parallel multiplexers or a decoder with a multiplexer, resulting in more balanced and faster logic. A major advantage of case is readability and efficiency when handling multiple mutually exclusive conditions, such as state machines or decoders. However, a caveat arises if not all possible input values are covered—this can cause latch inference or unintended priority behavior. To avoid this, designers typically use default cases to ensure complete assignment, or synthesis directives like parallel_case and full_case in some tools. Overall, case constructs are well-suited for implementing FSMs, decoders, and multiplexers in a clean and optimized manner.



### Caveat in Case Constructs

A key caveat in case constructs is that if not all possible input values are covered and no default branch is provided, the synthesizer may infer latches to preserve the previous output value for unhandled cases, which can lead to timing and reliability issues. Another caveat is the difference between priority vs. parallel behavior: while case statements are generally parallel (all options are checked at once), some synthesis tools may still optimize them as priority logic if overlapping conditions exist. In FPGA/ASIC flows, improper use of synthesis directives like full_case or parallel_case can also create mismatches between simulation and synthesis, since they might suppress warnings and allow unintended hardware to be generated. To avoid these issues, designers should always ensure complete case coverage with a default branch and avoid overlapping conditions unless priority is explicitly desired.

### Verilog file:incomp_case

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_10_27_56.png?raw=true)

In that specific case statement code, a latch is inferred because the code does not cover all possible values of the 2-bit sel input.

Here is the breakdown for that exact code:

The input sel is 2 bits wide, so it can have four possible binary values: 00, 01, 10, and 11.

Your case statement only tells the circuit what to do when sel is 00 or 01.

It provides no instruction for the output y when sel is 10 or 11.

Because the behavior for sel = 2'b10 and sel = 2'b11 is undefined, the synthesizer creates a latch to hold the last value of y whenever those conditions occur.

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_10_38_07.png?raw=true)

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_11_02_58.png?raw=true)

### Verilog file:partial_case_assign.v

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_10_53_20.png?raw=true)

The output y is fully specified. It gets a value in every possible condition:

When sel is 2'b00, y is assigned i0.

When sel is 2'b01, y is assigned i1.

In the default case (covering 2'b10 and 2'b11), y is assigned i1.
Since y is always given a value, the logic for it is purely combinational.

Output x (Latch Inferred)
The output x is not assigned a value in all conditions:

When sel is 2'b00, x is assigned i2.

When sel is 2'b01, there is no assignment for x.

In the default case, x is assigned i2.

Because the code doesn't specify what to do with x when sel is 2'b01, the synthesis tool must infer a latch to hold the previous value of x for that specific case.

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_11_04_36.png?raw=true)

### Verilog file:bad_case.v

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_11_00_26.png?raw=true)
"Overlapping" in a case statement means that more than one case item can be true for the same input value.

When this happens, the case statement no longer behaves like a simple parallel switch (multiplexer). Instead, it behaves like a priority encoder, where the first matching item in the code is the one that gets executed.

This is not possible in a standard case statement with unique binary numbers, but it is the entire point of using the special casez and casex statements in Verilog.

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_11_05_43.png?raw=true)

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_11_01_48.png?raw=true)

## For Loop constructs
1. Procedural for loop

Where used: Inside always blocks or initial blocks.

Behavior: The loop is fully unrolled by the synthesizer, creating copies of hardware for each iteration.

Use case: Useful when performing repetitive operations, like adding up array elements, shifting data, or initializing signals.

Example:

    always @(*) begin
        sum = 0;
        for (i = 0; i < 4; i = i + 1)
            sum = sum + data[i];
    end


Synthesized as a chain of adders (or optimized to a tree structure).

Caveat: Loop bounds must be constant; otherwise, synthesis fails.

🔹 2. Generate for loop

Where used: At the module level, outside of procedural blocks.

Behavior: Used with the generate keyword to instantiate multiple modules, wires, or blocks of logic systematically.

Use case: Ideal for creating structural hardware replication, such as arrays of adders, flip-flops, or multiplexers.

Example:

    genvar i;
    generate
        for (i = 0; i < 4; i = i + 1) begin : gen_block
            dff dff_inst (.clk(clk), .d(d[i]), .q(q[i]));
        end
    endgenerate


Synthesizes as 4 flip-flops instantiated structurally.

Caveat: Only works with module instantiations, continuous assignments, and always blocks (not procedural statements).

### MUX

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_14_28_59.png?raw=true)

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_14_30_43.png?raw=true)

### DEMUX

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_14_31_40.png?raw=true)

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_14_33_48.png?raw=true)

### Generative For Loop

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_14_34_54.png?raw=true)

![img](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-5/Images/VirtualBox_Ubuntu_26_09_2025_14_36_57.png?raw=true)





