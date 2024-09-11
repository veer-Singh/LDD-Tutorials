<style>
g { color: green }
</style>
# PROCFS
In the linux root have folder <g>proc</g>. This folder is a mount point for the procfs (Process Filesystem) which is a filesystem in memory.

It can act as a bridge connecting the user space and the kernel space. Userspace programs can use proc files to read the information exported by the kernel. Every entry in the proc file system provides some information from the kernel.

<g>___meminfo___</g>  gives the details of the memory being used in the system.  
<g>___module___</g> gives the detail of the module currently the part of the kernel.

<g>/proc/devices</g> — registered character and block major numbers  
<g>/proc/ioports</g> — on-system I/O port addresses (especially for x86 systems)  
<g>/proc/iomem</g> — on-system physical RAM and bus device addresses  
<g>/proc/interrupts</g> — registered interrupt request numbers  
<g>/proc/softirqs</g> — registered soft IRQs  
<g>/proc/swaps</g> — currently active swaps  
<g>/proc/kallsyms</g> — running kernel symbols, including from loaded modules  
<g>/proc/partitions</g> — currently connected block devices and their partitions  
<g>/proc/filesystems</g> — currently active filesystem drivers  
<g>/proc/cpuinfo</g> — information about the CPU(s) on the system  


The proc file system is also very useful when we want to debug a kernel module. While debugging we might want to know the values of various variables in the module or maybe the data that the module is handling. In such situations, we can create a proc entry for ourselves and dump whatever data we want to look into in the entry.
proc files can also be used to control and modify kernel behavior on the fly. The proc files need to be writable in this case.

The proc entry can also be used to pass data to the kernel by writing into the kernel, so there can be two kinds of proc entries.

1. An entry that only reads data from the kernel space.
2. An entry that reads as well as writes data into and from kernel space.

## Creating procfs directory
```c
struct proc_dir_entry *proc_mkdir(const char *name, struct proc_dir_entry *parent)
/*
name: The name of the directory that will be created under /proc.

parent: In case the folder needs to be created in a subfolder under /proc a pointer to the same is passed else it can be left as NULL.
*/
```

```c
/*
<name>: The name of the proc entry
<mode>: The access mode for proc entry
<parent>: The name of the parent directory under /proc. If NULL is passed as a parent, the /proc directory will be set as a parent.
<proc_fops>: The structure in which the file operations for the proc entry will be created.
*/
struct proc_dir_entry *proc_create ( const char *name, umode_t mode, struct proc_dir_entry *parent, const struct file_operations *proc_fops )
```
**NOTE:**
 The above proc_create is valid in the Linux Kernel v3.10 to v5.5. From v5.6, there is a change in this API. The fourth argument const struct file_operations *proc_fops is changed to const struct proc_ops *proc_ops.  

If you are using the kernel version below 3.10, please use the below functions to create proc entry. then use 
```C
/* header  --> linux/proc_fs.h.*/
create_proc_read_entry()
create_proc_entry() 
```
now create a file_operations structure proc_fs that can create a map function for read and write.
```c
static struct file_operations proc_fops = {
    .open = open_proc,
    .read = read_proc,
    .write = write_proc,
    .release = release_proc
};
```
if using the file linux kernel version 5.10.
```c
static struct proc_ops proc_fops = {
        .proc_open = open_proc,
        .proc_read = read_proc,
        .proc_write = write_proc,
        .proc_release = release_proc
};
```
* Next, we need to add all functions to the driver
# Open and Release function 
These functions are optional

```c
static int open_proc(struct inode *inode, struct file *file)
{
    printk(KERN_INFO "proc file opend.....\t");
    return 0;
}

static int release_proc(struct inode *inode, struct file *file)
{
    printk(KERN_INFO "proc file released.....\n");
    return 0;
}
```
# Write Function  
The write function will receive data from the user space using the function copy_from_user into an array “etx_array”
```c
static ssize_t write_proc(struct file *filp, const char *buff, size_t len, loff_t * off)
{
    printk(KERN_INFO "proc file write.....\t");
    copy_from_user(etx_array,buff,len);
    return len;
}
```

# Read Function
Once data is written to the proc entry we can read from the proc entry using a read function, i.e transfer data to the user space using the function copy_to_user function

```c
static ssize_t read_proc(struct file *filp, char __user *buffer, size_t length,loff_t * offset)
{
    printk(KERN_INFO "proc file read.....\n");
    if(len)
        len=0;
    else{
        len=1;
        return 0;
    }
    copy_to_user(buffer,etx_array,20);

    return length;;
}
```
# Remove Proc Entry
Proc entry should be removed in the Driver exit function using the below function.

```c
void remove_proc_entry(const char *name, struct proc_dir_entry *parent);
```
And you can remove the complete parent directory using `proc_remove(struct proc_dir_entry *parent)`.


## fllow for the code

### Init  
1. Allocate the major and minor number. `alloc_chrdev_region(&dev, 0, 1, "etx_Dev")`
2. Create a cdev structure .`cdev_init(&etx_cdev,&fops); `
3. Add the character device to system. `cdev_add(&etx_cdev,dev,1)`
4. create a class structure. `dev_class = class_create(THIS_MODULE,"etx_class")`
5. create a device using class structure `device_create(dev_class,NULL,dev,NULL,"etx_device")`
6. Create proc directory. It will create a directory under "/proc"  `parent = proc_mkdir("etx",NULL)`
7. Creating Proc entry under "/proc/etx/" `proc_create("etx_proc", 0666, parent, &proc_fops);` 

### Deinit
1. proc_remove(parent);
2. device_destroy(dev_class,dev);
3. class_destroy(dev_class);
4. cdev_del(&etx_cdev);
5. unregister_chrdev_region(dev, 1);



# Refer
This is just a basic linux device driver which explains about the procfs.

Please refer this URL for the complete tutorial of this example source code.
https://embetronicx.com/tutorials/linux/device-drivers/procfs-in-linux/