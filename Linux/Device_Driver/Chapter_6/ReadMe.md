<style>
r {color: red }
b {color: blue }
g {color: green }
y {color: yellow }
o {color: orange }
</style>

# Linux Driver programming 
 
 ___User space programm <---> Kernel Space programm___

## Concept
Using this driver we can send strings or data to the kernel device driver using the write function. It will store that string in the kernel space. Then when I read the device file, it will send the data which is written by write by function to the userspace application.

Funtions
* kmalloc()  
	kmalloc function is used to allocate the memory in kernel space.  
	The function is fast (unless it blocks) and doesn’t clear the memory it obtains. The allocated region still holds its previous content. The allocated region is also contiguous in physical memory.
	```c
	#include <linux/slab.h>
	/*
	size– how many bytes of memory are required.

	flags– the type of memory to allocate.
	*/
	void *kmalloc(size_t size, gfp_t flags);
	```
	<o>GFP_USER</o> – Allocate memory on behalf of the user. May sleep.

	<o>GFP_KERNEL</o> – Allocate normal kernel ram. May sleep.

	<o>GFP_ATOMIC</o> – Allocation will not sleep. May use emergency pools. For example, use this inside interrupt handler.

	<o>GFP_HIGHUSER</o> – Allocate pages from high memory.

	<o>GFP_NOIO</o> – Do not do any I/O at all while trying to get memory.
	<o>GFP_NOFS</o> – Do not make any fs calls while trying to get memory.

	<o>GFP_NOWAIT</o> – Allocation will not sleep.

	<o>__GFP_THISNODE</o> – Allocate node-local memory only.

	<o>GFP_DMA</o> – Allocation is suitable for DMA. Should only be used for kmalloc caches. Otherwise, use a slab created with SLAB_DMA.

	Also, it is possible to set different flags by OR’ing in one or more of the following additional flags:

	<o>__GFP_COLD</o> – Request cache-cold pages instead of trying to return cache-warm pages.

	<o>__GFP_HIGH</o> – This allocation has high priority and may use emergency pools.

	<o>__GFP_NOFAIL</o> – Indicate that this allocation is in no way allowed to fail (think twice before using).

	<o>__GFP_NORETRY</o> – If memory is not immediately available, then give up at once.

	<o>__GFP_NOWARN</o> – If allocation fails, don’t issue any warnings.
	<o>__GFP_REPEAT</o> – If allocation fails initially, try once more before failing.

There are other flags available as well, but these are not intended for general use, and so are not documented here. For a full list of potential flags, always refer to linux/gfp.h.

* kfree()
	This is like a free() function in the userspace. This is used to free the previously allocated memory.
	```c
	/* *objp – pointer returned by kmalloc */
	void kfree(const void *objp)
	```
* copy_from_user()
	```c
	/*
	to – Destination address, in the kernel space
	from – The source address in the user space
	n – Number of bytes to copy
	Returns number of bytes that could not be copied. On success, this will be zero.
	*/
	unsigned long copy_from_user(void *to, const void __user *from, unsigned long  n);
	```
* copy_to_user()
```c
/*
to – Destination address, in the user space
from – The source address in the kernel space
n – Number of bytes to copy
Returns number of bytes that could not be copied. On success, this will be zero
*/
unsigned long copy_to_user(const void __user *to, const void *from, unsigned long  n);
```











#### Real device driver

This is just a basic linux device driver which explains about the real read and write of the device file.

Please update your Beaglebone board's kernel directory in the Makefile.

Build for Beaglebone:
	sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-

Build for Raspberry Pi or Virtualbox Ubuntu:
	sudo make

Build the application using the below command for Ubuntu and Raspberry Pi.
		gcc -o test_app test_app.c
Build the application using the below command for BeagleBone.	
		arm-linux-gnueabihf-gcc -o test_app test_app.c


Please refer this URL for the complete tutorial of this example source code.
https://embetronicx.com/tutorials/linux/device-drivers/linux-device-driver-tutorial-programming/

You can check the video tutorial of this project - https://youtu.be/xp9HTR6a98I

The Linux Device Driver Video Playlist - https://www.youtube.com/watch?v=BRVGchs9UUQ&list=PLArwqFvBIlwHq8WMKgsXSQdqIvymrEz9k

How to Setup Ubuntu and Raspberry PI - https://www.youtube.com/watch?v=e6gNeje3ljA
How to Setup BeagleBone and Cross compile the kernel - https://www.youtube.com/watch?v=am-dgmrMgYY&t 
