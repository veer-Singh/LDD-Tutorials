
### Major and minor number 

#### How Applications will communicate with Hardware devices?

**Application <---> Device file ---> major minor --> device driver <----> Hardware**

The driver will create a special file for every hardware device. We can communicate with the hardware using those special files (device files).

The Linux kernel represents character and block devices as pairs of numbers **\<major>:\<minor>**.  

#### __MAJOR NUMBER__
Traditionally, the major number identifies the driver associated with the device. A major number can also be shared by multiple device drivers. See /proc/devices to find out how major numbers are assigned on a running Linux instance.
```
linux@embetronicx-VirtualBox:~/project/devicedriver/devicefile$ cat /proc/devices
Character devices:
1 mem
4 /dev/vc/0
4 tty
Block devices:
1 ramdisk
259 blkext
```
#### **MINOR NUMBER**
Minor Number
The major number is to identify the corresponding driver. Many devices may use the same major number. So we need to assign the number to each device that is using the same major number. So, this is a minor number. In other words, The device driver uses the minor number <minor> to distinguish individual physical or logical devices.  

Major and minor can be allocated in two ways :  
1. ___Statically___   
	```C
	/*
	@param1 ( first ) : Is the beginning device number of the range you would like to allocate.

	@param2 ( count ):  Is the total number of contiguous device numbers you are requesting. Note that, if the count is large, the range you request could spill over to the next major number; but everything will still work properly as long as the number range you request is available.

	@param3 ( name ): Is the name of the device that should be associated with this number range; it will appear in /proc/devices and sysfs.

	@return  : 0 is successfull else return negative value .
	*/
	int register_chrdev_region(dev_t first, unsigned int count, char *name);
	```
	The dev_t type (defined in <linux/types.h>) is used to hold device numbers—both the major and minor parts. dev_t is a 32-bit quantity with 12 bits set aside for the major number and 20 for the minor number.

	```C
	MKDEV(int major, int minor);// create dev_t struct from major minor
	MAJOR(dev_t dev); // get the major number from the dev_t structure
	MINOR(dev_t dev);// get the minor number from the dev_t structure
	```
2. ___Dynamically___
	```C
	/*
	@param1 ( dev ) : Is an output-only parameter that will, on successful completion, hold the first number in your allocated range.

	@param2 ( firstminor ) : should be the requested first minor number to use; it is usually 0.

	@param3 ( count ) : Is the total number of contiguous device numbers you are requesting.

	@param4 ( name ):  Is the name of the device that should be associated with this number range; it will appear in /proc/devices and sysfs.
	*/
	int alloc_chrdev_region(dev_t *dev, unsigned int firstminor, unsigned int count, char *name);
	```

#### Unregister Major minor 
Regardless of how you allocate your device numbers, you should free them when they are no longer in use. Device numbers are freed with  
`void unregister_chrdev_region(dev_t first, unsigned int count);`


## Refer
This is just a basic linux device driver. This will explain major and minor number in the linux device driver.

Please update your Beaglebone board's kernel directory in the Makefile.

Build for Beaglebone:
	sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-

Build for Raspberry Pi or Virtualbox Ubuntu:
	sudo make

You can check the video tutorial of this example here (https://www.youtube.com/watch?v=TfUTkCMCyig) and (https://youtu.be/aTwBCUjtTnw).

Please refer this URL for the complete tutorial of this source code.
https://embetronicx.com/tutorials/linux/device-drivers/character-device-driver-major-number-and-minor-number/

The Linux Device Driver Video Playlist - https://www.youtube.com/watch?v=BRVGchs9UUQ&list=PLArwqFvBIlwHq8WMKgsXSQdqIvymrEz9k

How to Setup Ubuntu and Raspberry PI - https://www.youtube.com/watch?v=e6gNeje3ljA
How to Setup BeagleBone and Cross compile the kernel - https://www.youtube.com/watch?v=am-dgmrMgYY&t 
