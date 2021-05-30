# Open Source RTL Design using Sky130 Technolgy
![1](https://user-images.githubusercontent.com/20563301/120014244-74ad1d00-bfff-11eb-9d53-8df2784ecb07.PNG)

This repository contains the usage of tools like iverilog, gtkwave and yosys for open-source RTL Design. The content is documentation of tasks carried out during VSD "RTL Design Using Verilog With SKY130 Technology".

SKY130 is the hardware industry's first open-source process design kit (PDK) released by SkyWater Technology Foundry in collaboration with Google giving all hardware design experts and aficionados, a worldwide access to their IP functions and open source ASICs. More details [here.](https://github.com/google/skywater-pdk).

This particular workshop covers the various aspects of design in Verilog HDL both theoretically and practically with labs using open-source softwares through their VSD-IAT portal. Beginning with an introduction to digital design using Verilog HDL and various libraby, the instructors cover digital design steps that include design, functional simulation, test bench based validation of the design functionality and logic Synthesis with optimization. Further, we learn about efficient verilog coding styles that result in a predictable logic in Silicon.

## Table of Contents

- [Day 1 Introduction to Verilog RTL Design and Synthesis](#Day-1-Introduction-to-Verilog-RTL-Design-and-Synthesis)
  * [Part 1 - Setup the lab instance with libraries and verilog files](#Part 1 - Setup the lab instance with libraries and verilog files)
  * [gtkwave](#gtkwave)
  * [sky130 libraries](#sky130-libraries)
  * [yosys](#yosys)
- [Day 2 Timing libs Hierarical vs Flat Synthesis and efficient Flop Coding Styles](#Day-2-Timing-libs-Hierarical-vs-Flat-Synthesis-and-efficient-Flop-Coding-Styles)
  * [Sensitivity List for Combinational Logic](#Sensitivity-List-for-Combinational-Logic)
  * [Modelling Flip Flops](#Modelling-Flip-Flops)
- [Day 3 Combinational and sequential optimization](#Day-3-Combinational-and-sequential-optimization)  
  * [Hierarchial Synthesis](#Hierarchial-Synthesis)
  * [Flat Synthesis](#Flat-Synthesis)
  * [Submodule Level Synthesis](#Submodule-Level-Synthesis)
- [Day 4 GLS blocking vs non blocking and Synthesis Simulation mismatch](#Day-4-GLS-,blocking-vs-non-blocking-and-Synthesis-Simulation-mismatch)
  * [Synthesizing Multipliers](#Synthesizing-Multipliers)
- [Day 5 If, case, for loop and for generate](#Day-5-If-case-for-loop-and-for-generate)

## Day 1 Introduction to Verilog RTL Design and Synthesis

Usually front-end RTL design is carried out using huge EDA Tools from different vendors. Even though many of the tools come with free limited versions its better to give a try for development using open-source tools. Below is the list of tools used by this repo.

RTL Design - It consists of an actual verilog code / a set of verilog codes that have the functionality to meet the required design specifications of the circuit Testbench - It is a setup that one uses to apply a set of  stimuli (test-case vector) to check the functionalility of the design file.

The simulator is loaded with the design and its respective testbench file after which it looks for changes in the input signals and depending on the change, the output is evaluated. These changes in input and corresponding output values are dumped in a special format file called "value change dump" (.vcd) file. This file can be pictorially represented in waveforms using a waveform tool like gtkwave.


### Part 1 - Setup the lab instance with libraries and verilog files

Firstly, we have to clone 2 separate repositories namely vsdflow and sky130RTLDesignAndSynthesisWorkshop which contain the required library files and verilog design files to perform the simulations and logic synthesis parts of the workshop. It can be done using basic linux command gitclone filelink

```
git clone https://github.com/kunalg123/vsdflow.git. 
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```

We are given a default set of files and libraries shown below to work on using the practical lab instance

![3](https://user-images.githubusercontent.com/20563301/120075523-45f67b80-c0bf-11eb-8138-daef35db54c7.PNG)



![4](https://user-images.githubusercontent.com/20563301/120075534-50b11080-c0bf-11eb-89cb-07f3af1dd16d.PNG)



### Part 2 -  Labs using iverilog and gtkwave

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


### Part 3 -Introduction to Yosys and Logic synthesis

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



### Part 4 -Labs using Yosys and Sky130 for 2:1 Multiplex

-We will perform a synthesis example of the 2:1 Multiplexer RTL design using YOSYS with appropriate library files from SKY130 technology that we cloned into the directory.
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

### Part 1 - Introduction to timing .libs

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
### Part 2 - Hierarchical vs Flat Synthesis

Consider a simple example `multiple_modules` which instntiates two modules `sub_module1` and `sub_module2`. The sub-modules realize and logic and or logic respectively.

### Hierarchial Synthesis
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


### Flat Synthesis


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



### Submodule Level Synthesis

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

 1) Flip Flops
- The disadvantage of combinational circuit is they have glitch in output waveform which is created due proportional delay in gates.

- In order to avoid glitch we use flip flop which store the bit to make the output waveform stable for next combinational circuit.

- Reset/set help the flip flop to stop the bit. 

- There are Two Types of Reset/set in Flip Flop
1.Asynchronous Reset/Set: reset/set happen irrespective of clock.
2.Synchronous Reset/Set : reset/set happen along with clock.

-Some Commands used for Synthesis of Asynchronous Reset/Set and of Synchronous Reset/Set

```
$yosys

yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib           

yosys> read_verilog dff_asyncres.v                                                     

yosys> synth -top dff_asyncres                                                         

yosys> dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib: It command maps all the d flip flops in the design with the standard cell flip flops from liberty file.

yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib                    

yosys> show 

```
Output of Asynchronous reset through gtkwave and yosys Synthesis

![1](https://user-images.githubusercontent.com/20563301/120070903-6700a180-c0aa-11eb-9ade-20bbf6dd98fd.PNG)


![2](https://user-images.githubusercontent.com/20563301/120070911-6cf68280-c0aa-11eb-8dda-7f3b212b01ba.PNG)


![9](https://user-images.githubusercontent.com/20563301/120070921-7a137180-c0aa-11eb-80eb-e98e57e2eaf0.PNG)


Output of Synchronous reset through gtkwave and yosys Synthesis

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
Output of mult2

![sc1](https://user-images.githubusercontent.com/20563301/120071197-c3b08c00-c0ab-11eb-89e0-7d36031de1bc.PNG)


![sc2](https://user-images.githubusercontent.com/20563301/120071205-cf9c4e00-c0ab-11eb-9e9d-1f2a8deb489c.PNG)


Output of mult8

![sc3](https://user-images.githubusercontent.com/20563301/120071227-eb075900-c0ab-11eb-9e19-8fb85420d95f.PNG)


![sc4](https://user-images.githubusercontent.com/20563301/120071229-efcc0d00-c0ab-11eb-9cbf-909a2b9a1df7.PNG)



## Day 3 Combinational and sequential optimization

### Part 1 - Introduction to optimizations


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


### Part 2 - Combinational logic optimizations (Labs)

```
•	opt_clean –purge: It optimize and cleans the unused states in output file.
```
•	In order to optimize  multiple module we need to first flatten it then use op_clean –purge and synthesis the netlist file.


- Output of optimization of 2x1 MUX

![c3](https://user-images.githubusercontent.com/20563301/120071522-6f0e1080-c0ad-11eb-8706-97dedab6d5bc.PNG)


![c1](https://user-images.githubusercontent.com/20563301/120071527-759c8800-c0ad-11eb-9202-d429584ef604.PNG)


![c2](https://user-images.githubusercontent.com/20563301/120071534-7af9d280-c0ad-11eb-93a7-4c4c1db11962.PNG)


-Output of optimization of multiple modules in combinational circuit

![c4](https://user-images.githubusercontent.com/20563301/120071628-d6c45b80-c0ad-11eb-8aa1-ec5ea958ebb4.PNG)


![multiple module c5](https://user-images.githubusercontent.com/20563301/120071634-ddeb6980-c0ad-11eb-866c-94c11a2d0d32.PNG)


![c7](https://user-images.githubusercontent.com/20563301/120071639-e2178700-c0ad-11eb-88b1-895283f40914.PNG)


![c6](https://user-images.githubusercontent.com/20563301/120071644-e479e100-c0ad-11eb-8409-88e1cd2c327d.PNG)


### Part 3 - Sequential logic optimizations (Labs)
```
-gvim dff_const1.v –o dff_const2.v : open the text files of dff_cons1.v and dff_cons2.v simultaneously. 
```

- Output of optimization of dff_const1

![s4](https://user-images.githubusercontent.com/20563301/120071836-8d284080-c0ae-11eb-892a-84e1ecc605b5.PNG)


![s1](https://user-images.githubusercontent.com/20563301/120071848-9b765c80-c0ae-11eb-9680-6fa54348f5b8.PNG)


![s6](https://user-images.githubusercontent.com/20563301/120071858-aa5d0f00-c0ae-11eb-841a-761194c1b8d6.PNG)


- Output of optimization of dff_const3

![s3](https://user-images.githubusercontent.com/20563301/120071866-b8129480-c0ae-11eb-8a45-82fe35685d45.PNG)


![s2](https://user-images.githubusercontent.com/20563301/120071895-d082af00-c0ae-11eb-9bd1-4161f92ca15a.PNG)


![s5](https://user-images.githubusercontent.com/20563301/120071913-ded0cb00-c0ae-11eb-8cb6-22c80ef0b7b0.PNG)



### Part 4 Sequential optimzations for unused outputs
```
cp counter_opt.v counter_opt2.v : It copy the date from counter_opt text file to another text file counter_opt2.
```

Consider a 3 bit up counter show in fig below

![un1](https://user-images.githubusercontent.com/20563301/120071320-5ea96600-c0ac-11eb-884f-2501a54fb151.PNG)

 
From the fig the output of counter should be 3 bit number but when you synthesis it it should only 1 bit number because the output q is assign to 0th bit of cout and hence the synthesis will remove unused output which are not needed by the primary output.

![un2](https://user-images.githubusercontent.com/20563301/120071330-6963fb00-c0ac-11eb-8c26-0bfe166b8447.PNG)

## Day 4 GLS blocking vs non blocking and Synthesis Simulation mismatch

 



