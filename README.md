
VLSI design open source RTL2GDSII 

![OpenLANE Flow](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/VLSI-design-course.jpg)


**Physical design**

Physical design means netlist (.v ) converted into GDSII form(layout form)
logical connectivity of cells converted into physical connectivity.
During physical design, all design components are instantiated with their geometric representations. In other words, all macros, cells, gates, transistors, etc., with fixed shapes and sizes per fabrication layer, are assigned spatial locations (placement) and have appropriate routing connections (routing) completed in metal layers.

Physical design directly impacts circuit performance, area, reliability, power, and manufacturing yield. Examples of these impacts are discussed below.
Performance: long routes have significantly longer signal delays.
* Area: placing connected modules far apart results in larger and slower chips.
* Reliability: A large number of vias can significantly reduce the reliability of the circuit.
* Power: transistors with smaller gate lengths achieve greater switching speeds at the cost of higher leakage current and manufacturing variability; larger transistors and longer wires result in greater dynamic power dissipation.
* Yield: wires routed too close together may decrease yield due to electrical shorts occurring during manufacturing, but spreading gates too far apart may also undermine yield due to longer wires and a higher probability of opens

**OpenLANE (open source automated flow):** 

OpenLANE is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, Fault, OpenPhySyn, SPEF-Extractor and custom methodology scripts for design exploration and optimization.

Below is the openLANE flow (RTL2GDSII):

![OpenLANE Flow](Snapshots/3922DD5A-21DD-47FB-B7F1-A8966DB3DFA7.png)

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

**SYNTHESIS**

Synthesis transforms the simple RTL design into a gate-level netlist with all the constraints as specified by the designer. In simple language, Synthesis is a process that converts the abstract form of design to a properly implemented chip in terms of logic gates.

Synthesis takes place in multiple steps:

![Synthesis steps](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Synthesis_steps.png)

Synthesis is a very important process for the designers as it enables them to see how the design will actually look like after fabrication. All parameters including area, timing, power can be reported and checked by the designer beforehand. He/She can make the necessary changes(if required) before the actual fabrication process, thus saving both time and cost. Also,Mapping those gates to actual technology-dependent logic gates available in the technology libraries and optimizing the mapped netlist keeping the constraints set by the designer intact.

_**Tools:**_
1. yosys - Performs RTL synthesis
2. abc - Performs technology mapping
3. OpenSTA - Performs static timing analysis on the resulting net-list to generate timing reports

**_run_synthesis_** is the command for start synthesis. Synthesis takes a few minutes(according to the depth of design), below is the result summary:

![Synthesis](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/picorv32a_synthesis_result.JPG) 

Important is flop ratio. the **flop ratio = Total number of flip-flops / Total number of Cells**

Here,the total number of D flip-flop = 1634 and total number of Cells = 17323 and the ratio = 0.094

All the runs available in working_dir/openLANE/design/picorv32a/runs directory names as date.


**FLOORPLAN AND PDN:**

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

**PLACEMENT**

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

**STANDARD CELLS**

There are multiple standard cells available in github or skywater. In this workshop let's cloning from github as below:

![git clone](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/git_invertor_clone.PNG)

Goto cloned vsdstdcelldesign and copy the technology from PDK folder available in work directory because technology file needed for MAGIC tool to open the cell layout.
 
![copy technologyfile](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/copy_tech_file_for%20magic.PNG)

As we can see the sky130_inv.mag in the vsdstdcelldesign , we can open sky130_inv.mag using magic with the copied technology file. Below is the command to open the .mag file in magic:

![open mag with magic](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/open_Magic_with_mag_for_seeing_inv.PNG)

The standard cell inverter layout looks as below:

![Inverter](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/layout_inv.PNG)

In the inverter layout, select a mask as below to know the MOSFET type with the magic command "what" :

![nmos](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/nmos_layout.PNG)

![pmos](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/pmos_layout.PNG)

An inverter layout consists of multiple layers, below snapshot shows the layers used in inverter layout:

![layer in inverter](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/metal_layer_details_inv.PNG)

