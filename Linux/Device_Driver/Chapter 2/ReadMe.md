
### Module Parameter Passing

#### MACROS 
Header file required  :  `linux/moduleparam.h`
 ```C
module_param(name, type, perm);
module_param_array(name,type,num,perm);
module_param_cb();
 ```
There are several types of permissions for file/Variable:

* S_IWUSR
* S_IRUSR
* S_IXUSR
* S_IRGRP
* S_IWGRP
* S_IXGRP  
In this S_I is a common header.
R = read ,W =write ,X= Execute.
USR =user ,GRP =Group
Using OR ‘|’ (or operation) we can set multiple permissions at a time.

* The macro should be placed outside of any function and is typically found near the head of the source file.  


```C
module_param(valueETX, int, S_IWUSR|S_IRUSR);
```
This will create a sysfs entry. ___(/sys/module/hello_world_module/parameters/valueETX)___

we can change the value of the valueETX as
```
sudo su
echo 1 > /sys/module/hello_world_module/parameters/valueETX
```
This will update the valueETX variable. But there is no way to notify your module that “valueETX” has changed.

By using this module_param_cb() macro, we can get a notification.

If you want to get a notification whenever the value got to change, we need to register our handler function to its file operation structure first.
```c
struct kernel_param_ops 
{
 int (*set)(const char *val, const struct kernel_param *kp);
 int (*get)(char *buffer, const struct kernel_param *kp);
 void (*free)(void *arg);
};
```
follow code to get `module_param_cb()` understanding

## Refer

This is just a basic linux device driver. This will explain how to pass the arguments to the linux device driver.

Please update your Beaglebone board's kernel directory in the Makefile.

Build for Beaglebone:
	sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-

Build for Raspberry Pi or Virtualbox Ubuntu:
	sudo make

You can check the video tutorial of this example here (https://www.youtube.com/watch?v=Z4jwi8SP5zs).

Please refer this URL for the complete tutorial of this source code.
https://embetronicx.com/tutorials/linux/device-drivers/linux-device-driver-tutorial-part-3-passing-arguments-to-device-driver/

The Linux Device Driver Video Playlist - https://www.youtube.com/watch?v=BRVGchs9UUQ&list=PLArwqFvBIlwHq8WMKgsXSQdqIvymrEz9k

How to Setup Ubuntu and Raspberry PI - https://www.youtube.com/watch?v=e6gNeje3ljA
How to Setup BeagleBone and Cross compile the kernel - https://www.youtube.com/watch?v=am-dgmrMgYY&t 

