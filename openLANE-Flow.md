**Physical design**

Physical design means --->> netlist (.v ) converted into GDSII form(layout form)
logical connectivity of cells converted into physical connectivity.
During physical design, all design components are instantiated with their geometric representations. In other words, all macros, cells, gates, transistors, etc., with fixed shapes and sizes per fabrication layer, are assigned spatial locations (placement) and have appropriate routing connections (routing) completed in metal layers.

Physical design directly impacts circuit performance, area, reliability, power, and manufacturing yield. Examples of these impacts are discussed below.
Performance: long routes have significantly longer signal delays.
* Area: placing connected modules far apart results in larger and slower chips.
* Reliability: A large number of vias can significantly reduce the reliability of the circuit.
* Power: transistors with smaller gate lengths achieve greater switching speeds at the cost of higher leakage current and manufacturing variability; larger transistors and longer wires result in greater dynamic power dissipation.
* Yield: wires routed too close together may decrease yield due to electrical shorts occurring during manufacturing, but spreading gates too far apart may also undermine yield due to longer wires and a higher probability of opens

**OpenLANE (open sourse automated flow):** 

OpenLANE is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, Fault, OpenPhySyn, SPEF-Extractor and custom methodology scripts for design exploration and optimization.

Below is the openLANE flow (RTL2GDSII):

![OpenLANE Flow](https://github.com/efabless/openlane/blob/master/doc/openlane.flow.1.png)

Each flow run based on TCL scripts available in the [vsdflow](https://github.com/kunalg123/vsdflow) 

Working directory and available folders:

![Working Directory](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Working_dir_openLANE_PDK.JPG)

Note that PDK is the process design kit, this is very important in the open source design environment. The PDK folder contains delay,minimum length and  foundry capabilities of process across PVT corners.In this workshop, the directory is:

![OpenLANE Working directory](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Working_dir_openLANE.JPG)  

Before start the OpenLANE, important to make sure the file ./designs/[design]/config.tcl available and updated as per need.

Config file:

![config.tcl](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/COFIG_TCL.JPG)

Below is the command to start openLANE:

![OpenLANE Intro](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/OpenLANE_Intro.JPG)

Adding Package openLANE 0.9:

![OpenLANE Package](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/OpenLANE_Package.JPG)

It is important to prepare the design after invoke the package. The current design is picorv32a.so we need to issue prep -design picorv32a which will merge all the LEF and also complete padding:

![preparing](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/preparing.JPG)

After preparing, we need to perform synthesis. The Mapping and static timing analysis also performed in the Synthesis stage.below are the open source tools used for each tasks:

**Synthesis**

1. yosys - Performs RTL synthesis
2. abc - Performs technology mapping
3. OpenSTA - Performs static timing analysis on the resulting net-list to generate timing reports

**_run_synthesis_** is the command for start synthesis. Synthesis takes a few minutes(according to the depth of design), below is the result summary:

![Synthesis](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/picorv32a_synthesis_result.JPG) 

Important is flop ratio. the **flop ratio = Total number of flip-flops / Total number of Cells**

Here,the total number of D flip-flop = 1634 and total number of Cells = 17323 and the ratio = 0.094

All the runs available in working_dir/openLANE/design/picorv32a/runs directory names as date.


**Floorplan and PDN:**

This is the first major step in getting your layout done, and this is the most important one.Your floorplan determines your chip quality. Floorplanning includes
* Define the size of your chip/block and Aspect ratio
* Defining the core area and IO core spacing
* Defining ports specified by top level engineer.
* Design a Floor Plan and Power Network with horizontal metal layer such that the total IR Drop must be less than 5% (VDD+VSS) of VDD to operate within the power budget.
* IO Placement/Pin placement
* Allocates power routing resources
* Place the hard macros (fly-line analysis) and reserve space for standard cells. 
* Defining Placement and Routing blockages blockages
* If we have multi height cells in the reference library separate placement rows have to be provided for two different unit tiles.
* Creating I/O Rings
* Creating the Pad Ring for the Chip
* Creating I/O Pin Rings for Blocks     

_**Tools:**_

1. init_fp - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
2. ioplacer - Places the macro input and output ports
3. pdn - Generates the power distribution network
4. tapcell - Inserts welltap and decap cells in the floorplan

In this workshop, we are going to run floorplan first. The PDN (Power distribution network) will run later part in the flow.     
              
**Floorplan config:**

![Config Floorplaning ](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Floor_planing_CONFIG.PNG)

_**run_floorplan**_ is the command for start floorplan.Below are a few snapshots after completing floorplan:

![Floorplan result ](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Floor_planing_result.PNG)

Below snapshot contains the die area with the _UNIT DISTANCE MICRONS 1000;_ this means the die area [(Lower left(ll) X,Lower left(ll) Y),(Upper Right(ur) X,Upper Right(ur) Y)] are calculated with 1 um = 1000 Database unit.

![Floorplan result](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Floor_planing_result_1.PNG)  

Command to open the floorplan /work_dir/openLANE/design/picorv32a/runs/[Tag]/floorplan/*.def file using magic:

![Magic Floorplan](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/opening_magic_floor_plan.PNG) 

Floorplan in **Magic**  (more details available in http://opencircuitdesign.com/magic/):

![Floorplan in magic](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/magic_initial_window.PNG)

Use "z" and "Shift+z" for zoom-in and zoom-out and use the command "what" in the  "tkcon 2.3 main" xterm for viewing detials of any selected (use "s" once mouse point over the pin) pins:

![Floor plan metal details](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/metal%20details.PNG)

**Placement**

Placement is the process of finding a suitable physical location for each cell in the block.
Tool only determine the location of each standard cell on the die.
Placement does not just place the standard cell available in the synthesized netlist, it also optimized the design.

The tool determines the location of each of the standard cell on the core. Various factors come into play like the timing requirement of the system, the interconnect length and hence the connections between cells, power dissipation, etc. the interconnect length depends on the placement solution used, and it is very important in determining the performance of the system as the geometries shrink.
Placement will be driven based on different criteria like timing driven, congestion driven, power optimization.

_**Tools:**_

1. RePLace - Performs global placement
2. Resizer - Performs optional optimizations on the design
3. OpenPhySyn - Performs timing optimizations on the design
4. OpenDP - Perfroms detailed placement to legalize the globally placed components

_**run_placement**_ is the command for start placement.Below are a few snapshots after completing placement using magic:

Terminal : 

![placement_terminal](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/run_placement_terminal.PNG)

Magic "expand" command to view the layout of a cell after placement:

![Expand_magic](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/magic_expand_the_cell.PNG)


