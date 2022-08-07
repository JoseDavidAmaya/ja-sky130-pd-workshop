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
`run_synthesis`

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

`run_floorplan`

![img](img/day2/1.png)

![img](img/day2/2.png)

Die area

![img](img/day2/3.png)

Die area is of 660685/1000 [um] * 671405/1000 [um] = 443587.212425 [(um)^2]

To see the layout, from the runs/<run>/results/floorplan folder we run

magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &

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

From the runs/<run>/results/placement folder we run

magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &

![img](img/day2/10.png)

![img](img/day2/11.png)

![img](img/day2/12.png)

We can see all the standard cells are now placed across the core area.
