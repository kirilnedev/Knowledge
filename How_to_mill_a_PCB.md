# Milling a PCB
## Table of Contents

- [Milling a PCB](#milling-a-pcb)
  - [Table of Contents](#table-of-contents)
  - [Steps](#steps)
    - [Create the PCB](#create-the-pcb)
    - [Create the G-Code (FlatCAM)](#create-the-g-code-flatcam)
    - [Send the G-code to the machine after leveling the surface (OpenCNCPilot)](#send-the-g-code-to-the-machine-after-leveling-the-surface-opencncpilot)

## Steps

### Create the PCB

  - Open EasyEDA (Assuming you already have a schematic)
  - Click on Design > Convert To PCB
  - Select units: mm, copper layer (1 or 2, whatever you have), Board Outline (whatever you need)
  - Click Apply
  - Arrange the components (Space rotates them)
  - Put all traces between the components (On the right menu you can specify trace width - "Routing Width")
  - Go to Tools > Set Board Outline to  create the outline
  - Edit the outline if needed
  - Go to File > Generate PCB Fabrication file
  - Click on Generate Gerber - this will save a ZIP file with all needed Gerber files
    <br />![image.png](/.attachments/1.png)

### Create the G-Code (FlatCAM)

  - Open FlatCAM (described version is 8.9.9.1b)
  - File > Open > Open Gerber and load the Gerber_BoardOutline as well as Gerber_BottomLayer, TopLayer etc. 
  - File > Open > Open Excellon and load the Gerber_Drill paths that you have 
  - Select the Gerber_BoardOutline file and go to Tool > Cutout PCB
    <br />![image.png](/.attachments/2.png)

  - Specify your tool diameter (1mm), Cut Z: (-1.6mm), Multi-Depth (depth per cut - 0.2mm), Gap size (1mm)
  - Click on Generate Freeform Geometry. This will generate a geometry for cutting the board
  - Double click the newly created Geometry
    <br />![image.png](/.attachments/3.png)

  - Specify: 
    - Cut Z: -1.6
    - Multi-Depth: 0.200
    - Travel Z: 2.00 (Height above the PCB for free movements)
    - Feedrate X-Y: 60.00
    - Feedrate Z: 20.00
    - Spindle speed: 10000
    - PostProcessor: grbl_11
  - Click on Generate CNCJob object
  - Double click the Gerber_Drill_PTH (under Excellon)
    <br />![image.png](/.attachments/4.png)

  - Specify: 
    - Cut Z: -1.700
    - Travel Z: 2.000
    - Feedrate Z: 20.00
    - Spindle speed: 10000
    - Drill tool dia: 1.00 (or whatever your tool is)
    - PostProcessor: grbl_11
    <br />![image.png](/.attachments/5.png)

  - Click on Create Drills GCode
  - Double click the TopLayer (or BottomLayer, whatever you want to mill)
  - Specify:
    - Tool dia: 0.1000 (or whatever the tip of your V-bit is)
    - Passes: 1
      <br />![image.png](/.attachments/6.png)

  - Click on Generate Isolation Geometry
  - Specify:
    - Cut Z: -0.2000 (how deep the engraving v-bit will go into the PCB)
    - Travel Z: 2.000 (Height above the PCB for free movements)
    - Feedrate X-Y: 60.00 (the X-Y cutting speed)
    - Feedrate Z: 20.00 (how fast it will drill into the PCB)
    - Spindle speed: 10000
    - PostProcessor: grbl_11
  - Click on Generate CNCJob Object
  - At this point we have the CNC code for Drilling, milling the edges and engraving the traces. We can double click on each one of them and select Save CNC Code

### Send the G-code to the machine after leveling the surface (OpenCNCPilot)

  - Open OpenCNCPilot (in this case v1.5.10.0)
  - Click on Connect to connect to the CNC (make sure you have selected the proper COM port under settings)
  - Make sure you've put the proper tool for this operation, for example Drill bit for drilling, flat-end mill bit for milling, v-bit for engraving etc.
  - Click on Open and select the file for the operation you want to do. (Order should be Engraving > Drilling > Cutting)
    <br />![image.png](/.attachments/7.png)

  - Go to Probing > Create New > Apply (this sets the proper size for probing based no the GCode) > Ok
  - Click on RUN. This will start the probing process which will capture the surface deviations
    <br />![image.png](/.attachments/8.png)

  - Once ready click on Edit > Apply HeightMap. This will modify your GCode to align it with the PCB surface
    <br />![image.png](/.attachments/9.png)

  - Click on Start and this will start the milling operation