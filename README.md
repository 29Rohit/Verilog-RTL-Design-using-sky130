# Open Source RTL Design using Sky130 Technolgy
![1](https://user-images.githubusercontent.com/20563301/120014244-74ad1d00-bfff-11eb-9d53-8df2784ecb07.PNG)

This repository contains the usage of tools like iverilog, gtkwave and yosys for open-source RTL Design. The content is documentation of tasks carried out during VSD "RTL Design Using Verilog With SKY130 Technology".

SKY130 is the hardware industry's first open-source process design kit (PDK) released by SkyWater Technology Foundry in collaboration with Google giving all hardware design experts and aficionados, a worldwide access to their IP functions and open source ASICs. More details [here.](https://github.com/google/skywater-pdk).

This particular workshop covers the various aspects of design in Verilog HDL both theoretically and practically with labs using open-source softwares through their VSD-IAT portal. Beginning with an introduction to digital design using Verilog HDL and various libraby, the instructors cover digital design steps that include design, functional simulation, test bench based validation of the design functionality and logic Synthesis with optimization. Further, we learn about efficient verilog coding styles that result in a predictable logic in Silicon.

## Table of Contents

- [Day 1 Introduction to Verilog RTL Design and Synthesis](#Day-1-Introduction-to-Verilog-RTL-Design-and-Synthesis)
  * [Setup the lab instance with libraries and verilog files](#Setup-the-lab-instance-with-libraries-and-verilog-files)
  * [Labs using iverilog and gtkwave](#Labs-using-iverilog-and-gtkwave)
  * [Introduction to Yosys and Logic synthesis](#Introduction-to-Yosys-and-Logic-synthesis)
  * [Labs using Yosys and Sky130 for 2x1 Multiplex](#Labs-using-Yosys-and-Sky130-for-2-x-1-Multiplex)
- [Day 2 Timing libs Hierarical vs Flat Synthesis and efficient Flop Coding Styles](#Day-2-Timing-libs-Hierarical-vs-Flat-Synthesis-and-efficient-Flop-Coding-Styles)
  * [Introduction to timing .libs](#Introduction-to-timing-.libs)
  * [Hierarchical vs Flat Synthesis](#Hierarchical-vs-Flat-Synthesis)
  * [Efficient Flip-flop coding styles and Optimizations](#Efficient-Flip-flop-coding-styles-and-Optimizations) 
- [Day 3 Combinational and sequential optimization](#Day-3-Combinational-and-sequential-optimization)  
  * [Introduction to optimizations](#Introduction-to-optimizations)
  * [Combinational logic optimizations (Labs)](#Combinational-logic-optimizations-(Labs))
  * [Sequential logic optimizations (Labs)](#Sequential-logic-optimizations-(Labs))
  * [Sequential optimzations for unused outputs](#Sequential-optimzations-for-unused-outputs)
- [Day 4 GLS blocking vs non blocking and Synthesis Simulation mismatch](#Day-4-GLS-,blocking-vs-non-blocking-and-Synthesis-Simulation-mismatch)
  * [GLS, Synthesis-Simulation mismatch and Blocking/Non-blocking statements](#GLS,-Synthesis-Simulation-mismatch-and-Blocking/Non-blocking-statements)
  * [Labs on GLS and Synthesis-Simulation Mismatch](#Labs-on-GLS-and-Synthesis-Simulation-Mismatch)
  * [Labs on synth-sim mismatch for blocking statement](#Labs-on-synth-sim-mismatch-for-blocking-statement)
- [Day 5 If, case, for loop and for generate](#Day-5-If-case-for-loop-and-for-generate)
   * [If Case constructs](#If-Case-constructs)
   * [Labs on "Incomplete If Case"](#Labs-on-"Incomplete-If-Case")
   * [Labs on "Incomplete overlapping Case"](#Labs-on-"Incomplete-overlapping-Case")
   * [for loop and for generate](#for-loop-and-for-generate)
   * [Labs on "for loop" and "for generate"](#Labs-on-"for-loop"-and"-for-generate")
- [Acknowledgements](#Acknowledgements)
- [References](#References)


## Day 1 Introduction to Verilog RTL Design and Synthesis

Usually front-end RTL design is carried out using huge EDA Tools from different vendors. Even though many of the tools come with free limited versions its better to give a try for development using open-source tools. Below is the list of tools used by this repo.

RTL Design - It consists of an actual verilog code / a set of verilog codes that have the functionality to meet the required design specifications of the circuit Testbench - It is a setup that one uses to apply a set of  stimuli (test-case vector) to check the functionalility of the design file.

The simulator is loaded with the design and its respective testbench file after which it looks for changes in the input signals and depending on the change, the output is evaluated. These changes in input and corresponding output values are dumped in a special format file called "value change dump" (.vcd) file. This file can be pictorially represented in waveforms using a waveform tool like gtkwave.


### Setup the lab instance with libraries and verilog files

Firstly, we have to clone 2 separate repositories namely vsdflow and sky130RTLDesignAndSynthesisWorkshop which contain the required library files and verilog design files to perform the simulations and logic synthesis parts of the workshop. It can be done using basic linux command gitclone filelink

```
git clone https://github.com/kunalg123/vsdflow.git. 
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```

We are given a default set of files and libraries shown below to work on using the practical lab instance

![3](https://user-images.githubusercontent.com/20563301/120075523-45f67b80-c0bf-11eb-8138-daef35db54c7.PNG)



![4](https://user-images.githubusercontent.com/20563301/120075534-50b11080-c0bf-11eb-89cb-07f3af1dd16d.PNG)



### Labs using iverilog and gtkwave

The Simulator is tool which is uses to look for the changes in input signals and check if the input signals are following the given design by dumping the vcd file of design and teshbench into itself. 

The follwing commands are used for simulation using gtkwave and iverilog

```
sudo apt install iverilog : to enter into root mode to install the iverilog files.

iverilog design_file.v test_bench.v : It combinees the design files and associated test bench to produces a executable file.

./a.out : Dump the iverilog file to produce vcd file.

gtkwave tb_bad_mux.vcd : It generates value change dump (.vcd) file into such a file that can be viewed for waveform using gtkwave waveform viewer.
```

![7](https://user-images.githubusercontent.com/20563301/120075573-70e0cf80-c0bf-11eb-9ed3-b52b709c529d.PNG)


![1](https://user-images.githubusercontent.com/20563301/120076148-c0c09600-c0c1-11eb-9963-c6e8829e2e08.PNG)


### Introduction to Yosys and Logic synthesis

1) yosys 
-Afher the simulation of RTL Design with its respective Testbench, we need to perform the synthesis of deign with sky130.lib by using yosys Synthesizer. The yosys Synthesizer is a tool to convert the RTL Design file into netlist(Gate level translation) by using the standard cells like gates,combinational cells etc which are present in the .libraby file
- The yosys setup is such that it reads the verilog files from design and read liberty from .lib and synthesizes the file and creates a netlist by write_verilog.
- RTL Design is basically a behavioral representation of verilog file and netlist is file which is created by converting the design into gates and connections is made between the gates. The netlist has the same Testbench as the set of primary inputs/outputs will remain same between RTL design and Synthesized netlist.

2) .lib 
- lib files are a collection of logical modules which include logic gates like AND, OR, NOT, NAND, NOR etc which contain different flavours of same gates depending upon the input and seppedof circuit.
-  Combinational delays present in a logic path determine the maximum speed and performance of a logic circuit.In order to get maximum performance from a circuit, we need fast cells to minimize the clock delays and minimum setup time of circuit.
-  In the same way, in order to avoid Hold violations in a logic path, we have to use Slow cells to synchronize the hold time for logic path.All these different types of fast and slow cells are present in a .lib file to be used by the synthesis software tool

3) Faster Cells vs Slower Cells
- A cell delay in the digital logic circuit depends on the load of the circuit which here is Capacitance.
- Faster the charging / discharging of the capacitance --> Lesser is the Cell Delay.
- In order to charge/discharge the capacitance faster, we use wider transistors that can source more current. This will help us to reduce the cell delay but at the same time, wider transistors consumer more power and area. Similarly, using narrower transistors help in reduced area and power but the circuit will have a higher cell delay. Hence, we have to compromise on area and power if we are to design a circuit with low cell delay.

4) Constraints
A Constraint is a guidance file given to a synthesizer in order to enable an optimum implementation of the logic circuit by selecting the appropriate flavour of cells (fast or slow) to get minimum delays.

5) Practical Synthesis using yosys

The Commands used for Synthesis using yosys
```
$yosys                                                                             --> invokes YOSYS tool

yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib           --> reads the corresponding library file

yosys> read_verilog good_mux.v                                                     --> reads the Verilog script

yosys> synth -top good_mux                                                         --> reads the top level module

yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib                    --> converts the logic file to netlist

yosys> show                                                                        --> Final netlist output display
```

![5](https://user-images.githubusercontent.com/20563301/120076009-4728a800-c0c1-11eb-9345-897f6b55c49d.PNG)


![6](https://user-images.githubusercontent.com/20563301/120076028-560f5a80-c0c1-11eb-9151-c42281f2e390.PNG)


![10](https://user-images.githubusercontent.com/20563301/120076041-66273a00-c0c1-11eb-89ae-1f6afcafb3da.PNG)



### Labs using Yosys and Sky130 for 2x1 Multiplex

- We will perform a synthesis example of the 2:1 Multiplexer RTL design using YOSYS with appropriate library files from SKY130 technology that we cloned into the directory.
-The yosys Commands used are as follows
```
yosys 

read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog good_mux.v

synth -top good_mux

abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

show
```


![2](https://user-images.githubusercontent.com/20563301/120076119-aab2d580-c0c1-11eb-857c-8504e6f10cf3.PNG)


## Day 2 Timing libs Hierarical vs Flat Synthesis and efficient Flop Coding Styles

### Introduction to timing .libs

![l2](https://user-images.githubusercontent.com/20563301/120070250-33704800-c0a7-11eb-950f-a9fe915c8505.PNG)

```
vim ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib : shows the text file of sky130_fd_sc_hd__tt_-025C_1v80.lib
```

![l1](https://user-images.githubusercontent.com/20563301/120070269-43882780-c0a7-11eb-8c7b-34718e909baf.PNG)


Above show the text file of sky130_fd_sc_hd__tt_-025C_1v80.lib
We have seen that a .lib file is a collection of different flavours of standard cells with nets. In this workshop, we use the **sky130_fd_sc_hd_tt_025C_1v80.lib**. Looking in depth into the naming of this lib file, it denotes the following:

fd --> Foundry

sd --> Standard Cell

hd --> High Density

tt --> Typical Process

025C --> Temperature 

1v80 --> Voltage 
Above paraments determine how fast/slow silicon works depending upon its respective variations. It contain various different flavor of gates ,its power leakage,size etc. Area and Power of gate is inversely proportional to delay.


![image](https://user-images.githubusercontent.com/20563301/120070366-c7421400-c0a7-11eb-86bb-f4e6fa8e3532.png)

```
•	syn off : removes all color text	
•	se hls: : mark the beginning of cell
•	sp  ../ my_lib/verilog_model/sky130_fd_sc_hd__a2111o.behavioral.v :  shows the behavioral modelling of a21110.
•	vsp : add a text file along with existing file.
```
### Hierarchical vs Flat Synthesis

Consider a simple example `multiple_modules` which instntiates two modules `sub_module1` and `sub_module2`. The sub-modules realize and logic and or logic respectively.

### 1) Hierarchial Synthesis
- Hierarchial synthesis can be performed using yosys by following the steps mentioned in [above section](#yosys). When there are more than one modules we need to explicitly mention the name of the module while running `show` command. The show command does not show the internals of the submodules, it is maintaining hierarchial representation.
-	In yosys synthesis of Hierarchical and Flat Synthesis a NAND gate with both inputs inverted using two inverters is implemented instead of Nor gate because stacked NMOS is better than stacked PMOS which has low mobility and to remove it we need a good logical effort.
-	The schematic of hierarchical design in Yosys do not show the internal logic of any submodule instantiated.

```
$yosys
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib           

yosys> read_verilog multiple_modules.v                                                     

yosys> synth -top mutiple_modules                                                         

yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib                    

yosys> show multiple_modules : show the diagram of default multiple modules

write_verilog -noattr multiple_modules_hier.v : dump the Verilog netlist file of multiple module and its submodule in its vcd format through Hierarchical Synthesis

!vim multiple_modules_hier.v : show the text file of multiple modules in Hierarchical Synthesis

show multiple_modules : show the circuit diagram of multiple modules in Hierarchical Synthesis

```

![h3](https://user-images.githubusercontent.com/20563301/120070726-95ca4800-c0a9-11eb-8af1-c891629f7d68.PNG)


![h2](https://user-images.githubusercontent.com/20563301/120070428-24d66080-c0a8-11eb-9bb6-a624c3a66b49.PNG)


![h1](https://user-images.githubusercontent.com/20563301/120070432-299b1480-c0a8-11eb-9d30-8c9c9e33dd97.PNG)


### 2) Flat Synthesis


Run the below command to flatten the synthesized design

```
$yosys
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib           

yosys> read_verilog multiple_modules.v                                                     

yosys> synth -top mutiple_modules                                                         

yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib                    

yosys> show multiple_modules : show the diagram of default multiple modules

flatten: command in Yosys is used to remove all the hierarchies between different submodules    

write_verilog -noattr multiple_modules_flat.v: dump the Verilog netlist file of multiple module and its submodule in its vcd format through Flatl Synthesis

!vim multiple_modules_flat.v : : show the text file of multiple modules in Flat Synthesis

show multiple_modules show the circuit diagram of multiple modules in Flat Synthesis 

```
![f1](https://user-images.githubusercontent.com/20563301/120070774-c3af8c80-c0a9-11eb-87bf-4a22eb4dd17a.PNG)


![f2](https://user-images.githubusercontent.com/20563301/120070778-c7431380-c0a9-11eb-9311-02811dfd934a.PNG)


The synthesized design looks as shown below if we run `show` command after flattening the design. Even the netlist will be flattened and this can be viewed by writing the netlist.


![f3](https://user-images.githubusercontent.com/20563301/120070786-cf9b4e80-c0a9-11eb-9656-c07e02b20c4a.PNG)



### 3) Submodule Level Synthesis

Submodule level synthesis comes handy in several cases, if same submodule is instantiated many times within the module then we can synthesize the submodule only once using the submodule level synthesis. Consider another case where the design is very large and produces non-optimal netlist when synthesis is performed at once, here we can synthesize the submodules seperatly to produce optimal netlist.

Submodule synthesis can be performed using yosys by following same steps mentioned in [above section](#yosys).
```
synth -top sub_module1 : synthesis your sub module instead of top module
```
Why it is needed? Reasons:-
1. when we have multiple instance of same module.
2. to reduce the load by executing one at a time for tool which is working for massive machine.

![subm2](https://user-images.githubusercontent.com/20563301/120070796-e17cf180-c0a9-11eb-803f-d043cb04174c.PNG)


![subm1](https://user-images.githubusercontent.com/20563301/120070803-e80b6900-c0a9-11eb-8a66-5adf25961fd4.PNG)



### Part 3 - Efficient Flip-flop coding styles and Optimizations

 #### Flip Flops
- The disadvantage of combinational circuit is they have glitch in output waveform which is created due proportional delay in gates.

- In order to avoid glitch we use flip flop which store the bit to make the output waveform stable for next combinational circuit.

- Reset/set help the flip flop to stop the bit. 

- There are Two Types of Reset/set in Flip Flop

1. Asynchronous Reset/Set: reset/set happen irrespective of clock.
2. Synchronous Reset/Set : reset/set happen along with clock.

- Some Commands used for Synthesis of Asynchronous Reset/Set and of Synchronous Reset/Set

```
$yosys

yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib           

yosys> read_verilog dff_asyncres.v                                                     

yosys> synth -top dff_asyncres                                                         

yosys> dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib: It command maps all the d flip flops in the design with the standard cell flip flops from liberty file.

yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib                    

yosys> show 

```
Outputs of Asynchronous reset through gtkwave and yosys Synthesis

![1](https://user-images.githubusercontent.com/20563301/120070903-6700a180-c0aa-11eb-9ade-20bbf6dd98fd.PNG)


![2](https://user-images.githubusercontent.com/20563301/120070911-6cf68280-c0aa-11eb-8dda-7f3b212b01ba.PNG)


![9](https://user-images.githubusercontent.com/20563301/120070921-7a137180-c0aa-11eb-80eb-e98e57e2eaf0.PNG)


Outputs of Synchronous reset through gtkwave and yosys Synthesis

![6](https://user-images.githubusercontent.com/20563301/120071119-5d2b6e00-c0ab-11eb-88cf-1409837dd20f.PNG)


![7](https://user-images.githubusercontent.com/20563301/120071123-63214f00-c0ab-11eb-90be-6b5452f9ca26.PNG)


![11](https://user-images.githubusercontent.com/20563301/120071133-6caab700-c0ab-11eb-91d2-8b3f4cb9d318.PNG)



• The schematic generated by Yosys for RTL of asynchronous reset d-flip flop shows a flop with a invertor.

• The schematic generated by Yosys for RTL of synchronous reset d-flip flop shows a flop with AND with inverted input.  

2) Special Cases: 
1.	Multiply by 2: If a 3 bit number (a) is multiply by 2 or power of 2 then output is{a,0xn} when n is bit number(2^n)

2.	Multiply by 9: If a 3 bit number(a) is multiply by 9 then output is aa 
```
•	write_verilog _noattr mult2_net.v: dump the Verilog netlist in yosys synthesizer.

•	!vim mult2.v: display the text file of output netlist file in yosys.
```
Outputs of mult2

![sc1](https://user-images.githubusercontent.com/20563301/120071197-c3b08c00-c0ab-11eb-89e0-7d36031de1bc.PNG)


![sc2](https://user-images.githubusercontent.com/20563301/120071205-cf9c4e00-c0ab-11eb-9e9d-1f2a8deb489c.PNG)


Outputs of mult8

![sc3](https://user-images.githubusercontent.com/20563301/120071227-eb075900-c0ab-11eb-9e19-8fb85420d95f.PNG)


![sc4](https://user-images.githubusercontent.com/20563301/120071229-efcc0d00-c0ab-11eb-9cbf-909a2b9a1df7.PNG)



## Day 3 Combinational and sequential optimization

### Introduction to optimizations


1) Combinational Optimization: It squeezing the combinational logic to most optimized design in order to save area and power savings.
	
i) There are 2 types of combinational optimization

(a) Constant Propogation: it is direct optimization technique which reduces series of gate to single to get constant propogation delay.

(b) Boolean Logic Optimisation: it reduces a complex Boolean expression into its simplified form with the help of K-map and quine Mckluskey.

2. Sequential Logic Optimisations
     a)Sequential Constant propagation: 
- It is direct optimization technique which happens in flop irrespective of set/reset it should give constant output value which led to constant propogation delay.
- If the output does not give constant value due to delay in set/reset and output led to regaining of flip flop.

b)Advanced :
-	State Optimisation : It is optimization of unused states.
-	Retiming: It is optimization technique which takes of portion of delay from higher delay to lower delay to increase the frequency operation which results in improving the performance of system.
-	Sequential Logic cloning: It is a optimization technique which involves cloning of flip flop which has large positive slack to reduce the router delay between flops.


### Combinational logic optimizations (Labs)

```
•	opt_clean –purge: It optimize and cleans the unused states in output file.
```
•	In order to optimize  multiple module we need to first flatten it then use op_clean –purge and synthesis the netlist file.


- Outputs of optimization of 2x1 MUX

![c3](https://user-images.githubusercontent.com/20563301/120071522-6f0e1080-c0ad-11eb-8706-97dedab6d5bc.PNG)


![c1](https://user-images.githubusercontent.com/20563301/120071527-759c8800-c0ad-11eb-9202-d429584ef604.PNG)


![c2](https://user-images.githubusercontent.com/20563301/120071534-7af9d280-c0ad-11eb-93a7-4c4c1db11962.PNG)


-Outputs of optimization of multiple modules in combinational circuit

![c4](https://user-images.githubusercontent.com/20563301/120071628-d6c45b80-c0ad-11eb-8aa1-ec5ea958ebb4.PNG)


![multiple module c5](https://user-images.githubusercontent.com/20563301/120071634-ddeb6980-c0ad-11eb-866c-94c11a2d0d32.PNG)


![c7](https://user-images.githubusercontent.com/20563301/120071639-e2178700-c0ad-11eb-88b1-895283f40914.PNG)


![c6](https://user-images.githubusercontent.com/20563301/120071644-e479e100-c0ad-11eb-8409-88e1cd2c327d.PNG)


### Sequential logic optimizations (Labs)
```
-gvim dff_const1.v –o dff_const2.v : open the text files of dff_cons1.v and dff_cons2.v simultaneously. 
```

- Outputs of optimization of dff_const1

![s4](https://user-images.githubusercontent.com/20563301/120071836-8d284080-c0ae-11eb-892a-84e1ecc605b5.PNG)


![s1](https://user-images.githubusercontent.com/20563301/120071848-9b765c80-c0ae-11eb-9680-6fa54348f5b8.PNG)


![s6](https://user-images.githubusercontent.com/20563301/120071858-aa5d0f00-c0ae-11eb-841a-761194c1b8d6.PNG)


- Outputs of optimization of dff_const3

![s3](https://user-images.githubusercontent.com/20563301/120071866-b8129480-c0ae-11eb-8a45-82fe35685d45.PNG)


![s2](https://user-images.githubusercontent.com/20563301/120071895-d082af00-c0ae-11eb-9bd1-4161f92ca15a.PNG)


![s5](https://user-images.githubusercontent.com/20563301/120071913-ded0cb00-c0ae-11eb-8cb6-22c80ef0b7b0.PNG)



### Sequential optimzations for unused outputs
```
cp counter_opt.v counter_opt2.v : It copy the date from counter_opt text file to another text file counter_opt2.
```

Consider a 3 bit up counter show in fig below

![un1](https://user-images.githubusercontent.com/20563301/120071320-5ea96600-c0ac-11eb-884f-2501a54fb151.PNG)

 
From the fig the output of counter should be 3 bit number but when you synthesis it it should only 1 bit number because the output q is assign to 0th bit of cout and hence the synthesis will remove unused output which are not needed by the primary output.

![un2](https://user-images.githubusercontent.com/20563301/120071330-6963fb00-c0ac-11eb-8c26-0bfe166b8447.PNG)

## Day 4 GLS blocking vs non blocking and Synthesis Simulation mismatch

### GLS, Synthesis-Simulation mismatch and Blocking/Non-blocking statements

1.	GLS(Gate Level Simulation) :

-	It is a simulator which run the testbench with netlist as Design Under Test. Since Netlist is the behavioral representation of RTL Design Code hence we can use the same Testbench.
-	GLS is needed because of two reasons:- 
1.	To validiate the functionality of my design (netlist) after synthesis.
2.	To ensure the timing of design is met which can be achieved by running with delay annotation.
-	GLS is used by iverilog it is same as default method except there is a another block called Gate Level Verilog Models along with Design and Testbench
-	We need to verify the functionality of netlist because of Synthesis Simulation Mismatch.

2.	Synthesis Simulation Mismatch 
-	It happens due to 3 Reasons:  
1)	Missing sensitivity List

![sy1](https://user-images.githubusercontent.com/20563301/120114157-7f041e00-c19b-11eb-9111-5967dbe706fa.PNG)
	

 
-	In the above figure always block contain only sel which function as a flop(takes previous values of both the input) in simulation and mux in synthesis.
-	This creates a Synthesis Simulation mismatch which avoids by using the sensitivity list(*) .
	

2)	Blocking vs Non-Blocking Assignments

a)	Blocking Assignments (==): It executes the statements in order it is written ie the first statement is executed before the second statement.
b)	Non-Blocking Assignments(<=) : It executes all the RHS when always block is entered and assign to LHS ie parallel evalution.


c)	Caveats with Blocking Statements: 
-	It occur when a blocking statement is used in sequential circuits.
-	It occur when a two blocking statement are not write in order.

3)	Non Standard Verilg Coding

### Labs on GLS and Synthesis-Simulation Mismatch

    Commands for GLS
 ```   
Simulation:
-	iverilog bad_mux.v tb_bad_mux.v
-	./a.out
-	gtkwave tb_bad_mux.vcd
```
Synthesis
```
-	yosys 
-	read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
-	read_verilog ternary_operator_mux.v
-	synth -top ternary_operator_mux
-	abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
-	show
-	write_verilog –noattr ternary_operator_mux_net.v
```
To get GLS output
```
-	iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v  ternary_operator_mux_net.v tb_ternary_operator_mux.v
-	./a.out
-	gtkwave  tb_ternary_operator_mux.vcd
```
1)	GLS output of ternary_operator_mux



![g1](https://user-images.githubusercontent.com/20563301/120114211-aeb32600-c19b-11eb-9423-3c7537bc00e2.PNG)



![g2](https://user-images.githubusercontent.com/20563301/120114214-b1ae1680-c19b-11eb-9507-79fabb18575f.PNG)



![g3](https://user-images.githubusercontent.com/20563301/120114217-b4a90700-c19b-11eb-81bc-34b6c8c4c620.PNG)




2)	Synthesis Simulation Mismatch Output


![sy2](https://user-images.githubusercontent.com/20563301/120114289-1c5f5200-c19c-11eb-95c9-999ad812e74a.PNG)



![sy3](https://user-images.githubusercontent.com/20563301/120114297-25e8ba00-c19c-11eb-9b73-d21829574c39.PNG)




![sy4](https://user-images.githubusercontent.com/20563301/120114301-2bde9b00-c19c-11eb-9308-7c38edb30889.PNG)




### Labs on synth-sim mismatch for blocking statement



![c1](https://user-images.githubusercontent.com/20563301/120114315-4284f200-c19c-11eb-94da-cec4d93f1203.PNG)



![c2](https://user-images.githubusercontent.com/20563301/120114319-46187900-c19c-11eb-8664-d6c0ffa62df6.PNG)



![c3](https://user-images.githubusercontent.com/20563301/120114322-4c0e5a00-c19c-11eb-9854-cdc3d1a1a1bb.PNG)



![c4](https://user-images.githubusercontent.com/20563301/120114328-50d30e00-c19c-11eb-8523-5b928c792202.PNG)




## Day 5 If, case, for loop and for generate

### If Case constructs

1.	If-else : Consider a if-else example
```
if <cond1>
   C1;
else if <cond2>
  C2:
else
  C3;
  ```
  
-	In the above statement the if cond1 will check and if it is true then it will executed and if it is false then it will go to else like this it will check every if condition and finally if all the conditions are false it will executed the final else statement.They are always used inside always block with req variable.

-There are some danger while writing if-else condition
	
a)If the if condition is written but there is no else condition then the circuit will have an inferred latch which will latch the previous state of output. Generally combinational circuit do not have inferred latches.
	
b)But in some case these inferred latches in if condition do not affect the logic flow. For Example consider a 3 bit Up counter

```	
always @(posedge clk , posedge reset)
     begin 
                if (reset) 
                         cout <=3’b000;
                else if (en)
                              cout <= cout +1;
      end
```

c)	In this counter the inferred latch will latch on to previous value instead of going to zero count.


2.	case statement  : Consider a case example below


![ca53](https://user-images.githubusercontent.com/20563301/120114734-2eda8b00-c19e-11eb-8644-54da039e5b8d.PNG)



-	In the above example the case statement will check whether the given condition is true of not and if it is true then it will execute further otherwise it will do end case.
-	It is always used inside always block with req variable.
	
-	There are some Caveats with case statements

a)	Incomplete case statement: 
	
![ca1](https://user-images.githubusercontent.com/20563301/120114751-431e8800-c19e-11eb-9d07-f679770b6bf3.PNG)

	
-	In the above example the case statement is only defined for 2’b00 and 2b’01 and hence the other combinational bit will form inferred latch.
-	This problem can be solved by writing the default case which will avoid the inferred latch.

b)	Partial assignments in case
	
	
	
![ca63](https://user-images.githubusercontent.com/20563301/120114780-59c4df00-c19e-11eb-9f91-9a6502047006.PNG)
	


-	In the above example the x statement is partially written and hence for the missing x statement it it will create a inferred latch in circuit.

c)	Overlapping case
- Compared with if-else the case statement do not check if the specific bit is present in it or not it just execute parallelly once the condition is true.
- Hence it create unpredictable outputs ie one or two output for a single input which creates a problem.
- THe example is shown below
	
![c73](https://user-images.githubusercontent.com/20563301/120114881-d6f05400-c19e-11eb-908f-a3b3a1ee87a8.PNG)

	


### Labs on "Incomplete If Case"
	
	
1. Outputs of incomp_if.v
	
![i1](https://user-images.githubusercontent.com/20563301/120114907-f9826d00-c19e-11eb-9a4b-5a978cfedfac.PNG)
	
	
![i2](https://user-images.githubusercontent.com/20563301/120114920-069f5c00-c19f-11eb-81e6-83b20a2742de.PNG)

	
![i3](https://user-images.githubusercontent.com/20563301/120114923-0acb7980-c19f-11eb-8819-3434c4a70284.PNG)

	
![i4](https://user-images.githubusercontent.com/20563301/120114929-0f902d80-c19f-11eb-99c1-a6a3e5998156.PNG)
	
2. Output of incomp_if2.v
	
	
![i21](https://user-images.githubusercontent.com/20563301/120115102-d4dac500-c19f-11eb-8440-94f794758c95.PNG)

	
	
![i22](https://user-images.githubusercontent.com/20563301/120115110-dc01d300-c19f-11eb-9288-560da8007d1c.PNG)
	
	
![i23](https://user-images.githubusercontent.com/20563301/120115114-e328e100-c19f-11eb-948f-167127965145.PNG)

	
	
![i24](https://user-images.githubusercontent.com/20563301/120115116-e623d180-c19f-11eb-9343-9ce63333b997.PNG)

	
	
### Labs on "Incomplete overlapping Case"

1. Outputs of Incomplete case
	
	
![ca2](https://user-images.githubusercontent.com/20563301/120115306-c93bce00-c1a0-11eb-9653-5c1940a233cd.PNG)
	
	
![ca3](https://user-images.githubusercontent.com/20563301/120115458-5a12a980-c1a1-11eb-91df-c7a29d8ca876.PNG)
	

![ca4](https://user-images.githubusercontent.com/20563301/120115462-61d24e00-c1a1-11eb-86b6-b4c8544ee4ca.PNG)
	
	
2. Outputs of Partial assignments in case
	
	
![ca61](https://user-images.githubusercontent.com/20563301/120115589-f9d03780-c1a1-11eb-9a4a-fcd01cf7dddf.PNG)

	
![ca62](https://user-images.githubusercontent.com/20563301/120115597-ff2d8200-c1a1-11eb-8d0e-1fd30110274c.PNG)	
	
	
3. Outputs of Overlapping case

	
![c74 gtk](https://user-images.githubusercontent.com/20563301/120115651-49166800-c1a2-11eb-82a2-1812774b0c46.PNG)


![c71](https://user-images.githubusercontent.com/20563301/120115665-59c6de00-c1a2-11eb-9ea0-aae0351c6e87.PNG)
	
	
![c72](https://user-images.githubusercontent.com/20563301/120115668-5df2fb80-c1a2-11eb-8953-aa0d2f52925f.PNG)
	
	
		
### for loop and for generate 

There are two types of Looping Constructs :
1. For loop
-	It is always used inside the always block.it is used to evalulate expression to create a simplified code.
-	Instead of using case statement for higher mux and demux, we used for loop to make the code simple to write.
	
	
	
![for1](https://user-images.githubusercontent.com/20563301/120115681-77944300-c1a2-11eb-883a-0cafd122b735.PNG)


	
-	In above example for loop is used to increment the selection line to get the 4x1 MUX.
	
	
	
![for4](https://user-images.githubusercontent.com/20563301/120115723-a6121e00-c1a2-11eb-8b81-49c4699203f9.PNG)



-	In the above example DEMUX 1x8 is created by using for

2. generate for loop 
-	It is used to replicate the hardware to make simulation easy
-	It is always used outside the always block.



![for7](https://user-images.githubusercontent.com/20563301/120115741-b924ee00-c1a2-11eb-9d24-4b711c9e0fe6.PNG)
	
	
	
-	In the above example instead of instantiating  8  full adder, we replicate the full adder 8 times to get RIPPLE Carry Adder.


### Labs on "for loop" and "for generate"
	
```
- se nu : to see the number of lines in code.	
```

1.Outputs of 4x1 MUX using for	
	
	
![for2](https://user-images.githubusercontent.com/20563301/120115790-02753d80-c1a3-11eb-892a-985857d61998.PNG)

	
	
![for3](https://user-images.githubusercontent.com/20563301/120115795-0903b500-c1a3-11eb-938e-371bd6a909ee.PNG)
	

2. Outputs of 1x8 DEMUX 
	
![for5](https://user-images.githubusercontent.com/20563301/120115969-7a436800-c1a3-11eb-8188-470513aa6f76.PNG)

	

![for6](https://user-images.githubusercontent.com/20563301/120115973-7f081c00-c1a3-11eb-981c-004035e9208c.PNG)
	
	
	
	
3.Outputs of Ripple Carry Adder
	
	
	
![for7](https://user-images.githubusercontent.com/20563301/120115990-90512880-c1a3-11eb-8627-f2884a6e9784.PNG)
	
	
	
### Acknowledgements

- Kunal Ghosh , Co-founder (VSD Corp. Pvt. Ltd)
- Shon Taware 
- Mohan Jindal , Founder of Chipspirit
- Mukesh Nadar

### References

- https://www.vlsisystemdesign.com/rtl-design-using-verilog-with-sky130-technology/?q=%2Frtl-design-using-verilog-with-sky130-technology%2F&v=a98eef2a3105
- https://github.com/kunalg123/vsdflow
- https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop
- https://gitlab.com/gab13c/openlane-workshop.git

 
	
	
	


 
   
