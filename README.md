# Advanced Physical Design using OpenLANE/Sky130 

## Application Specific Integrated Circuit (ASIC)

To develop an ASIC, We mainly require 3 components
- RTL IPs (github.com, opencores.org, librecores.org)
- PDK data (Qflow, OpenRoad, OpenLane)
- EDA Tools [google+skywater](github.com/google/skywater-pdk)

1. **```RTL```** : refers to a specific abstraction level in the field of digital design and electronics, where the behavior of a system is articulated in terms of registers, logic gates, and the interconnected flow of data between these elements
2. **```PDK```** :  short for Process Design Kit, comprises a set of resources such as files, libraries, and documentation offered by semiconductor foundries. These resources are intended to aid designers in the creation of integrated circuits (ICs) that align with a particular manufacturing process. PDKs encompass crucial information concerning design regulations, device models, and technology files essential for the design and simulation of ICs.
3. **```EDA```** : or Electronic Design Automation tools, represents a category of software applications employed for the development and evaluation of electronic systems, encompassing ICs and PCBs (Printed Circuit Boards). These tools play a pivotal role in facilitating the design and testing phases of electronic systems.

## RTL to GDS FLow 

- ```synthesis```
- ```Floorplanning```
- ```Powerplanning```
- ```Placement```
- ```Clock Tree Synthesis```
- ```Routing```
- ```Signoff```

## OpenLANE Design Stages

OpenLANE flow consists of several stages. By default all flow steps are run in sequence. Each stage may consist of multiple sub-stages. OpenLANE can also be run interactively as shown [here][25].

1. *Synthesis*
    1. `yosys` - Performs RTL synthesis
    2. `abc` - Performs technology mapping
    3. `OpenSTA` - Pefroms static timing analysis on the resulting netlist to generate timing reports
2. *Floorplan and PDN*
    1. `init_fp` - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
    2. `ioplacer` - Places the macro input and output ports
    3. `pdn` - Generates the power distribution network
    4. `tapcell` - Inserts welltap and decap cells in the floorplan
3. *Placement*
    1. `RePLace` - Performs global placement
    2. `Resizer` - Performs optional optimizations on the design
    3. `OpenPhySyn` - Performs timing optimizations on the design
    4. `OpenDP` - Perfroms detailed placement to legalize the globally placed components
4. *CTS*
    1. `TritonCTS` - Synthesizes the clock distribution network (the clock tree)
5. *Routing*
    1. `FastRoute` - Performs global routing to generate a guide file for the detailed router
    2. `CU-GR` - Another option for performing global routing.
    3. `TritonRoute` - Performs detailed routing
    4. `SPEF-Extractor` - Performs SPEF extraction
6. *GDSII Generation*
    1. `Magic` - Streams out the final GDSII layout file from the routed def
    2. `Klayout` - Streams out the final GDSII layout file from the routed def as a back-up
7. *Checks*
    1. `Magic` - Performs DRC Checks & Antenna Checks
    2. `Klayout` - Performs DRC Checks
    3. `Netgen` - Performs LVS Checks
    4. `CVC` - Performs Circuit Validity Checks

OpenLane can be operated at 2 different modes ie., Automated flow and Interactive mode.

## To enter the automated flow, use these commands
```
cd OpenLane
make mount
./ flow.tcl -design openlane/<DESIGN_NAME>  -tag <TAG>
```

## To enter the Interactive mode, use these commands 
```
cd OpenLane
make mount
./flow.tcl -interactive 
prep -design <path_to_your_design_folder> -tag <tag> -overwrite //overwrite is optional
```

# COURSE


<details>
<summary>DAY 1 : Inception of opensource-EDA, Opennlane and Skywater130</summary>
<br>

## Skywater-130 PDK

![day1 1](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/8eb5cd23-a9c2-4da4-84e6-8bea5e98fdf5)

