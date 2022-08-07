# ja-sky130-pd-workshop

This is the lab report of the sky130-pd-workshop

# Day 1

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

![img](img/day1/1.jpg)

After that, the runs directory is created inside the designs/picorv32a folder

![img](img/day1/2.jpg)

We run synthesis with the command:
`run_synthesis`

![img](img/day1/3.jpg)

Note that tns and wns are both negative

We calculate flop ratio and buffer ratio

![img](img/day1/4.jpg)

![img](img/day1/5.jpg)

Flop ratio = Number of dfxtp cells / Number of cells = 1613 / 14876 = 0.1084 

![img](img/day1/6.jpg)

Buffer ratio = (Number of buf cells) / Number of cells = (1656 + 8) / 14876 = 0.11185

