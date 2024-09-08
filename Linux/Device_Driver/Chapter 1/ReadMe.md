# FIRST LINUX PROGRAM

### MODULE INFORMATION 

Header File Used  :  linux/modules.h
### LICENCE

```C
MODULE_LICENCSE( "GPL" )
MODULE_LICENSE("DUAL  BSD|GPL")
```

* "GPL" [GNU Public License v2 or later]
* "GPL v2" [GNU Public License v2]
* "GPL and additional rights" [GNU Public License v2 rights and more]
* "Dual BSD/GPL" [GNU Public License v2 or BSD license choice]
* "Dual MIT/GPL" [GNU Public License v2 or MIT license choice]
* "Dual MPL/GPL" [GNU Public License v2 or Mozilla license choice]

`modinfo` command can be used to get the module information  
**Note:** It is not strictly necessary, but your module really should specify which license applies to its code.

### AUTHOR
```C
MODULE_AUTHOR("Author");
```
### MODULE DESCRIPTION
```C
MODULE_DESCRIPTION("A sample driver");
```
### MODULE VERSION 
Version of form \[\<epoch>:\]\<version>\[-\<extra-version>].  
1. **epoch**: A (small) unsigned integer which allows you to start versions anew. If not mentioned, it’s zero. eg. “2:1.0” is after “1:2.0  
2. **version**: The ___version___ may contain only alphanumerics and the character `.’. Ordered by numeric sort for numeric parts, ASCII sort for ASCII parts (as per RPM or DEB algorithm).  
3. **extraversion**: Like \<version>, but inserted for local customizations, eg “rh3” or “rusty1”.
```C
MODULE_VERSION("2:1.0");
```


### Init function  
This is the function that will execute first when the Linux device driver is loaded into the kernel. For example, when we load the driver using insmod, this function will execute. Please see below to know the syntax of this function.
```c
static int __init hello_world_init(void) /* Constructor */
{
	return 0;
}
module_init(hello_world_init);
```

### Exit function
This is the function that will execute last when the Linux device driver is unloaded from the kernel. For example, when we unload the driver using rmmod, this function will execute. Please see below to know the syntax of this function.
```C
void __exit hello_world_exit(void)
{

}
module_exit(hello_world_exit);
```

**printK** let u to print the kernel messages with defined priority or class
KERN_EMERG, KERN_INFO, KERN_ALERT, KERN_CRIT, KERN_ERR, KERN_WARNING,KERN_NOTICE

Note: In the newer Linux kernels, you can use the APIs below instead of this printk.

pr_info – Print an info-level message. (ex. pr_info("test info message\n")).  
pr_cont – Continues a previous log message in the same line.  
pr_debug – Print a debug-level message conditionally.  
pr_err – Print an error-level message. (ex. pr_err(“test error message\n”)).  
pr_warn – Print a warning-level message.  

![ Compile Module]( ./linux-device-driver-tutorial-make.png)

### Loading 
```
sudo insmod hello_world_module.ko
```
check the modules loaded with command ***lsmod***  
check the kernel message with ***dmesg***

### Unloading 
```
sudo rmmod hello_world_module.ko or
sudo rmmod hello_world_module
```


#### About Linux Module

This is just a basic linux device driver. This kernel module will print some debug messages at the init and exit time.

Please update your Beaglebone board's kernel directory in the Makefile.

Build for Beaglebone:
	sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-

Build for Raspberry Pi or Virtualbox Ubuntu:
	sudo make

Please refer this URL for the complete tutorial of this source code.
[link to blog](https://embetronicx.com/tutorials/linux/device-drivers/linux-device-driver-tutorial-part-2-first-device-driver/)

You can check the video tutorial of this project.
https://youtu.be/hMsA1bA1Upk and https://youtu.be/xqsro29xQPo

The Linux Device Driver Video Playlist - https://www.youtube.com/watch?v=BRVGchs9UUQ&list=PLArwqFvBIlwHq8WMKgsXSQdqIvymrEz9k

How to Setup Ubuntu and Raspberry PI - https://www.youtube.com/watch?v=e6gNeje3ljA
How to Setup BeagleBone and Cross compile the kernel - https://www.youtube.com/watch?v=am-dgmrMgYY&t 