The Skywater PDK files we are working with are described under $PDK_ROOT
1. Skywater-pdk – Contains all the foundry provided PDK related files
2. Open_pdks – Contains scripts that are used to bridge the gap between closed-source and open-source PDK to EDA tool compatibility
3. Sky130A – The open-source compatible PDK files

## Invoking OpenLane

![day1 2](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/a4d1de38-cd1e-4a3d-97e8-c987e9721cf4)

flow.tcl is the file that contains the script to run the designs

## Importing package

Different software dependencies are needed to run OpenLANE. To import these into the OpenLANE tool we need to run: ```package require openlane 0.9```

![day1 12](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/9587e861-10e5-4882-8ba4-709766f5df88)

## Designs presnt in openalne and Heirarchy in a Design

![day1 3](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/63f73b81-d15a-45d1-9b5e-daea5b2ea342)

- ```Src folder``` - Contains verilog files and sdc constraint files
- ```Config.tcl files``` - Design specific configuration switches used by OpenLANE

## Config file example content

![day1 4](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/1eafe914-cb4e-4d38-bf1f-e73f74a5512d)

## Prepare the design for the flow 

```prep -design <design_name> -tag <tag>```

![day1 11](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/ad112456-9253-4d07-beab-a53cc192ac7c)

Once the design prep stage is done, it creates a runs directory where all the results will be stored

![day1 6](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/60c60edd-60c1-4526-82b5-121b44fa3528)

## Synthesis

```run_synthesis```

![day1 7](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/79fe040a-1436-4f1e-8c62-0a8fb052051a)

****The main task to do at the beginning stage is to find the flop ration ie., (No. of D flip flops / Total number of cells)****

![day1 8](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/c976f9f8-6210-4521-b9cb-57104e7d7950)

![day1 9](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/1add254f-c995-40ea-93ef-78c7b05c3bc1)

![day1 10](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/a36cfb34-c430-45b0-8be8-a8bfeaa628d6)


</details>






<details>
<summary>DAY 2 : Good Floorplan vs Bad Floorplan and Introduction to library cells</summary>
<br>

## Chip Floorplanning Considerations

### 1. Define Width and height of core and die

- ```Die``` : Structure that consists of core which is a small semiconductor material on which the fundamental circuit is fabricated.
- ```core``` : Structire that contains primary logic and functional components.

Whenever we come across the concepts of core and die, ```Utilisation factor``` plays an important role.
UTILISATION FACTOR = Area Occupied by the Netlist / Area of the core (usually 50%-70%)
ASPECT RATIO = Height / Width (1 = square, others = rectangle)

### 2. Define Location of Pre-Placed cells

```pre-placed cells``` : memories, clock gating cells, comparator, mux etc

- The arrangement of these IPs on chip is called FLOORPLANNING
- These IPs have user defined locations and hence are placed in chip before automated placement and routing. Therefore called pre-placed cells.
- Automated PnR tool places the remaining logical cells in design onto chip.

### 3. De-coupling capacitors

_____Problem_____
We know that all the combinational blocks are connected to Vdd and Vss for their operation. But when there is a large circuit with many resistors, then The capacitors in the logic might not get fully charged as there occurs voltage deop due to wire metal and the resistors present along the path. So after voltage drop, if the voltage obtained by the logic is within noise margin, then it works well but what if it doesn't? 

_____Solution_____
We use De-Coupling capacitors (A huge capacitance with voltage equal to that of supply voltage) that is placed close to the combinational logic. When the switching activity takes place, it detatches the circuit from main supply and this capacitor acts as power supply.

The local communication has been successfully eshtablished with the solution mentioned above. The global communication is taken care by power planning.

### 4. Power Planning

- Power planning during the Floorplanning phase is essential to lower noise in digital circuits attributed to voltage droop and ground bounce. Coupling capacitance is formed between interconnect wires and the substrate which needs to be charged or discharged to represent either logic 1 or logic 0.
- When a transition occurs on a net, charge associated with coupling capacitors may be dumped to ground. If there are not enough ground taps charge will accumulate at the tap and the ground line will act like a large resistor, raising the ground voltage and lowering our noise margin. To bypass this problem a robust PDN with many power strap taps are needed to lower the resistance associated with the PDN.

