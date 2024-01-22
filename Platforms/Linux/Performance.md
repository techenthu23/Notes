---
layout: default
title:  "Red Hat Performance Management"
---

- [Terms](#terms)
  - [Run Queue Statistics](#run-queue-statistics)
  - [Context Switches](#context-switches)
  - [Interrupts](#interrupts)
  - [CPU Utilization](#cpu-utilization)
- [Performance Investigation](#performance-investigation)
  - [Finding a Metric, Baseline, and Target](#finding-a-metric-baseline-and-target)
  - [Grabbing the Low-Hanging Fruit](#grabbing-the-low-hanging-fruit)
  - [Track Down the Approximate Problem](#track-down-the-approximate-problem)
- [Performance Issues](#performance-issues)
  - [High number of context switching and interrupt rate on Linux box](#high-number-of-context-switching-and-interrupt-rate-on-linux-box)
    - [Diagnostic Steps](#diagnostic-steps)

# Terms

## Run Queue Statistics

In Linux, a process can be either runnable or blocked waiting for an event to complete. A blocked process may be waiting for data from an I/O device or the results of a system call. If a process is runnable, that means that it is competing for the CPU time with the other processes that are also runnable. A runnable process is not necessarily using the CPU, but when the Linux scheduler is deciding which process to run next, it picks from the list of runnable processes. When these processes are runnable, but waiting to use the processor, they form a line called the run queue. The longer the run queue, the more processes wait in line.

The performance tools commonly show the number of processes that are runnable and the number of processes that are blocked waiting for I/O. Another common system statistic is that of load average. The load on a system is the total amount of running and runnable process. For example, if two processes were running and three were available to run, the system's load would be five. The load average is the amount of load over a given amount of time. Typically, the load average is taken over 1 minute, 5 minutes, and 15 minutes. This enables you to see how the load changes over time.

## Context Switches

Most modern processors can run only one process or thread at a time. Although some processors, such hyperthreaded processors, can actually run more than one process simultaneously, Linux treats them as multiple single-threaded processors. To create the illusion that a given single processor runs multiple tasks simultaneously, the Linux kernel constantly switches between different processes. The switch between different processes is called a context switch, because when it happens, the CPU saves all the context information from the old process and retrieves all the context information for the new process. The context contains a large amount of information that Linux tracks for each process, including, among others, which instruction the process is executing, which memory it has allocated, and which files the process has open. Switching these contexts can involve moving a large amount of information, and a context switch can be quite expensive. It is a good idea to minimize the number of context switches if possible.

multitasking is realized by means of systematic context switching between competing applications and time slicing the processor.

To avoid context switches, it is important to know how they can happen. First, context switches can result from kernel scheduling. To guarantee that each process receives a fair share of processor time, the kernel periodically interrupts the running process and, if appropriate, the kernel scheduler decides to start another process rather than let the current process continue executing. It is possible that your system will context switch every time this periodic interrupt or timer occurs. The number of timer interrupts per second varies per architecture and kernel version. One easy way to check how often the interrupt fires is to use the /proc/interrupts file to determine the number of interrupts that have occurred over a known amount of time. This is demonstrated below

```
$ cat /proc/interrupts | grep timer

; sleep 10 ; cat /proc/interrupts | grep timer
  0:   24060043          XT-PIC  timer
  0:   24070093          XT-PIC  timer
```

In above listing, we ask the kernel to show us how many times the timer has fired, wait 10 seconds, and then ask again. That means that on this machine, the timer fires at a rate of (24,070,093 – 24,060,043) interrupts / (10 seconds) or ~1,000 interrupts/sec. If you have significantly more context switches than timer interrupts, the context switches are most likely caused by an I/O request or some other long-running system call (such as a sleep). When an application requests an operation that can not complete immediately, the kernel starts the operation, saves the requesting process, and tries to switch to another process if one is ready. This allows the processor to keep busy if possible.

You can also find out the number of context switches that system underwent by referring field ctxt in /proc/stat

“context switch” is in fact a broader term. It applies to any two threads of execution that share the same processor, even if one is not associated with an application per-se. Specifically, this is the case when a hardware interrupt fires: the operating system temporarily suspends the currently running application, switches to kernel space, and invokes the interrupt handler (during which the kernel is said to be running in “interrupt context”). When the handler finally terminates, the operating system returns to user space and restores user context.

Read: [The Context-Switch Overhead Inflicted by Hardware Interrupts (and the Enigma of Do-Nothing Loops)](https://www.usenix.org/legacy/events/expcs07/papers/4-tsafrir.pdf)

## Interrupts

In addition, periodically, the processor receives an interrupt by hardware devices. These interrupts are usually triggered by a device that has an event that needs to be handled by the kernel. For example, if a disk controller has just finished retrieving a block from the drive and is ready for the kernel to use it, the disk controller may trigger an interrupt. For each interrupt the kernel receives, an interrupt handler is run if it has been registered for that interrupt; otherwise, the interrupt is ignored. These interrupt handlers run at a very high priority in the system and typically execute very quickly. Sometimes, the interrupt handler has work that needs to be done, but does not require the high priority, so it launches a "bottom half," which is also known as a soft-interrupt handler. If there are a high number of interrupts, the kernel can spend a large amount of time servicing these interrupts. The file /proc/interrupts can be examined to show which interrupts are firing on which CPUs.

## CPU Utilization

CPU utilization is a straightforward concept. At any given time, the CPU can be doing one of seven things. First, it can be idle, which means that the processor is not actually doing any work and is waiting for something to do. Second, the CPU can be running user code, which is specified as "user" time. Third, the CPU can be executing code in the Linux kernel on behalf of the application code. This is "system" time. Fourth, the CPU can be executing user code that has been "nice"ed or set to run at a lower priority than normal processes. Fifth, the CPU can be in iowait, which mean the system is spending its time waiting for I/O (such as disk or network) to complete. Sixth, the CPU can be in irq state, which means it is in high-priority kernel code handling a hardware interrupt. Finally, the CPU can be in softirq mode, which means it is executing kernel code that was also triggered by an interrupt, but it is running at a lower priority (the bottom-half code). This can happen when a device interrupt occurs, but the kernel needs to do some work with it before it is ready to hand it over to user space (for example, with a network packet).

Most performance tools specify these values as a percentage of the total CPU time. These times can range from 0 percent to 100 percent, but all three total 100 percent. A system with a high "system" percentage is spending most of its time in the kernel. Tools such as oprofile can help determine where this time is being spent. A system that has a high "user" time spends most of its time running applications. The next chapter shows how to use performance tools to track down problems in these cases. If a system is spending most of its time iowait when it should be doing work, it is most likely waiting for I/O from a device. It may be a disk, network card, or something else causing the slowdown.

# Performance Investigation

http://books.gigatux.nl/mirror/linuxperformanceguide/0131486829/ch01lev1sec2.html

This section outlines a series of essential steps as you begin a performance investigation. Because the ultimate goal is to fix the problem, the best idea is to research the problem before you even touch a performance tool. Following a particular protocol is an efficient way to solve your problem without wasting valuable time.

## Finding a Metric, Baseline, and Target
The first step in a performance investigation is to determine the current performance and figure out how much it needs to be improved. If your system is significantly underperforming, you may decide that it is worth the time to investigate. However, if the system is performing close to its peak values, it might not be worth an investigation. Figuring out the peak performance values helps you to set reasonable performance expectations and gives you a performance goal, so that you know when to stop optimizing. You can always tweak the system just a little more, and with no performance target, you can waste a lot of time squeezing out that extra percent of performance, even though you may not actually need it.

1) **Establish a Metric**

To figure out when you have finished, you must create or use an already established metric of your system's performance. A metric is an objective measurement that indicates how the system is performing. 
For example, if you are optimizing a Web server, you could choose "serviced Web requests per second." If you do not have an objective way to measure the performance, it can be nearly impossible to determine whether you are making any progress as you tune the system.

2) **Establish a Baseline**

After you figure out how you are going to measure the performance of a particular system or application, it is important to determine your current performance levels. Run the application and record its performance before any tuning or optimization; this is called the baseline value, and it is the starting point for the performance investigation.

3) **Establish a Target**

After you pick a metric and baseline for the performance, it is important to pick a target. This target guides you to the end of the performance hunt. You can indefinitely tweak a system, and you can always get it just a little better with more and more time. If you pick your target, you will know when have finished. To pick a reasonable goal, the following are good starting points:

- *Find others with a similar configuration and ask for their performance measurements*

This is an ideal situation. If you can find someone with a similar system that performs better, not only will you be able to pick a target for your system, you may also be able to work with that person to determine why your configuration is slower and how your configurations differ. Using another system as a reference can prove immensely useful when investigating a problem.

- *Find results of industry standard benchmarks*

Many Web sites compare benchmark results of various aspects of computing systems. Some of the benchmark results can be achieved only with a heroic effort, so they might not represent realistic use. However, many benchmark sites have the configuration used for particular results. These configurations can provide clues to help you tune the system.

- *Use your hardware with a different OS or application*

It may be possible to run a different application on your system with a similar function. For example, if you have two different Web servers, and one performs slowly, try a different one to see whether it performs any better. Alternatively, try running the same application on a different operating system. If the system performs better in either of these cases, you know that your original application has room for improvement.

If you use existing performance information to guide your target goal, you have a much better chance of picking a target that is aggressive but not impossible to reach.

## Grabbing the Low-Hanging Fruit
Another approach to the performance hunt is pick a certain amount of time for the hunt and, instead of picking a target, optimize as much as possible within that time period. If an application has never been optimized for a given workload, it often has a few problems with relatively easy fixes. These easy fixes are called the "low-hanging fruit."

- Why "low-hanging fruit"? 
  
  An analogy to a performance investigation is to imagine that you were hungry and standing at the base of an apple tree. You would likely grab for the apple closest to the ground and easiest for you to reach. These low-hanging apples will satisfy your hunger just as well as the harder-to-reach apples farther up the tree; however, picking them requires much less work. Similarly, if you are optimizing an application in a limited amount of time, you might just try to fix the easiest and obvious problems (low-hanging fruit) rather than making some of the more difficult and fundamental changes.

## Track Down the Approximate Problem
Use the performance tools to take a first cut at determining the cause of the problem. By taking an initial rough cut at the problem, you get a high-level idea of the problem. The goal of the rough cut is to gather enough information to pass along to the other users and developers of this program, so that they can provide advice and tips. It is vitally important to have a well-written explanation of what you think the problem might be and what tests led you to that conclusion.

1.2.3. See Whether the Problem Has Already Been Solved
Your next goal should be to determine whether others have already solved the problem. A performance investigation can be a lengthy and time-consuming affair. If you can just reuse the work of others, you will be done before you start. Because your goal is simply to improve the performance of the system, the best way to solve a performance problem is to rely on what someone else has already done.

Although you must take specific advice regarding performance problems with a grain of salt, the advice can be enlightening, enabling you to see how others may have investigated a similar problem, how they tried to solve the problem, and whether they succeeded.

Here are a few different places to look for performance advice:

Search the Web for similar error messages/problems— This is usually my first line of investigation. Web searches often reveal lots of information about the application or the particular error condition that you are seeing. They can also lead to information about another user's attempt to optimize the systems, and possibly tips about what worked and what did not. A successful search can yield pages of information that directly applies to your performance problem. Searching with Google or Google groups is a particularly helpful way to find people with similar performance problems.

Ask for help on the application mailing lists— Most popular or publicly developed software has an e-mail list of people who use that software. This is a perfect place to find answers to performance questions. The readers and contributors are usually experienced at running the software and making it perform well. Search the archive of the mailing list, because someone may have asked about a similar problem. Subsequent replies to the original message might describe a solution. If they do not, send an e-mail to the person who originally wrote about the problem and ask whether he or she figured out how to resolve it. If that fails, or no one else had a similar problem, send an e-mail describing your problem to the list; if you are lucky, someone may have already solved your problem.

Send an e-mail to the developer— Most Linux software includes the e-mail address of the developer somewhere in the documentation. If an Internet search and the mailing list fails, you can try to send an e-mail to the developer directly. Developers are usually very busy, so they might not have time to answer. However, they know the application better than anyone else. If you can provide the developer with a coherent analysis of the performance problem, and are willing to work with the developer, he or she might be able to help you. Although his idea of the cause of the performance problem might not be correct, the developer might point you in a fruitful direction.

Talk to the in-house developers— Finally, if this is a product being developed in-house, you can call or e-mail the in-house developers. This is pretty much the same as contacting the external developers, but the in-house people might be able to devote more time to your problem or point you to an internal knowledge base.

By relying on the work of others, you might be able to solve your problem before you even begin to investigate. At the very least, you will most likely be able to find some promising avenues to investigate, so it is always best to see what others have found.

1.2.4. The Case Begins (Start to Investigate)
Now that you have exhausted the possibility of someone else solving the problem, the performance investigation must begin. Later chapters describe the tools and methods in detail, but here are a few tips to make things work better:

Isolate the problem— If at all possible, eliminate any extraneous programs or applications that are running on the system you are investigating. A heavily loaded system with many different running applications can skew the information gathered by the performance tools and ultimately lead you down false paths.

Use system difference to find causes— If you can find a similar system that performs well, it can be a powerful aid in debugging your problem. One of the problems of using performance tools is that you do not necessarily have a good way of knowing whether the results from a performance tool indicate a problem. If you have a good system and a bad one, you can run the same performance tool on both systems and compare the results. If the results differ, you might be able to determine the cause of the problem by figuring out how the systems differ.

Change one thing at a time— This very important. To really determine where the problem lies, you should only make one change at a time. This might be time-consuming and cause you to run many different tests, but it is really the only way to figure out whether you have solved the problem.

Always remeasure after optimizing— If you are tweaking a system, it is important to remeasure everything after you change something. When you start modifying the system configuration, all the performance information that you previously generated might not be valid anymore. Usually, as you solve a performance problem, others jump in to take its place. The new problems may be very different from the old problems, so you really have to rerun your performance tools to make sure you are investigating the right problem.

Following these tips can help you avoid false leads and help to determine the cause of a performance problem.

1.2.5. Document, Document, Document
As mentioned previously, it is really important to document what you are doing so that you can go back at a later date and review it. If you have hunted down the performance problem, you will have a big file of notes and URLs fresh in your mind. They may be a jumbled disorganized mess, but as of now, you understand what they mean and how they are organized. After you solve the problem, take some time to rewrite what you have discovered and why you think that it is true. Include performance results that you've measured and experiments that you've done. Although it might seem like a lot of work, it can be very valuable. After a few months, it is easy to forget all the tests that you have done, and if you do not write down your results, you may end up redoing it. If you write a report when the tests are fresh in your mind, you will not have to do the work again, and can instead just rely on what you have written down.

Summary
Hunting a performance problem should be a satisfying and exciting process. If you have a good method in place to research and analyze, it will be repaid back many times as you hunt the problem. First, determine whether other people have had similar problems; if they have, try their solutions. Be skeptical of what they tell you, but look for others with experience of a similar problem. Create a reasonable metric and target for your performance hunt; the metric enables you to know when you have finished. Automate performance tests. Be sure to save test results and configuration information when you generate them so that you can review the results later. Keep your results organized and record any research and other information that you find that relates to your problem. Finally, periodically review your notes to find information that you might have missed the first time. If you follow these guidelines, you will have a clear goal and a clear procedure to investigate the problem.

This chapter provided a basic background for a performance investigation, and the following chapters cover the Linux-specific performance tools themselves. You learn how to use the tools, what type of information they can provide, and how to use them in combination to find performance problems on a particular system


# Performance Issues

## High number of context switching and interrupt rate on Linux box

- A context switch is described as the kernel suspending execution of one process on the CPU and resuming execution of some other process that had previously been suspended. A context switch is required for every interrupt and every task that the scheduler picks.
- Context switching can be due to multitasking, Interrupt handling , user & kernel mode switching. The interrupt rate will naturally go high, if there is higher network traffic, or higher disk traffic. Also it is dependent on the application which every now and then invoking system calls.
- If the cores/CPU's are not sufficient to handle load of threads created by application will also result in context switching.
- It is not a cause of concern until performance breaks down. This is expected that CPU will do context switching. One shouldn't verify these data at first place since there are many statistical data which should be analyzed prior to looking into kernel activities. Verify the CPU, memory and network usage during this time. Sar utility will provide these data.

### Diagnostic Steps

Collect following output to check which process is causing issue

```bash
pidstat -w 3 10   > /tmp/pidstat.out   # This will provide you process PID
pidstat -wt 3 10  > /tmp/pidstat-t.out # This will provide you Thread ID
```

Then with help of PID which is causing issue, one can get all system calls details:

```bash
strace -c -f -p <pid of process/thread>
```

Let this command run for a few minutes while the load/context switch rates are high. It is safe to run this on a production system so you could run it on a good system as well to provide a comparative baseline. Through strace, one can debug & troubleshoot the issue, by looking at system calls the process has made.


```
Performance tuning is about achieving balance between the different sub-systems of an OS. These  
sub-systems include:  
• CPU  
• Memory  
• IO  
• Network  
  
These sub-systems are all highly dependent on each other. Any one of them with high utilization can easily cause problems in the other. For example:  
• large amounts of page-in IO requests can fill the memory queues  
• full gigabit throughput on an Ethernet controller may consume a CPU  
• a CPU may be consumed attempting to maintain free memory queues  
• a large number of disk write requests from memory may consume a CPU and IO channels&nbsp;  
  
In order to apply changes to tune a system, the location of the bottleneck must be located. Although one subsystem appears to be causing the problems, it may be as a result of overload on another sub-system.&nbsp;

## Determining Application Type

In order to understand where to start looking for tuning bottlenecks, it is first important to understand the behavior of the system under analysis. The application stack of any system is often broken down into two types:  
  
• IO Bound – An IO bound application requires heavy use of memory and the underlying storage system. This is due to the fact that an IO bound application is processing (in memory) large amounts of data. An IO bound application does not require much of the CPU or network (unless the storage system is on a network). IO bound applications use CPU resources to make IO requests and then often go into a sleep state. Database applications are often considered IO bound applications.  
  
• CPU Bound – A CPU bound application requires heavy use of the CPU. CPU bound applications require the CPU for batch processing and/or mathematical calculations. High volume web servers, mail servers, and any kind of rendering server are often considered CPU bound applications.

## Determining Baseline Statistics

The only way to understand if a system is having performance issues is to understand what is expected of the system. What kind of performance should be expected and what do those numbers look like? The only way to establish this is to create a baseline. Statistics must be available for a system under acceptable performance so it can be compared later against unacceptable performance.

### CPU Terminology

The utilization of a CPU is largely dependent on what resource is attempting to access it. The kernel has a scheduler that is responsible for scheduling two kinds of resources: threads (single or multi) and interrupts. The scheduler gives different priorities to the different resources. The following list outlines the priorities from highest to lowest:  
• Hardware Interrupts – These are requests made by hardware on the system to process data. For example, a disk may signal an interrupt when it has completed and IO transaction or a NIC may signal that a packet has been received.  
• Soft Interrupts – These are kernel software interrupts that have to do with maintenance of the kernel. For example, the kernel clock tick thread is a soft interrupt. It checks to make sure a process has not passed its allotted time on a processor.  
• Real Time Threads – Real time threads have more priority than the kernel itself. A real time process may come on the CPU and preempt (or “kick off) the kernel. The Linux 2.4 kernel is NOT a fully preemptable kernel, making it not ideal for real time application programming.  
• Kernel Threads – All kernel processing is handled at this level of priority.  
• User Threads – This space is often referred to as “userland”. All software applications run in the user space. This space has the lowest priority in the kernel scheduling mechanism.&nbsp;  
  
  
In order to understand how the kernel manages these different resources, a few key concepts need to be introduced. The following sections introduce context switches, run queues, and utilization.&nbsp;

#### Context Switches

Most modern processors can only run one process (single threaded) or thread at time. The n-way Hyper threaded processors have the ability to run n threads at a time. Still, the Linux kernel views each processor core on a dual core chip as an independent processor. For example, a system with one dual core processor is reported as two individual processors by the Linux kernel.  
  
A standard Linux kernel can run anywhere from 50 to 50,000 process threads at once. With only one CPU, the kernel has to schedule and balance these process threads. Each thread has an allotted time quantum to spend on the processor. Once a thread has either passed the time quantum or has been preempted by something with a higher priority (a hardware interrupt, for example), that thread is place back into a queue while the higher priority thread is placed on the processor. This switching of threads is referred to as a context switch.  
  
  
Every time the kernel conducts a context switch, resources are devoted to moving that thread off of the CPU registers and into a queue. **T_he higher the volume of context switches on a system, the more work the kernel has to do in order to manage the scheduling of processes._**

#### The Run Queue

Each CPU maintains a run queue of threads. Ideally, the scheduler should be constantly running and executing threads. Process threads are either in a sleep state (blocked and waiting on IO) or they are runnable. If the CPU sub-system is heavily utilized, then it is possible that the kernel scheduler can’t keep up with the demand of the system. As a result, runnable processes start to fill up a run queue.  
  
The larger the run queue, the longer it will take for process threads to execute.  
  
A very popular term called “load” is often used to describe the state of the run queue. The system load is a combination of the amount of process threads currently executing along with the amount of threads in the CPU run queue. If two threads were executing on a dual core system and 4 were in the run queue, then the load would be 6. Utilities such as top report load averages over the course of 1,5, and 15 minutes.

#### CPU Utilization

CPU utilization is defined as the percentage of usage of a CPU. How a CPU is utilized is an important metric for measuring system. Most performance monitoring tools categorize CPU utilization into the following categories:  
• User Time – The percentage of time a CPU spends executing process threads in the user space.  
• System Time – The percentage of time the CPU spends executing kernel threads and interrupts.  
• Wait IO – The percentage of time a CPU spends idle because ALL process threads are blocked waiting for IO requests to complete.&nbsp;  
  
• Idle – The percentage of time a processor spends in a completely idle state.

#### Time Slicing

The timeslice2 is the numeric value that represents how long a task can run until it is preempted. The scheduler policy must dictate a default timeslice, which is not simple.&nbsp;

 A timeslice that is too long will cause the system to have poor interactive performance; the system will no longer feel as if applications are being concurrently executed.&nbsp;

 A timeslice that is too short will cause significant amounts of processor time to be wasted on the overhead of switching processes, as a significant percentage of the system's time will be spent switching from one process with a short timeslice to the next.&nbsp;

 Furthermore, the conflicting goals of I/O-bound versus processor-bound processes again arise; I/O-bound processes do not need longer timeslices, whereas processor-bound processes crave long timeslices (to keep their caches hot, for example).

  

**Priorities**

A common type of scheduling algorithm is priority-based scheduling. The idea is to rank processes based on their worth and need for processor time. Processes with a higher priority will run before those with a lower priority, while processes with the same priority are scheduled round-robin (one after the next, repeating). On some systems, Linux included, processes with a higher priority also receive a longer timeslice. The runnable process with timeslice remaining and the highest priority always runs. Both the user and the system may set a processes priority to influence the scheduling behavior of the system.
```