Next step is to characterise the inverter using ngspice. The command " **extraxt all,ext2spice chresh 0 rthresh 0** followed by **ext2spice**" create spice netlist.

![splice netlist extraxt](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/magic_extract_cell_inv.PNG)

![splice netlist extraxt](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/ext2spice.PNG)

![splice netlist extraxt](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/ext2spice_generate_spice_file.PNG)

THe spice netlist can be observed with the nodes available in the layout:

![spice netlist](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/spice_inv_file.PNG)

The spice netlist is not completed without the VDD,VSS and input pulse. Below update spice netlist consist of all the necessary input and power:
 
![inverter updated spice netlist](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/sky130_invPspice_netlist.PNG)

Now, we are ready for ngspice simualtion.Below is the terminal result after issuing the ngspice command  **ngspice sky130_inv.spice** : 

![ngspice terminal](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/ngspice_sky130_inv_terminal.PNG)

As we need to observe the waveform we can use the command **plot**.

![ngspice plot](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/plot_ngspice.PNG)

To reduce the spike in the rising edge, we will update the spice netlist C3 capacitance (output (Y) Load capacitance ) as below:

![change cap in spice netlist](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/spice_file_update_C3_for_reduce_the%20spike.PNG)

The inverter waveform can observe as below after re-run the **ngspice sky130_inv.spice** and **plot** command:

![spice wave](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/SPICE_WAVE_INV.PNG)

As the part of characterisation, we can get the propogation delay from the ngspice as below:
![characterisation](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/propogation_rise_high.PNG)

Using the waveform in  **ngspice** tool, we are mesuring the propogation delay. there are 8 different timing threshold characterisation need to perform which are listed below:

1. slew_low_rise_thr  - considering 20% of rise time
2. slew_high_rise_thr - considering 80% of rise time
3. slew_low_fall_thr - considering 20% of fall time
4. slew_high_fall_thr - considering 80% of fall time
5. in_rise_thr - considering 50% of input rise time
6. in_fall_thr - considering 50% of input fall time
7. out_rise_thr - considering 50% of output rise time
8. out_fall_thr - considering 50% of out fall time

**propogation delay = time(out_thr) - time(in_thr)**

**DRC CHECK IN DRC TESTS LAYERS**

The DRC (Design Rule Check) is one of the very important in the layout.In this workshop, we are trying to download all the metal layers. Below is command and respective website for download the list of drc_tests.

_wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz_

Untar the downloaded folder with tar -xvz and goto the drc_test folder to open in magic (using command magix -d XR)

Here, opened metal3 layout as below:
![M3 Layer](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/open_magic_M3_layers_sky130.PNG)
Now that create a box and add metal2 init and see via using cif see VIA2 :
![cif see VIA2](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/cif%20see%20VIA2_command_add_via.PNG)

There are a few more commands for DRC as below:

1. ;drc why  - to check the drc error from layout
2. **tech load[tech file]** & command **drc check** - to load the updated tech file and check the drc

some other commands as below :

Box (for measure):
![box](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/box_command_magic.PNG)

Feed clear (to clear the via2):
![feed clear](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/feed_clear_command_to_remove_VIA2.PNG)

Load command to load any .mag file (here poly layer) :
![load poly](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/load_poly_magic_command.PNG)