### 5. Pin Placement

- ```Pin placement``` is an essential part of floorplanning to minimize buffering and improve power consumption and timing delays.
- We usually place input pins on the left and output pins on the right
- for primary inputs and outputs, pin size may be small and for clock, the pin size would be large because clock should drive many cells so we need to make sure that the resistance is less.
- larger the area, lesser the resistance.
- ```Placement blockage``` is done inorder to makesure that no logic is placed along the area where the pin placement is carried out.

## Floorplan

```run_floorplan```

Before running floorplan, lets look into the switches available for the floorplan stage

![day2 1](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/ad099daf-509b-45f5-aa49-68085f8aa8f4)

Changes made in the config.tcl for floorplan purpose:

![day2 2](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/b3c11bc8-46c7-42cc-86d6-84c4a5525d30)

Now in openlane, enter ```run_floorplan``` and the results will be updated at the runs folder

![day22](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/bb3e8af1-baea-49f7-8e07-556d41a21dad)

(0 0) in DIE AREA Indicates top-left corner co-ordinates and (660.685 671.405) indicates bottom-right corner of the die in micro-meters

To view the layout of the floorplan, use the command ```magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &```

- ```-T indicates``` techfile
- ```&``` is used to avoid the prompt that magic shows

![day2 3](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/167d29ea-0b29-4149-a00f-a07f0eb44dc7)

## Library Binding and Placement

### 1. Bind the netlist with physical cells

- ```Library``` consists of cells, sizes of cells, various flavours and shapes of the cells, Timing, Power and delay information.
- Now, we have the floorplan, netlist and representation of components of netlist in library
- place all the components such that the timing is not disturbed and distribute them properly. 


### 2. Optimize Placement

- Some components may be located very far to their inputs which can disturb signal integrity (as wire length increases, RC value increases). Therefore we use repeaters(may be series of buffers) inorder to avoid signal loss but area loss comes into picture.
- Assuming that all the clock signals are working at ideal rate, we do the timing analysis if the current placement works good.

### 3. Placement

```run_placement```

![day2 4](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/545ead72-6939-4762-ae1a-8992b7df5099)

## Cell Design Flow

Cell design is done in 3 parts:

1. **Inputs** - PDKs (Process design kits), DRC & LVS rules, SPICE models, library & user-defined specs.
2. **Design Steps** - Design steps of cell design involves Circuit Design, Layout Design, Characterization. The software GUNA used for characterization. The characterization can be classified as Timing characterization, Power characterization and Noise characterization.
3. **Outputs** - Outputs of the Design are CDL (Circuit Description Language), GDSII, LEF, extracted Spice netlist (.cir), timing, noise, power.libs, function.

### Standard cell Charachterization Flow

Standard Cell Libraries consist of cells with different functionality/drive strengths. These cells need to be characterized by liberty files to be used by synthesis tools to determine optimal circuit arrangement. The open-source software GUNA is used for characterization.
Characterization is a well-defined flow consisting of the following steps:

- Link Model File of CMOS containing property definitions
- Specify process corner(s) for the cell to be characterized
- Specify cell delay and slew thresholds percentages
- Specify timing and power tables
- Read the parasitic extracted netlist
- Apply input or stimulus
- Provide necessary simulation commands

### General Timing characterization parameters

#### Timing threshold definitions

- ```slew_low_rise_thr``` - 20% from bottom power supply when the signal is rising
- ```slew_high_rise_thr``` - 20% from top power supply when the signal is rising
- ```slew_low_fall_thr``` - 20% from bottom power supply when the signal is falling
- ```slew_high_fall_thr``` - 20% from top power supply when the signal is falling
- ```in_rise_thr``` - 50% point on the rising edge of input
- ```in_fall_thr``` - 50% point on the falling edge of input
- ```out_rise_thr``` - 50% point on the rising edge of ouput
- ```out_fall_thr``` - 50% point on the falling edge of ouput

