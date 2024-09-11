<style>
b { color: blue }
</style>


# SYSFS
The operating system segregates virtual memory into kernel space and userspace. Kernel space is strictly reserved for running the kernel, kernel extensions, and most device drivers. In contrast, user space is the memory area where all user-mode applications work, and this memory can be swapped out when necessary.

Sysfs is the virtual filesystem mounted as `/sys`
The directories in Sysfs contain the hierarchy of devices, as they are attached to the computer.  

Sysfs is commonly used to export the device information from kernel space to the user space

The heart of the sysfs model is the kernel object. Kobject is the glue that binds the sysfs and the kernel, which is represented by struct kobject and defined in <b><linux/kobject.h></b>. A struct kobject represents a kernel object, maybe a device or so, such as the things that show up as a directory in the sysfs filesystem

```c
#define KOBJ_NAME_LEN 20 
/*
struct kobject
|– name (Name of the kobject. Current kobject is created with this name in sysfs.)
|– parent (This is kobject’s parent. When we create a directory in sysfs for the current kobject, it will create under this parent directory)
|– ktype (the type associated with a kobject)
|– kset (a group of kobjects all of which are embedded in structures of the same type)
|– sd (points to a sysfs_dirent structure that represents this kobject in sysfs.)
|– kref (provides reference counting)

*/
struct kobject {
 char *k_name;
 char name[KOBJ_NAME_LEN];
 struct kref kref;
 struct list_head entry;
 struct kobject *parent;
 struct kset *kset;
 struct kobj_type *ktype;
 struct dentry *dentry;
};
```
# Sysfs in linux driver  

* Creating and using the sysfs   
    1. Create a directory in /sys
    we can use the following function to create a directory in sysfs :
    ```c
    /*
    @about funtion  :  This function creates a kobject structure dynamically and registers it with sysfs. If the kobject was not able to be created, NULL will be returned.

    <name> – the name for the kobject

    <parent> – the parent kobject of this kobject, if any.
    */
    struct kobject * kobject_create_and_add ( const char * name, struct kobject * parent);
    /* so if NULL is passed to the second argument then file is created on /sys/ 
          if the frimweare_kobj is passed to the second arg. the  file is created in /sys/firmware/ */


    ```
    call `kobject_put` and the structure will be dynamically freed when it is no longer being used.

    2. Create Sysfs file
        * Create Attribute
         kobj attribute defined as 
         ```c
         struct kobj_attribute {
                struct attribute attr;
                ssize_t (*show)(struct kobject *kobj, struct kobj_attribute *attr, char *buf);
                ssize_t (*store)(struct kobject *kobj, struct kobj_attribute *attr, const char *buf, size_t count);
                };
         ```
        We can create an attribute using __ATTR macro.

        `__ATTR(name, permission, show_ptr, store_ptr);`

        * the n create a sysfs file 
         ```c
         int sysfs_create_file ( struct kobject *  kobj, const struct attribute * attr);
         ```

        One can use another function ‘ sysfs_create_group ‘ to create a group of attributes.

        Once you have done with the sysfs file, you should delete this file using sysfs_remove_file

    ``` c
    void sysfs_remove_file ( struct kobject *  kobj, const struct attribute * attr);
    ```

# Testing 


# REFER

This is just a basic linux device driver which explains about the sysfs in linux device driver.

Please refer this URL for the complete tutorial of this example source code.
https://embetronicx.com/tutorials/linux/device-drivers/sysfs-in-linux-kernel/



