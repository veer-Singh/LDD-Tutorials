# Linux_embtronix
Learn the linux module development for the from embtronix

## Linux Modules 

**KERNEL MODULE** is a piece of software  loaded or unloaded in to the kernel on demand to extend the functionality of the kernels without need of the reboot.

load-able modules are also called LKM ( Loadable Kernel Module ).
**Driver** --> Software that enables the comunication to the hardware .

### Linux Device Driver Types  : 
 1. Character Device 
    --> Devices that read/write in the char by char fashion for e.g. keyboard, mouse, serial printer 
    if a user using a file to write a data no other user can access the file .
 2. Block Device 
    --> Device that read/write the data in the Block by bloack fashion  e.g CD-ROM, HDD, USB
 3. Network Device 
    --> An entity or device that sends and receives the packets e.g Ethernet card .