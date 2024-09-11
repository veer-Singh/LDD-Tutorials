<style>
r { color: Red }
o { color: Orange }
g { color: Green }
b { color: Blue }
y { color: yellow }
</style>	
# Cdev structure and File Operations of Character drivers
If we want to open, read, write, and close we need to register some structures to the driver.  

## cdev structure
In Linux kernel struct inode structure is used to represent files. Therefore, it is different from the file structure that represents an open file descriptor. There can be numerous file structures representing multiple open descriptors on a single file, but they all point to a single inode structure.

The inode structure contains a great deal of information about the file. As a general rule, cdev structure is useful for writing driver code:

 struct cdev is one of the elements of the inode structure. As you probably may know already, an inode structure is used by the kernel internally to represent files. The struct cdev is the kernel’s internal structure that represents char devices.  This field contains a pointer to that structure when the inode refers to a char device file.

 ```c
 struct cdev { 
    struct kobject kobj; 
    struct module *owner; 
    const struct file_operations *ops; 
    struct list_head list; 
    dev_t dev; 
    unsigned int count; 
};
 ```
This is cdev structure. Here we need to fill the two fields,  
`file_operation` (This we will see after this cdev structure)
owner ( This should be ___THIS_MODULE___ )

* Runtime Allocation  
* Own allocation

If you wish to obtain a standalone cdev structure at runtime, you may do so with code such as:
```c
struct cdev *my_cdev = cdev_alloc( );
my_cdev->ops = &my_fops;
```
Or else you can embed the cdev structure within a device-specific structure of your own by using the below function.
```c
void cdev_init(struct cdev *cdev, struct file_operations *fops);
```
Once the cdev structure is set up with file_operations and owner, the final step is to tell the kernel about it with a call to:
```c
/*
dev is cdev structure

num is the first device number to which this device responds, and

count is the number of device numbers that should be associated with the device. Often count is one, but there are situations where it makes sense to have more than one device number correspond to a specific device.

*/
int cdev_add(struct cdev *dev, dev_t num, unsigned int count);
```
If this function returns a negative error code, your device has not been added to the system. So check the return value of this function.

After a call to cdev_add(), your device is immediately alive. All functions you defined (through the file_operations structure) can be called.  

To remove a char device from the system, call:
```c
void cdev_del(struct cdev *dev);
```
Clearly, you should not access the cdev structure after passing it to cdev_del.

## File Operations 
The ___file_operations___ structure is how a char driver sets up this connection. The structure, (defined in `<linux/fs.h>`), is a collection of function pointers. Each open file is associated with its own set of functions. The operations are mostly in charge of implementing the system calls and are, therefore, named open, read, and so on.

A ___file_operations___ structure is called fops. Each field in the structure must point to the function in the driver that implements a specific operation or have to left NULL for unsupported operations. The whole structure is mentioned below snippet.

```c
struct file_operations {
    struct module *owner;
    loff_t (*llseek) (struct file *, loff_t, int);
    ssize_t (*read) (struct file *, char __user *, size_t, loff_t *);
    ssize_t (*write) (struct file *, const char __user *, size_t, loff_t *);
    ssize_t (*read_iter) (struct kiocb *, struct iov_iter *);
    ssize_t (*write_iter) (struct kiocb *, struct iov_iter *);
    int (*iterate) (struct file *, struct dir_context *);
    int (*iterate_shared) (struct file *, struct dir_context *);
    unsigned int (*poll) (struct file *, struct poll_table_struct *);
    long (*unlocked_ioctl) (struct file *, unsigned int, unsigned long);
    long (*compat_ioctl) (struct file *, unsigned int, unsigned long);
    int (*mmap) (struct file *, struct vm_area_struct *);
    int (*open) (struct inode *, struct file *);
    int (*flush) (struct file *, fl_owner_t id);
    int (*release) (struct inode *, struct file *);
    int (*fsync) (struct file *, loff_t, loff_t, int datasync);
    int (*fasync) (int, struct file *, int);
    int (*lock) (struct file *, int, struct file_lock *);
    ssize_t (*sendpage) (struct file *, struct page *, int, size_t, loff_t *, int);
    unsigned long (*get_unmapped_area)(struct file *, unsigned long, unsigned long, unsigned long, unsigned long);
    int (*check_flags)(int);
    int (*flock) (struct file *, int, struct file_lock *);
    ssize_t (*splice_write)(struct pipe_inode_info *, struct file *, loff_t *, size_t, unsigned int);
    ssize_t (*splice_read)(struct file *, loff_t *, struct pipe_inode_info *, size_t, unsigned int);
    int (*setlease)(struct file *, long, struct file_lock **, void **);
    long (*fallocate)(struct file *file, int mode, loff_t offset,
              loff_t len);
    void (*show_fdinfo)(struct seq_file *m, struct file *f);
#ifndef CONFIG_MMU
    unsigned (*mmap_capabilities)(struct file *);
#endif
    ssize_t (*copy_file_range)(struct file *, loff_t, struct file *,
            loff_t, size_t, unsigned int);
    int (*clone_file_range)(struct file *, loff_t, struct file *, loff_t,
            u64);
    ssize_t (*dedupe_file_range)(struct file *, u64, u64, struct file *,
            u64);
};
```

e.g:
```
static struct file_operations fops =
{
.owner          = THIS_MODULE,
.read           = etx_read,
.write          = etx_write,
.open           = etx_open,
.release        = etx_release,
};
```


#### File Operations

This is just a basic linux device driver which explains about the file operations.

Please update your Beaglebone board's kernel directory in the Makefile.

Build for Beaglebone:
	sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-

Build for Raspberry Pi or Virtualbox Ubuntu:
	sudo make

Please refer this URL for the complete tutorial of this example source code.
https://embetronicx.com/tutorials/linux/device-drivers/cdev-structure-and-file-operations-of-character-drivers/

You can check the video tutorial of this example here (https://youtu.be/20dQsadVdII).

The Linux Device Driver Video Playlist - https://www.youtube.com/watch?v=BRVGchs9UUQ&list=PLArwqFvBIlwHq8WMKgsXSQdqIvymrEz9k

How to Setup Ubuntu and Raspberry PI - https://www.youtube.com/watch?v=e6gNeje3ljA
How to Setup BeagleBone and Cross compile the kernel - https://www.youtube.com/watch?v=am-dgmrMgYY&t 