These are the main parameters that we use to calculate factors such as propogation delay and transition time

- ```propogation delay ``` - time(out_*_thr) - time(in_*_thr)
- ```Transition time``` - time(slew_high_rise_thr) - time(slew_low_rise_thr)

</details>










<details>
<summary>DAY 3 :  Design library cell </summary>
<br>

## SPICE Deck creation for CMOS Inverter

SPICE deck contains the information of netlist such as:
- Connectivity Information
- Component values
- 'Nodes' identified
- 'Node' names
- 
### [CMOS_INVERTER.cir]()

```
*** MODEL DESCRIPTIONS ***
*** NETLIST DESCRIPTION ***
M1 out in vdd vdd pmos W=0.375u L=0.25u
M2 out in 0 0 nmos W=0.375u L=0.25u

cload out 0 10f

Vdd vdd 0 2.5
Vin in 0 2.5
*** SIMULATION Commands ***

.op
.dc Vin 0 2.5 0.05
*** include tsmc_025um_model.mod ***
.LIB "tsmc_025um_models.mod" CMOS_MODELS
.end
```

SPICE Simulation steps
```
cd <folder where the .cir file is present>
source CMOS_INVERTER.cir
run
setplot
dc1
display
plot out vs in
```

Observe the output. It should be symmetric ie., the threshold voltage should be at vdd/2 if it isnt, try to increase the PMOS width and run the simulation again. One of the important parameters tthat defines the **ROBUSTNESS** of the CMOS is ```Switching Threshold (Vm)``` @Vm : Vin = Vout

## Fabrication Process for a CMOS Inverter

Fabrication of CMOS Inverter is a 16-Mask process

### 1. Selecting the substrate 

- P-Type substrate with resistivity around (5-50 ohm) doping level (10^15 cm^-3) and orientation (100).
- Note that substrate doping should be less than well doping (used to fabricate NMOS and PMOS)

### 2. Create active resistance

This step creates pockets for NMOS and PMOS
1. Grow SiO2(~40nm) on Psub
2. deposit ~80nm Si3N4 on SiO2
3. deposit 1um layer of photoresist(used to define regions)
4. photolithography
5. etch out Si3N4 and SiO2 using a suitable solvent
6. Place the obtained structure in oxidartion furnace due to which field oxide is grown.This process is called ```LOCOS``` that is ```Local oxidation of silicon```
7. Etch out Si3N4 using hot phosphoric acid

### 3.NWel and PWel formation

- Apply photoresist, apply mask that covers NMOS
- Expose to UV, Wash, remove mask, appl boron(p-type) using Ion Implantation at an energy of 200Kev(for diffusion)
- repeat it for the other half using phosphorous @400Kev because phosphorous is heavier
- Wells have been created but the depth is low. Therefore subject it to high temperature furnace which increases the well depth.

### 4. Formation of Gate

- We repeat the step 3 but at low energy with p-type implant as boron @60Kev and n-type implant as Arsenic.
- Due to this The SiO2 is damaged as the dopants penetrate through it.
- Therefore original SiO2 is etched out using dilute HF solution and regrown to give high quality oxide(~10 nm thin)
- Finally for the gate to form, apply N-type ion implants for low gate resistance.
- Now mask on small width of Nwell and PWell above SiO2  and perform photolithography
- Gate Formation is Done

### 5. Lighlt Doped Drain Formation(LDD Formation)

- On the surface of SiO2 corresponding to NWell, apply photoresist, mask it, put phosphorous to make N-Implant on p-well(N-)
- Similarly do it for the other side using boron that forms (p-) implant
- This LDD has to be protected from further process
- so, Deposit 0.1um thick SiO2 on full structure and etch out using plasma anisotropic etching that results in formation of side wall spacers..

### 6. Source and Drain Formation

- Mask Nwell structure, deposit arsenic @75KeV that forms an N+ implant on Pwell
- use boron for P+ implant formation on Nwell
- Subject it to high temperature furnace that results in required thickness of N+,P+,N-,P- implants.

