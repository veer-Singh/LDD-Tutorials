<style>
o { color: orange }
y { color: yellow }
g { color: green }
b { color: blue }
</style>

# IOCTL

There were many ways to communicate b/w user space and kernel space :
* IOCTL --> Input output control
* Procfs
* Sysfs
* Configfs
* Debugfs
* Sysctl
* UDP Sockets
* Netlink Sockets

## IOCTL
The major use of this is in case of handling some specific operations of a device for which the kernel does not have a system call by default.  

There are some steps involved in Implementing IOCTL in Linux Device Drivers-
* Create IOCTL command in the driver
* Write the IOCTL function in the driver
* Create IOCTL command in a Userspace application
* Use the IOCTL system call in a Userspace

## Create IOCTL Command in the Driver
1. define IOCTL command
    ```c
    #define "ioctl name" __IOX("magic number","command number","argument type")
    /*
    where IOX can be :  
    “IO“: an ioctl with no parameters  
    “IOW“: an ioctl with write parameters (copy_from_user)  
    “IOR“: an ioctl with read parameters (copy_to_user)  
    “IOWR“: an ioctl with both write and read parameters  

    The Magic Number is a unique number or character that will differentiate our set of ioctl calls from the other ioctl calls. some times the major number for the device is used here.

    Command Number is the number that is assigned to the ioctl. This is used to differentiate the commands from one another.

    The last is the type of data.
    */
    ```
    ```c++
    #include <linux/ioctl.h>

    #define WR_VALUE _IOW('a','a',int32_t*)
    #define RD_VALUE _IOR('a','b',int32_t*)
    ```
2. Write IOCTL Function in the Driver:  
    implement the IOCTL call we defined into the corresponding driver.
    ```c
    /*
    <inode>: is the inode number of the file being worked on.
    <file>: is the file pointer to the file that was passed by the application.
    <cmd>: is the ioctl command that was called from the userspace.
    <arg>: are the arguments passed from the userspace

    Within the function “ioctl” we need to implement all the commands that we defined above (WR_VALUE, RD_VALUE). We need to use the same commands in the switch statement which is defined above.
    */
    int  ioctl(struct inode *inode,struct file *file,unsigned int cmd,unsigned long arg)
    ```
    Then we need to inform the kernel that the ioctl calls are implemented in the function “etx_ioctl“. This is done by making the fops pointer “unlocked_ioctl” to point to “etx_ioctl” as shown below:  
    ```c
    /*
    ** This function will be called when we write IOCTL on the Device file
    */
    static long etx_ioctl(struct file *file, unsigned int cmd, unsigned long arg)
    {
            switch(cmd) {
                    case WR_VALUE:
                            if( copy_from_user(&value ,(int32_t*) arg, sizeof(value)) )
                            {
                                    pr_err("Data Write : Err!\n");
                            }
                            pr_info("Value = %d\n", value);
                            break;
                    case RD_VALUE:
                            if( copy_to_user((int32_t*) arg, &value, sizeof(value)) )
                            {
                                    pr_err("Data Read : Err!\n");
                            }
                            break;
                    default:
                            pr_info("Default\n");
                            break;
            }
            return 0;
    }

    /*
    ** File operation sturcture
    */
    static struct file_operations fops =
    {
            .owner          = THIS_MODULE,
            .read           = etx_read,
            .write          = etx_write,
            .open           = etx_open,
            .unlocked_ioctl = etx_ioctl,
            .release        = etx_release,
    };
    ```
3. Create IOCTL Command in a Userspace Application  
```c
#define WR_VALUE _IOW('a','a',int32_t*)
#define RD_VALUE _IOR('a','b',int32_t*)
```  
4.  Use IOCTL System Call in Userspace
```c
/*
<file descriptor>: This the open file on which the ioctl command needs to be executed, which would generally be device files.

<ioctl command>: ioctl command which is implemented to achieve the desired functionality

<arguments>: The arguments need to be passed to the ioctl command.
*/
long ioctl( "file descriptor","ioctl command","Arguments");
```



#### IOCTL

This is just a basic linux device driver which explains about the IOCTL.

Please refer this URL for the complete tutorial of this example source code.
https://embetronicx.com/tutorials/linux/device-drivers/ioctl-tutorial-in-linux/