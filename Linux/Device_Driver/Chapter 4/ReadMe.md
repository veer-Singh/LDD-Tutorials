### Device 
<style>
r { color: Red }
o { color: Orange }
g { color: Green }
b { color: Blue }
y { color: yellow }
</style>	
---
The __<b>device file</b>__ allows transparent communication between user-space applications and hardware.
All device files are stored in /dev directory.  
 ___<o>/dev/null___ acts as a byte sink; any write request to ***<o>/dev/null*** will succeed, but the data are written will be ignored.

We can create a device file in two ways.

* ___Manually___  
	```
	mknod -m <permissions> <name> <device type> <major> <minor> <name> – Your device file name that should have a full path (/dev/name)

	<device type> – Put c or b
	c – Character Device
	b – Block Device
	<major> – major number of your driver
	<minor> – minor number of your driver
	-m <permissions> – optional argument that sets the permission bits of the new device file to permissions
	e.g. : sudo mknod -m 666 /dev/etx_device c 246 0
	```
* ___Automatically___  
The automatic creation of device files can be handled with udev. Udev is the device manager for the Linux kernel that creates/removes device nodes in the ___<o>/dev</o>___ directory dynamically. Just follow the below steps.

	1. Include the header file ___<o>linux/device.h___ and ___<o>linux/kdev_t.h___
	2. Create the ___<g>struct Class___
	3. Create Device with the class which is created by the above step  
#### ___Create Class___
```C
/*
owner – pointer to the module that is to “own” this struct class

name – pointer to a string for the name of this class
*/
struct class * class_create(struct module *owner, const char *name);
```
It will create a structure under <b>/sys/class/</b>.
This is used to create a struct class pointer that can then be used in calls to <o>class_device_create</o>. The return value can be checked using __IS_ERR()__ macro.   
the pointer created here is to be destroyed when finished by making a call to class_destroy.
```c
void class_destroy (struct class * cls);
```

### ___Create Device___
```c
/*
class – pointer to the struct class that this device should be registered to

parent – pointer to the parent struct device of this new device, if any

devt – the dev_t for the char device to be added

drvdata – the data to be added to the device for callbacks

fmt – string for the device’s name

... – variable arguments
*/
struct device *device_create(struct *class, struct device *parent, dev_t dev, void * drvdata, const char *fmt, ...);
```
This function can be used by char device classes. A struct device will be created in <o>__sysfs__</o>, and registered to the specified class.  

A “dev” file will be created, showing the dev_t for the device, if the dev_t is not 0,0. If a pointer to a parent struct device is passed in, the newly created struct device will be a child of that device in <o>__sysfs__</o>.  

Always destroy the device if not required 
```c
void device_destroy (struct class * class, dev_t devt);
```


#### ___Creating a device file___

This is just a basic linux device driver. This will explain about the device file and how to create that in the linux device driver.

Please update your Beaglebone board's kernel directory in the Makefile.

Build for Beaglebone:
	sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-

Build for Raspberry Pi or Virtualbox Ubuntu:
	sudo make

You can check the video tutorial of this example here (https://youtu.be/nRG8DdsjQdE).

Please refer this URL for the complete tutorial of this source code.
https://embetronicx.com/tutorials/linux/device-drivers/device-file-creation-for-character-drivers/

The Linux Device Driver Video Playlist - https://www.youtube.com/watch?v=BRVGchs9UUQ&list=PLArwqFvBIlwHq8WMKgsXSQdqIvymrEz9k

How to Setup Ubuntu and Raspberry PI - https://www.youtube.com/watch?v=e6gNeje3ljA
How to Setup BeagleBone and Cross compile the kernel - https://www.youtube.com/watch?v=am-dgmrMgYY&t 