### 7. Steps to form contacts and interconnects

- Etch thin SiO2 oxide in HF solution
- Deposit Titanium of wafer surface using sputtering all over the structure
- Wafer heated at 600-700 degree in ambient N2 environment for 60 sec that reults in low resistance TiSi2 where the gate of both MOS is present.
- At the other places, TiN is formed that's used for local communication
- Etch off TiN on and half around gate structure of both MOS using RCA Cleaning

### 8. Higher level metal formation

- On the resulted structure, deposit a thick layer of (1um) SiO2 doped with P/B known as phosphoborosilicate glass
- To make the added surface plain, use CMP (Chemical Metal Polishing)
- For the creation of contact pins, proper holes with contacts have to be made
- This can be done using Al, W and TiN layer depositions.
- Deposit a layer of Si3N4 that acts as dielectric to protect the chip.

### 9. Final STructure

 ![image](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/0e355a75-55ff-4723-96ae-4abd5845697c)

## Inverter Layout using Magic

```
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag
```

![day3 1](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/6f339dd1-3e58-438d-95cf-55179dfceccc)


## Exploring the Layout displayed by MAGIC

Select the specific layer/device by hovering over the object and pressing, s, iteratively, until you traverse the hierarchy to the specified object:

![day3 2](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/f6cc8623-ae59-44ff-b081-d466e758235a)

- select a region from the layout, go to the console and type ```what``` to display the information of selected area
- To select a region, place ```cursor``` on that point and  press```s```. More the number of times you press ```s```, higher the abstraction selected.

![day3 3](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/c35cf985-d19e-4cd0-b5d2-98b6bc441b14)

refer to [inverter](https://github.com/nickson-jose/vsdstdcelldesign) to create layout for CMOS Inverter

### DRC Check

To check for DRC Errors, select a region (left click for starting point, right click at end point) and see the DRC column at the top that shows how many DRC errors are present.The Details of DRC Errors will be printed on the console.

![day3 2(1)](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/012b09cb-2ec0-4599-8e2a-25aae50ff984)

For more information on DRC errors plase refer to: [DRC_Erros](https://skywater-pdk--136.org.readthedocs.build/en/136/)
For more information on how to fix these DRC errors using Magic please refer to: [fix_DRC](http://opencircuitdesign.com/magic/)


## Extracting PEX to SPICE with MAGIC

Select Full inverter layout. Then

![day3 4](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/cee17ded-023b-4f0e-aaa2-56888e04fdfa)

![day3 6](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/ddbb4358-197f-4d37-b891-628d244ace03)

The above file has details of inverter netlist but the sources and their values are not specified. So we have to modify the file.

- Grid size from the layout is 0.01u
- specify the library for MOS
- create VDD, VSS, Input pulse Va
- specify the type of analysis to be done

### Grid Size

![day3 7](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/6582716b-e0d4-418d-99df-b1dde40af3ce)

## Modified Spice netlist

![dd](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/1cdae9c9-cfec-4b4a-a257-20aad102a294)

To run the spice netlist, run ```ngspice sky130_inv.spice``` and ```plot y vs time a```

![day3 8](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/fc612751-df6f-4194-b18b-cb7c90e31286)

The results obtained from the graph are :
- Rise Transition : 0.0395ns
- Fall transition : 0.0282ns
- Cell Rise delay : 0.03598ns
- Cell fall delay : 0.0483ns

</details>







<details>
<summary>DAY 4 : Pre-Layout timing analysis and importance of good clock tree</summary>
<br>

## Extraction of LEF 

Place and routing (PnR) is performed using an abstract view of the GDS files generated by Magic. The abstract information will include metal and pin information. The PnR tool will use the abstract view information, formally defined as LEF information, to perform interconnect routing in conjunction to routing guides generated from the PnR flow.

- Technology LEF - Contains layer information, via information, and restricted DRC rules
- Cell LEF - Abstract information of standard cells

From PnR POV, We have to follow certain guidelines to get standard cell set
1. Input and output ports must lie on the intersection of vertical and horizontal tracks
2. Width of the standard cell should be odd multiples of the track pitch and height should be odd multiple of vertical track pitch


Track info can be found at :

``` ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130fd_sc_hd/tracks.info```

![day4 1](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/68379f83-205d-4b7c-863b-ad2830e3299b)

- 1st value indicates the offset and 2nd value indicates the pitch along provided direction

### Setting grid values using above file info

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/7ad47a01-0c73-4ad5-b735-827fa55057a5)

Layout before setting grid info vs after setting grid info

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/5a59eeb9-8053-48c9-8316-55bfe1655e62)
![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/de619090-164f-4171-8890-565c424f9b71)
![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/374897b8-9f17-44d4-bcec-8679cae412a7)

- From the above pic, its confirmed that the pins A and Y are at the intersection of X and Y tracks. So the first condition is met.
- The PR boundary is taking 3 grids on width and 9 grids on height which says that the 2nd condition is also met

## LEF Generation

Since the layout is perfect, we can generate the lef file

#### 1. save the modified layout (with new grid)
   - In console, type ```save sky130_vsdinv.mag```
   - This saves the modified layout in current working directory

#### 2. Open the file and extract LEF
   - Open using ``` magic -T sky130A.tch sky130_vsdinv.mag```
   - in the console opened, type ```lef write``` and a lef file will be generated

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/076337b8-cccb-451c-830a-58d6173c2539)

