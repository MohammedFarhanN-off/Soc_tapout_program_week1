
# Introduction to Verilog RTL Design & Synthesis
Day-1 of Video demonstrates the process of simulating and synthesizing digital circuits using industry-standard tools. We start with Icarus Verilog (iverilog) and GTKWave for functional simulation, allowing us to verify the correctness of our Verilog designs by observing waveform outputs. Then, we move to Yosys, an open-source synthesis tool, to convert the Verilog code into a gate-level representation, preparing it for implementation on FPGAs or ASICs. This workflow highlights the complete journey from writing RTL code to verifying and synthesizing it efficiently.
## Table of Contents

 [1. Design and Testbench](#1-Design-and-Testbench)
 
 [2. Multiplexer Code ](#1-Multiplexer-Code) 

 [3. VIM Text Editor](#1-VIM-Text-Editor)

 [4. Iverilog Simulator](#1-Iverilog-Simulator)

 [5. GTKWave](#1-GTKWave)

 [6. Netlist](#1-Netlist)

 [7. Standard Cells](#1-Cells)

 [8. Yosys Synthesizer](#1-Yosys)


### Design and Testbench

   Design in Verilog defines the hardware logic that you want to implement. It uses constructs like module, input, output, wire, reg, and always blocks to describe the behavior of the circuit.

   Testbench in Verilog program used to verify the design. It instantiates the design module, generates input signals, applies test vectors, and monitors the outputs to check if they behave as expected.It is never synthesized into hardware. Its sole purpose is to simulate and validate the design before fabrication or deployment.

   ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-1/Images/Screenshot%202025-09-22%20181702.png?raw=true)

### Multiplexer Code

   Design

      module good_mux(input i0, input i1, input sel, output reg y);
      always @(*)
      begin
      if(sel)
         y <= i1;
      else
         y <= i0;
      end
      endmodule

Testbench

      module tb_good_mux;
      // Inputs
      reg i0,i1,sel;
      // Outputs
      wire y;

      // Instantiate the Unit Under Test (UUT)
      good_mux uut(
         .sel(sel),
         .i0(i0),
         .i1(i1),
         .y(y)
      );

      initial begin
         $dumpfile("tb_good_mux.vcd");
         $dumpvars(0, tb_good_mux);
         // Initialize Inputs
         sel = 0;
         i0 = 0;
         i1 = 0;
         #300 $finish;
      end

      always #75 sel = ~sel;
      always #10 i0 = ~i0;
      always #55 i1 = ~i1;
      endmodule
   The good_mux module is a 2-to-1 multiplexer that uses an if-else statement to select between input i0 and input i1 based on the sel signal, and assigns the result to output y.
   The testbench module (tb_good_mux) is designed to verify the functionality of the good_mux module
## VIM Text Editor
Vim is a highly configurable, free, and open-source text editor.

    gvim tb_good_mux.v -O good_mux.v
   ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-1/Images/VirtualBox_Ubuntu_23_09_2025_18_51_50.png?raw=true)

## Iverilog Simulator

Simulator is a software program that models the behavior of a physical system or process over time. In the context of hardware design (like the Verilog code you provided earlier), a simulator allows engineers to test and verify their digital circuits without needing to build the actual hardware.

Iverilog is a free, open-source Verilog compiler and simulator. It takes Verilog HDL (Hardware Description Language) code, compiles it into an executable format, and then simulates the behavior of the described digital circuit.

    iverilog tb_good_mux.v good_mux.v
    ./a.out

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-1/Images/VirtualBox_Ubuntu_23_09_2025_21_09_35.png?raw=true)

## GTKWave
GTKWave is a free and open-source waveform viewer for analyzing digital simulation results. It's a key tool in the digital design and verification workflow, used to visually inspect the behavior of signals over time after a simulation is run.

    gtkwave tb_good_mux.vcd
   
![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-1/Images/VirtualBox_Ubuntu_23_09_2025_21_10_37.png?raw=true)

## Standard Cells

 ### Slower cell
Slower cell is a standard cell that has been designed to have a higher propagation delay compared to its "fast" counterparts. It is the version of a logic gate with the lowest drive strength, meaning it uses smaller transistors.
 ### Faster cell
Faster cell is a standard cell with a high drive strength, meaning it can quickly charge or discharge a capacitive load. This results in a low propagation delay, allowing signals to travel through it faster.

| Cells | Power |Area|Delay|
|:------|:------|:---|:----|
|Slower cell|Low|Low|High|
|Faster cell|High|High|Low|

## Yosys Synthesizer
 
  Synthesizer is a software program that converts a high-level description of a circuit into a low-level, optimized implementation. The most common input is a Register-Transfer Level (RTL) description written in a Hardware Description Language (HDL) like Verilog or VHDL.The output of the synthesizer is a gate-level netlist.

  Yosys is a free and open-source framework for RTL synthesis. It takes hardware designs written in Verilog and synthesizes them into a gate-level netlist.

    yosys
    read-liberty -lib ../lib/
    read_verilog good_mux.v
    synth -top good_mux
    abc -libertu ../lib/
    show
    
## Netlist
Netlist is a textual description of an electronic circuit. It's a list of all the components (like logic gates, transistors, and flip-flops) and the "nets" or wires that connect them. Think of it as a blueprint for a circuit, specifying how all the individual parts are wired together.

  ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-1/Images/VirtualBox_Ubuntu_23_09_2025_21_28_42.png?raw=true)

   

  


