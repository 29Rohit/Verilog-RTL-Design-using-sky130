# Open Source RTL Design using Sky130 Technolgy
![1](https://user-images.githubusercontent.com/20563301/120014244-74ad1d00-bfff-11eb-9d53-8df2784ecb07.PNG)

This repository contains the usage of tools like iverilog, gtkwave and yosys for open-source RTL Design. The content is documentation of tasks carried out during VSD "RTL Design Using Verilog With SKY130 Technology".

SKY130 is the hardware industry's first open-source process design kit (PDK) released by SkyWater Technology Foundry in collaboration with Google giving all hardware design experts and aficionados, a worldwide access to their IP functions and open source ASICs. More details [here.](https://github.com/google/skywater-pdk).

This particular workshop covers the various aspects of design in Verilog HDL both theoretically and practically with labs using open-source softwares through their VSD-IAT portal. Beginning with an introduction to digital design using Verilog HDL and various libraby, the instructors cover digital design steps that include design, functional simulation, test bench based validation of the design functionality and logic Synthesis with optimization. Further, we learn about efficient verilog coding styles that result in a predictable logic in Silicon.
## Table of Contents

- [Day 1 - Introduction to Verilog RTL Design and Synthesis](#Day 1 - Introduction to Verilog RTL Design and Synthesis)
  * [iverilog](#iverilog)
  * [gtkwave](#gtkwave)
  * [sky130 libraries](#sky130-libraries)
  * [yosys](#yosys)
- [Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles](#Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles)
  * [Sensitivity List for Combinational Logic](#Sensitivity-List-for-Combinational-Logic)
  * [Modelling Flip Flops](#Modelling-Flip-Flops)
- [Synthesis Techniques](#Synthesis-Techniques)
  * [Hierarchial Synthesis](#Hierarchial-Synthesis)
  * [Flat Synthesis](#Flat-Synthesis)
  * [Submodule Level Synthesis](#Submodule-Level-Synthesis)
- [Beauty of Optimizations](#Beauty-of-Optimizations)
  * [Synthesizing Multipliers](#Synthesizing-Multipliers)
## Day 1 - Introduction to Verilog RTL Design and Synthesis

Usually front-end RTL design is carried out using huge EDA Tools from different vendors. Even though many of the tools come with free limited versions its better to give a try for development using open-source tools. Below is the list of tools used by this repo.

### Part 1 - Setup the lab instance with libraries and verilog files
### 1) iverilog

iverilog is the tool used for compiling and simulation purpose. Simulation is the process of creating models that mimic the behavior of the device. Tool performs macro preprocessing, compilation, elaboration, optional optimizations and finally code generation. For more details about the tool refer the [wiki](https://iverilog.fandom.com/wiki/User_Guide).

* Use following command to install iverilog on Ubuntu machine

```
sudo apt install iverilog
```

* Compile the design files and associated test bench using following command, it produces a executable

```
iverilog design_file.v test_bench.v
```

* Execute the a.out file to produce vcd file

```
./a.out
```

### Part 2 -Labs using iverilog and gtkwave
2) gtkwave

The simulator generates value change dump (.vcd) file this can be viewed as wave using gtkwave waveform viewer.

* Use following command to install gtkwave on Ubuntu machine

```
sudo apt install gtkwave
```

* Run following command to view the waveform

```
gtkwave test_bench.vcd
```

### Part 3 -Introduction to Yosys and Logic synthesis
3) sky130 libraries

sky130 pdk library is used for mapping the purpose during synthesis. The design gets mapped to standard cell definitions present in "sky130_fd_sc_hd__tt_025C_1v80.lib" library. The necessary files for synthesis are well organized in the [git repository](https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git). For further details about the library refer [wiki](https://skywater-pdk.readthedocs.io/en/latest/contents.html).

* Clone the repository using following command

```
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```


yosys

yosys is used for synthesis. Synthesizer takes the design file and technology library file as input and produces netlist. Syntesis is the process in which the design is mapped to standard cells defined in the technology library. Here we are using SKY130 Technology library file. For further details about the tool refer the [documentation](http://www.clifford.at/yosys/documentation.html).

* Use following command to install yosys on Ubuntu machine

```
sudo apt install yosys
```

* Invoke yosys by running following command

```
yosys
```

* Read library using following command

```
read_liberty -lib my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

* Read design files using following command

```
read_verilog ../design_file.v
```

* Synthesize the design module

```
synth -top design_file
```

* Generate the netlist

```
abc -liberty my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
* View the realized logic

```
show
```
### Part 4 -Labs using Yosys and Sky130 for 2:1 Multiplex

To properly model any combinational logic in verilog the sensitivity list must contain all inputs, else the design will not function as expected. To demonstrate this concept consider a simple multiplexer as example. Below figure shows good and bad modelling style along with the simulation waveform. Left side of the figure has proper verilog code for the mux. The code shown in right side has only select input in sensitivity list hence the output will not change if select input is constant and inputs are changing.

## Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

### Part 1 - Introduction to timing .libs
```
vim ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib : shows the text file of sky130_fd_sc_hd__tt_-025C_1v80.lib
```
Above show the text file of sky130_fd_sc_hd__tt_-025C_1v80.lib
We have seen that a .lib file is a collection of different flavours of standard cells with nets. In this workshop, we use the **sky130_fd_sc_hd_tt_025C_1v80.lib**. Looking in depth into the naming of this lib file, it denotes the following:

fd --> Foundry

sd --> Standard Cell

hd --> High Density

tt --> Typical Process

025C --> Temperature 

1v80 --> Voltage 
Above paraments determine how fast/slow silicon works depending upon its respective variations. It contain various different flavor of gates ,its power leakage,size etc. Area and Power of gate is inversely proportional to delay.
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

The synthesized design looks as shown below if we run `show` command after flattening the design. Even the netlist will be flattened and this can be viewed by writing the netlist.

![Flat Synthesis](images/synth_flat.png)

### Submodule Level Synthesis

Submodule level synthesis comes handy in several cases, if same submodule is instantiated many times within the module then we can synthesize the submodule only once using the submodule level synthesis. Consider another case where the design is very large and produces non-optimal netlist when synthesis is performed at once, here we can synthesize the submodules seperatly to produce optimal netlist.

Submodule synthesis can be performed using yosys by following same steps mentioned in [above section](#yosys).
```
synth -top sub_module1 : synthesis your sub module instead of top module
```
Why it is needed? Reasons:-
1. when we have multiple instance of same module.
2. to reduce the load by executing one at a time for tool which is working for massive machine.


### Part 3 - Efficient Flip-flop coding styles and Optimizations

 ###  Flip Flops
-	The disadvantage of combinational circuit is they have glitch in output waveform which is created due proportional delay in gates.
-	In order to avoid glitch we use flip flop which store the bit to make the output waveform stable for next combinational circuit.
-	Reset/set help the flip flop to stop the bit. 

There are Two Types of Reset/set in Flip Flop
1.	Asynchronous Reset/Set: reset/set happen irrespective of clock.
2.	 Synchronous Reset/Set : reset/set happen along with clock.

Some Codes of Asynchronous Reset/Set and of Synchronous Reset/Set

```
$yosys

yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib           

yosys> read_verilog dff_asyncres.v                                                     

yosys> synth -top dff_asyncres                                                         

yosys> dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib: It command maps all the d flip flops in the design with the standard cell flip flops from liberty file.

yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib                    

yosys> show 

```

•	The schematic generated by Yosys for RTL of asynchronous reset d-flip flop shows a flop with a invertor.

•	The schematic generated by Yosys for RTL of synchronous reset d-flip flop shows a flop with AND with inverted input.  

Special Cases: 
1.	Multiply by 2: If a 3 bit number (a) is multiply by 2 or power of 2 then output is{a,0xn} when n is bit number(2^n)
2.	Multiply by 9: If a 3 bit number(a) is multiply by 9 then output is aa 
```
•	write_verilog _noattr mult2_net.v: dump the Verilog netlist in yosys synthesis.
•	!vim mult2.v: display the text file of output file in yosys.
```
## Day 3 - Combinational and sequential optimizations

### Part 1 - Introduction to optimizations


1)	Combinational Optimization: It squeezing the combinational logic to most optimized design in order to save area and power savings.
	
i)	There are 2 types of combinational optimization

(a)	Constant Propogation: it is direct optimization technique which reduces series of gate to single to get constant propogation delay.

(b)	Boolean Logic Optimisation: it reduces a complex Boolean expression into its simplified form with the help of K-map and quine Mckluskey.

2)	Sequential Logic Optimisations
a)	Sequential Constant propagation: 
•	It is direct optimization technique which happens in flop irrespective of set/reset it should give constant output value which led to constant propogation delay.
•	If the output does not give constant value due to delay in set/reset and output led to regaining of flip flop.

b)	Advanced :
•	State Optimisation : It is optimization of unused states.
•	Retiming: It is optimization technique which takes of portion of delay from higher delay to lower delay to increase the frequency operation which results in improving the performance of system.
•	Sequential Logic cloning: It is a optimization technique which involves cloning of flip flop which has large positive slack to reduce the router delay between flops.


### Part 2 - Combinational logic optimizations (Labs)
```
•	opt_clean –purge: It optimize and cleans the unused states in output file.
•	In order to optimize  multiple module we need to first flatten it then use op_clean –purge and synthesis the netlist file.
```
### Part 3 - Sequential logic optimizations (Labs)
```
•	gvim dff_const1.v –o dff_const2.v : open the text file of dff_cons1.v and dff_cons2.v simultaneously. 
```
### Part 4 - Sequential logic optimizations (Labs)