#### 3. Plug the generated lef file into PICORV32a

To do this, we need the lef file, library file that has cells
![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/7b5d1322-26f8-4e8f-b477-c14c00a9e974)

Change config file so that these libraries and lef file is used

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/9d4f140c-fc8a-47d3-82fb-82fe4e082019)


#### 4. Make sure the lef file is added

add the below 2 lines in the initial stage of interactive flow and ```run_synthesis```  to see if our inverter has been used and find timing violations if any.

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/1b22f494-b45a-4a97-9825-c4939ffebe72)

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/1a2e9d87-8546-4311-bd39-20c288cf8c51)

The above figure shows that our vsdinv cell has been used in synthesis process

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/e328e642-efa3-450c-8841-49463457cbd3)

since there is slack, we have to reduce it

VLSI engineers will obtain system specifications in the architecture design phase. These specifications will determine a required frequency of operation. To analyze a circuit's timing performance designers will use static timing analysis tools (STA). When referring to pre clock tree synthesis STA analysis we are mainly concerned with setup timing in regards to a launch clock. STA will report problems such as worst negative slack (WNS) and total negative slack (TNS). These refer to the worst path delay and total path delay in regards to our setup timing restraint. Fixing slack violations can be debugged through performing STA analysis with OpenSTA, which is integrated in the OpenLANE tool. To describe these constraints to tools such as In order to ensure correct operation of these tools two steps must be taken:

- Design configuration files (.conf) - Tool configuration files for the specified design
- Design Synopsys design constraint (.sdc) files - Industry standard constraints file

For the design to be complete, the worst negative slack needs to be above or equal to 0. If the slack is outside of this range we can do one of multiple things:

1. Review our synthesis strategy in OpenLANE
    - Enalbed CELL_SIZING
    - Enabled SYNTH_STRATEGY with parameter as "DELAY 1"
    - The synthesis result is :
  
![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/1777843a-5819-4519-9d75-0b524a5962a3)

    The slack has reduced a lot but still didnt meet the requirement. The sdc file used is [my_base.sdc](https://github.com/yagnavivek/PES_OpenLane_PD/blob/main/my_base.sdc) defined in ![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/b1f0a7f9-5286-424d-b5d8-12d1d5944578)
 using the command ```sta pre_sta.conf```

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/abc84a21-bb24-4c7f-966c-a6407f5cebd5)

    The delay is high when the fanout is high. Therefore we can re-run synthesis by changing the value of ```SYNTH_MAX_FANOUT``` variable
    