**To clear the DRC rules, need to follow the Skywater PDK website (https://skywater-pdk--136.org.readthedocs.build/en/136/rules/periphery.html#poly)** for right measurements.

**LEF FILE EXTRACTION FROM STD CELL LAYOUT**

Next is to get the grid in magic. The grid details available in pdk/TRACE.info file as below:

![trace_info](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/TracePinfo_in_pdk.PNG)

Include grid in magic with _grid_:

![magic grid](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Grid_magic.PNG)

Define port (using options -> texts) :

![port](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/define_the_port_from_edit_text_after_select.PNG)

Define _port class_ and _port use_ in magic:

![port class](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Y_port_class_and_use.PNG)

Create LEF from STD CELL inverter using _write lef_:

![LEF write](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/lef_write_command_to_create_lef.PNG)

**INCLUDE STD CELL TO PICORV32A**

Copy the library and new created inverter .lef file to the /design/picorv32a/src directory:

![COPY TO SRC](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/copy_lef_lib_to_design_src.PNG)

Next step to update the config file with std cell inverter .lef file as below:
![config_update](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/CONFIG_UPDATE_WITH_LEF.PNG)

Add .lef after _prep_ :

![add lef in openlane flow](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/add_lefs.PNG)

The inverter cell can be observed after placement in magic as below:

![Inverter cell](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/CUstom_inverter_after_placement.PNG)

Create/copy the my_base.sdc file for timing analysis and pre_sta.conf file as below:

![my_base](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/my_base_sdc.PNG)

![cts_config](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/create_cts_file.PNG)

SYNTHESIS variables need to set for reduce the slack violations as below:
![slack violation](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/InkedSYTH_variable_set_LI.jpg)

Reducing slack after using the _sta pre_sta.conf_ and replace a few buffers:

![slack violation](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/reduce_slack_m1p0535_after_replace_cell.PNG)

![slack violation](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/reduce_to_0p77.PNG)

We can reduce the slack and use _write_verilog_ command to write to the synthesis verilog in the result/synthesis/synthesis.v

**CLOCK TREE ANALYSIS**

Use **_run_cts_** command to start CTS and we can see the result in terminal as below:
![run cts](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/run_cts_result_terminal.PNG)

Next is to start openroad in openlane to start timing analysis with just issue the command _openroad_. Once the openroad ready for next command, use load_lef and load_def followed bt write_db to create a database. below are a few snapshots:
![timing analysis1](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/created_db.PNG)

![timing analysis2](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/read_db_and_verilog.PNG)

![timing analysis3](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/read_sdc.PNG)

Below are a few debug steps to reduce the slack  in timing analysis:

![timing analysis4](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/find_delay_clock.PNG)

![timing analysis5](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/reduce_slack_inopenroad_timing_analysis.PNG)

![timing analysis6](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/link_desing_operoad.PNG)

![timing analysis7](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/report_check_delay_digits4.PNG)

Slack violation for Hold time:

![timing analysis8](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/hold_timing.PNG)

Slack violation for Setuptime time:

![timing analysis9](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/setup_time.PNG)

Command to remove the first buf from the list:

![timing analysis10](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/remove_first_buf_with_Ireplace.PNG)

Need to use _set_ to get reflected in the openlane:

![timing analysis11](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/set_lreplace.PNG)

Setting current def as placement for further steps in the openlane flow:

![timing analysis12](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/set_current_def_placement_before_cts.PNG)

Another command to insert the buf back to the list:

![timing analysis13](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/linsert.PNG)




**PDN SETTING**
This is another important requirement for generate power routing. in openLANE we use gen_pdn command:

![GEN PDN](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/gen_pdn_forPDN.PNG)

![PDN](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/pdn_df.PNG)




**ROUTING**

The last step in the openLANE is routing using _**run_routing**_ :

![ROUTING1](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/run_routing_terminal.PNG)

Summary of routing available in terminal as below:

![ROUTING2](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/routing_summary.PNG)

After the routing we can see the DRC violations as below:

![ROUTING3](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Final_routing_drc_report_violations.PNG)

After the routing, SPEF(standard paracitic extraction form) file need to generate from DEF and LEF in outside of openlane flow as below:

![SPEF EXTRACTION](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/SPEF_EXTRACTION.PNG)



**FULL DESIGN WITH INVERTER STD CELL**

We can observe our routing layout including inverter with magic as below:

![ROUTING](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Inverter_after_route_in_full_design.PNG)

![ROUTING](https://github.com/soorajkvl/openLANE-Sky130-Workshop/blob/main/Snapshots/Inverter_after_route_in_full_design1.PNG)


**GDSII** file can be obtained with  _**run_magic**_ command.


_Note: more snapshots available in snapshot directory in github._


**ACKNOWLEDGEMENTS**

* Kunal Ghosh, Co-Founder (VSD Corp. Pvt. Ltd)
* Nickson P Jose, Teaching Assistant (VSD Corp. Pvt. Ltd)
