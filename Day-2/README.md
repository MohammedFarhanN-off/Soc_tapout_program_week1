#  Timing libs, hierarchical vs flat synthesis and efficient flop coding styles
 Timing libraries (Lib files) provide essential delay, power, and functional information of standard cells for accurate timing analysis. Hierarchical vs. flat synthesis deals with whether a design is compiled block-by-block (hierarchical) or as a whole (flat), each having trade-offs in runtime, memory, and optimization quality. Efficient flop coding styles in RTL ensure better synthesis results, reduce glitches, and improve timing closure while maintaining design reliability.

 ## Table of Contents
1. [Dot Library File](./dot-libray-file-analysis)
2. [Multiple Module Code Analysis](./multiple-module-code-analysis)
3. [Hierarchical Synthesis](./hierarchical-synthesis)
4. [Flattened Synthesis](./flattened-synthesis)
5. [D-flipflop Code Styles](./D-flipflop-code-styles)
6. [Multiplication Code](./multiplication-code)



## Dot Library File
   
   .lib(sky130-fd-sc-d-hd-tt-025C-1v80.lib) contain standard cells

#### PVT (Process, Voltage, Temperature) Conditions

- **Process (tt):** Represents semiconductor fabrication variations such as typical, slow, or fast corners.  
- **Voltage (1v80):** Affects switching speed and power consumption.  
- **Temperature (025C):** Influences cell delay and leakage characteristics.  

      gvim ../lib/sky130-fd-sc-d-hd-tt-025C-1v80.lib)

In Text editor

    :syn off

    /cell
![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_10_22_28.png?raw=true)

    /cell .*and

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_10_33_45.png?raw=true)

## Multiple Module Code Anlalysis

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_11_43.png?raw=true)


sub_module2: It takes two inputs, a and b, and assigns the bitwise OR of them to the output y. The operation is y = a | b.

sub_module1: It takes two inputs, a and b, and assigns the bitwise AND of them to the output y. The operation is y = a & b.

multiple_modules: This is the top-level module that instantiates and connects the two sub-modules. It takes three inputs, a, b, and c and has one output y.

## Hierarchical Synthesis

Hierarchical synthesis is a synthesis approach where a large design is broken down into smaller functional blocks (modules), and each block is synthesized separately before combining them into the top-level design. This method helps manage complexity, improves compile time, and allows reuse of pre-synthesized IPs or modules.
      yosys
      read_liberty -lib ../lib/sky130-fd-sc-d-hd-tt-025C-1v80.lib
      read_verilog multiple_modules.v
      synth -top multiple_modules
      abc -liberty ../lib/sky130-fd-sc-d-hd-tt-025C-1v80.lib
      show multiple_modules
      
-[image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_19_14.png?raw=true)

      write_verilog -noattr multiple_modules_hier.v
      !gvim multiple_modules_hier.v

-[image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_25_21.png?raw=true)


 ## Flattened Synthesis

 Flattened synthesis is a method where the entire design hierarchy is collapsed into a single-level netlist before optimization. This allows the synthesis tool to perform global optimizations across module boundaries, often improving timing and area efficiency.

      yosys
      read_liberty -lib ../lib/sky130-fd-sc-d-hd-tt-025C-1v80.lib
      read_verilog multiple_modules.v
      flatten
      synth -top multiple_modules
      abc -liberty ../lib/sky130-fd-sc-d-hd-tt-025C-1v80.lib
      show multiple_modules
-[image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_37_30.png?raw=true)


      flatten
      write_verilog -noattr multiple_modules_flat.v
      !gvim multiple_modules_flat.v
-[image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_40_54.png?raw=true)

## D-flipflop Code Styles
      
   |Synchronous|Aysynchronous|
   |:----------|:------------|
   |Logic works respective of clock|Logic works irrespective of clock

 ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_48_52.png?raw=true)

   #### dff_asyncres_syncres.v
   Demonstrates a D-flipflop with both asynchronous and synchronous reset capabilities.

   ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_51_52.png?raw=true)

   #### dff_asyncres.v
   D-flipflop with an asynchronous reset. The reset is prioritized and takes effect immediately, independent of the clock edge.

   ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_53_32.png?raw=true)

   #### dff_syncres.v
   D-flipflop with a synchronous reset. The reset signal is sampled and applied only on the active clock edge.

   ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_55_02.png?raw=true)

   #### dff_asyn_set.v
   D-flipflop with an asynchronous set signal, which immediately sets the output to '1' when active.

   ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_09_56_49.png?raw=true)

   ## Multiplication Code
   
   Hardware multipliers are expensive in terms of area and power. For specific cases, you can use more efficient shift-and-add logic.

   ![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_10_02_35.png?raw=true)

mult_2.v

Condition

      input = a
      multiplier=2^n
      output y=a + n zeroes padded
      eg:
         a=3'b111
         m=2
         y=4'b1110

![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_10_14_00.png?raw=true)
![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_10_20_25.png?raw=true)

mult_8.v

Condition

      input = a
      multiplier = 2^n + 1
      ouput y = a + a 
      eg:
         a=3'b001
         m=9
         y=6'b001001


![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_10_15_11.png?raw=true)
![image alt](https://github.com/MohammedFarhanN-off/Soc_tapout_program_week1/blob/main/Day-2/Images/VirtualBox_Ubuntu_24_09_2025_10_18_54.png?raw=true)

      



   

 









 
