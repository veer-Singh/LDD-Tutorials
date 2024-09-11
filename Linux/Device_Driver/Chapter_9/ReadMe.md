<style>
r {color : red }
b {color: blue }
g {color : green }
o {color: orange }
</style>



# <r>WaitQueue In Linux</r>

Some process need to be wait or sleep for some event. So there were several ways to sleep and waking up in linux and waitqueue is one of them.

Whenever a process must wait for an event (such as the arrival of data or the termination of a process), it should go to sleep. Sleeping causes the process to suspend execution, freeing the processor for other uses.  

After some time, the process will be woken up and will continue with its job when the event which we are waiting for has arrived.

Linux Waitqueue is a mechanism provided in the kernel to implement the wait. As the name itself suggests, waitqueue is the list of processes waiting for an event.

In other words, A wait queue is used to wait for someone to wake you up when a certain condition is true. They must be used carefully to ensure there is no race condition.

There are 3 <o>important</o> steps in Waitqueue.
1. Initializing Waitqueue
2. Queuing (Put the Task to sleep until the event comes)
3. Waking Up Queued Task

Header file used  -->  (`#include /linux/wait.h`)  
There are 2 ways to initialize a wait queue   
1. Static method = `DECLARE_WAIT_QUEUE_HEAD(wq);`
2. Dynamic method 
```c 
wait_queue_head_t wq;
init_waitqueue_head (&wq);
```
### Step 2  ----> QUEUING

1. wait_event : sleep until the event get true , `wait_event(wq, condition);`
2. wait_event_timeout : sleep until a condition gets true or a timeout elapses `wait_event_timeout(wq, condition, timeout);`
3. wait_event_cmd : sleep until a condition gets true
   ```c
   /*wq –  the waitqueue to wait on

    condtion – a C expression for the event to wait for

    cmd1 – the command will be executed before sleep

    cmd2 – the command will be executed after sleep
    */
   wait_event_cmd(wq, condition, cmd1, cmd2);
   ```
4. wait_event_interruptible : sleep until a condition gets true
    ```c
    wait_event_interruptible(wq, condition);
    /*
     * function will return -ERESTARTSYS if it was interrupted by a signal and 0 if condition evaluated to true
     * wq –  the waitqueue to wait on

     * condtion – a C expression for the event to wait for
    */
    ```

5. wait_event_interruptible_timeout: sleep until a condition gets true or a timeout elapses
    ```c
    wait_event_interruptible_timeout(wq, condition, timeout);
    /*
    wq –  the waitqueue to wait on

    condtion – a C expression for the event to wait for

    It returns, 0 if the condition evaluated to false after the timeout elapsed, 1 if the condition evaluated to true after the timeout elapsed, the remaining jiffies (at least 1) if the condition evaluated to true before the timeout elapsed, or -ERESTARTSYS if it was interrupted by a signal.
    */
    ```

6. wait_event_killable : `wait_event_killable(wq, condition);`   
The process is put to sleep (TASK_KILLABLE) until the condition evaluates to true or a signal is received. The condition is checked each time the waitqueue wq is woken up.

The function will return -ERESTARTSYS if it was interrupted by a signal and 0 if condition evaluated to true.

# Wake up
1. wake_up : `wake_up(&wq);`
2. wake_up_all : `wake_up_all(&wq);`
3. wake_up_interruptible : `wake_up_interruptible(&wq);`
4. wake_up_sync and wake_up_interruptible_sync
    ```c
    wake_up_sync(&wq);
    wake_up_interruptible_sync(&wq);
    ```
    sync is used to avoid rescheduling when the current process is known to be going to sleep, thus forcing a reschedule anyway. Note that awakened processes could run immediately on a different processor, so these functions should not be expected to provide mutual exclusion.


# Refer

This is just a basic linux device driver. This will explain waitqueue in the linux device driver.

Please refer this URL for the complete tutorial of this source code.
https://embetronicx.com/tutorials/linux/device-drivers/waitqueue-in-linux-device-driver-tutorial/