2. Enable cell buffering 
3. Perform manual cell replacement on our WNS path with the OpenSTA tool

    - We can see which net is driving most outputs and replace the driver cell with larger form of its own kind

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/11d6e569-12d0-43b2-959c-0f29afaf9675)

4. Optimize the fanout value with OpenLANE tool

Since we have synthesised the core using our vsdinv cell too and as it got successfully synthesized, it should be visible in layout after ```run_placement``` stage which is followed after ```run_floorplan``` stage

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/a15da9cd-ab4c-4684-9525-770d1f9a66ab)

## Clock Tree Synthesis

- After all the above steps of fixing slack violations, as we have ```run_synthesis``` in openlane, it would have generated a mapped.v file in synthesis results but we have fixed all the violations using ```pre_sta.conf```. Therefore we write this netlist using ```write_verilog``` and replace the openlane generated mapped file ie., ```picorv32a.synthesis.v```

- now in the openlane flow, continue with ```run_flooorplan``` ```run_placement``` ```run_cts```

## Post CTS- STA Analysis

OpenLANE has the OpenROAD application integrated into its flow. The OpenROAD application has OpenSTA integrated into its flow. Therefore, we can perform STA analysis from within OpenLANE by invoking OpenROAD.

In OpenROAD the timing analysis is done by creating a .db database file. This database file is created from the post-cts LEF and DEF files. To generate the .db files within OpenROAD:
- Invoke OpenRoad
- Read lef file from tmp folder of runs
- Read def file from results of cts
- write db file
- Read the generated db file
- Read the cts generated verilog file
- read min and max liberty file
- set the clocks
- generate the reports

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/f85796c2-8bcd-45e0-b282-12c822fec9a8)
![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/255e0954-cac6-4a1a-a1cf-bb1f11870bf2)
![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/396b017d-2f3c-4b18-8b6d-1e635a717d1f)

The results wont meet the timing because we are using min and max lib files and openroad doesnot support multi corner optimisation. Therefore we do it using only typical corner lib

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/db042727-e91c-40cd-8d0e-a564eb6ee502)

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/fb644353-726b-49f2-93a6-8eb8502a8c33)

We have to ensure that the skew is withing 10% of clock period ie., should be less than 1.6 in my case

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/749959ff-b4ec-4191-a9bc-c448ec6e745c)

</details>







<details>
<summary>DAY 5 : Final steps for RTL2GDSII</summary>
<br>

## Power Distribution Network

After generating our clock tree network and verifying post routing STA checks we are ready to generate the power distribution network ```gen_pdn``` in OpenLANE:

The PDN feature within OpenLANE will create:

- Power ring global to the entire core
- Power halo local to any preplaced cells
- Power straps to bring power into the center of the chip
- Power rails for the standard cells

![image](https://github.com/Shashanksharma280201/PES_OpenLane_PD/assets/79470436/f6c1cc9c-5fa9-4366-b61c-169010ceaf25)

Note: The pitch of the metal 1 power rails defines the height of the standard cells

## Global and Detailed Routing

OpenLANE uses TritonRoute as the routing engine ```run_routing``` for physical implementations of designs. Routing consists of two stages:

- Global Routing - Routing guides are generated for interconnects on our netlist defining what layers, and where on the chip each of the nets will be reputed
- Detailed Routing - Metal traces are iteratively laid across the routing guides to physically implement the routing guides

If DRC errors persist after routing the user has two options:

- Re-run routing with higher QoR settings
- Manually fix DRC errors specific in tritonRoute.drc file

## SPEF Extraction

After routing has been completed interconnect parasitics can be extracted to perform sign-off post-route STA analysis. The parasitics are extracted into a SPEF file. The SPEF extractor is not included within OpenLANE as of now.

```
cd ~/Desktop/work/tools/SPEFEXTRACTOR
python3 main.py <path to merged.lef in tmp> <path to def in routing>
```

The SPEF File will be generated in the location where def file is present
</details>
