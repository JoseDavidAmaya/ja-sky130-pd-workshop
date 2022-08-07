# ja-sky130-pd-workshop

This is the lab report of the sky130-pd-workshop

# Day 1

To start the RTL to GDSII flow, we first go to the openlane directory and run docker, then start the flow in interactive mode using the picorv32a design

Run commands:

```
cd
source .bashrc

cd ~/Desktop/work/tools/openlane_working_dir/openlane

docker
./flow.tcl -interactive

package require openlane 0.9

prep -design picorv32a
```

![img](img/day1/1.png)

After that, the runs directory is created inside the designs/picorv32a folder

![img](img/day1/2.png)

We run synthesis with the command:
```
run_synthesis
```

![img](img/day1/3.png)

Note that tns and wns are both negative

We calculate flop ratio and buffer ratio

![img](img/day1/4.png)

![img](img/day1/5.png)

Flop ratio = Number of dfxtp cells / Number of cells = 1613 / 14876 = 0.1084 

![img](img/day1/6.png)

Buffer ratio = (Number of buf cells) / Number of cells = (1656 + 8) / 14876 = 0.11185

# Day 2

## Floorplan

After the synthesis is done, we run the floorplan where we place things like the io pins and preplaced cells (standard cells are not yet placed). The floorplan has several configurations just like the synthesis that modify the behaviour and thus the results of the step, we run it with default values.

```
run_floorplan
```

![img](img/day2/1.png)

![img](img/day2/2.png)

Die area

![img](img/day2/3.png)

Die area is of 660685/1000 [um] * 671405/1000 [um] = 443587.212425 [(um)^2]

To see the layout, from the `runs/<run>/results/floorplan` folder we run

```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

![img](img/day2/4.png)

![img](img/day2/5.png)

We can see the distribution of the IO pins

![img](img/day2/6.png)

Standard cells are kept in the lower left corner (they are not placed yet)

![img](img/day2/7.png)

## Placement

In this step, the standard cells are placed in the core.

`run_placement`

![img](img/day2/8.png)

![img](img/day2/9.png)

From the `runs/<run>/results/placement` folder we run

```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![img](img/day2/10.png)

![img](img/day2/11.png)

![img](img/day2/12.png)

We can see all the standard cells are now placed across the core area.

# Day 3

## IO placer

We go back to running magic on the floorplan

![img](img/day3/1.png)

Pins should be equidistant, but they don't look like it

On openlane, we can change the variables with a command

we are gonna change FP_IO_MODE

```
set ::env(FP_IO_MODE) 2
```

and then we run floorplan again

```
run_floorplan
```

![img](img/day3/2.png)

![img](img/day3/3.png)

running magic to see the io distribution after the floorplan

From `runs/<run>/results/floorplan` folder

```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

looks like the io pins are distributed along half of the left side and the down side

![img](img/day3/4.png)

![img](img/day3/5.png)

![img](img/day3/6.png)

## Custom cells

We first need to download a repository

On the openlane folder we clone the repo

```
git clone https://github.com/nickson-jose/vsdstdcelldesign
```

![img](img/day3/7.png)


We have to open the .mag file (layout of the inverter) with magic, but we need the .tech file

the .tech file is located on `openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech`

we copy the tech file to the folder of the repo

(Running from the folder of the repo)
```
cp ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .
```

![img](img/day3/8.png)

now we run magic

```
magic -T sky130A.tech sky130_inv.mag &
```

![img](img/day3/9.png)

![img](img/day3/10.png)


On the layout, colors on the right represent the layers (for example ndifussion, metal1, etc), hovering over them shows the name of the layer on the top of the GUI

![img](img/day3/11.png)

![img](img/day3/12.png)

![img](img/day3/13.png)


To check if there's a connection to a component, we press S multiple times while hovering over it 

To see things connected to the output

1 time to select the output
![img](img/day3/14.png)

2 times to select the connections of the output
![img](img/day3/15.png)

## Spice model extraction and characterization

To simulate, we first need to get the spice model

![img](img/day3/16.png)

![img](img/day3/17.png)

we take the .ext file to create the spice file

![img](img/day3/18.png)

![img](img/day3/19.png)

Contents of the file

![img](img/day3/20.png)


On the spice file we check the scale

We check the size of a singular unit of the grid in the layout, and  from that change the scale in the spice file (0.01u)

![img](img/day3/21.png)

![img](img/day3/22.png)

We also have to include the .lib files, include power supplies and stimulus (a pulse output at the input of the inverter), also define the parameters for transient analysis, and update the names of the models of the pmos and nmos to reflect those in the .lib files

![img](img/day3/23.png)

To run ngspice simulation based on the spice deck we execute

```
ngspice sky130_inv.spice
```

![img](img/day3/24.png)

To plot the output (y) and input (a) vs time we run

```
plot y a vs time
```

![img](img/day3/25.png)

To characterize it, we find the rise/fall delay


### Rise delay

When the output is rising, we measure the time delta between 20%  of the maximum value and 80% of the maximum value

max value is 3.3V

20%
![img](img/day3/26.png)

80%
![img](img/day3/27.png)

cell rise delay = 2.195n - 2.152n = 0.043n

### Fall delay

Procedure is similar

80%
![img](img/day3/28.png)

20%
![img](img/day3/29.png)

cell fall delay = 4.06642n - 4.0403n = 0.02612n
