---
title: "Java"
draft: true
---

In computer science, garbage collection (GC) is a form of automatic memory management. The garbage collector, or just collector, attempts to reclaim garbage, or memory occupied by objects that are no longer in use by the program. Garbage collection may take a significant proportion of total processing time in a program and, as a result, can have significant influence on performance, possible stalls in program execution, and incompatibility with manual resource management.

Resources other than memory, such as network sockets, database handles, user interaction windows, file and device descriptors, are not typically handled by garbage collection. Methods for managing such resources, particularly destructors, may suffice to manage memory as well, leaving no need for GC. Some GC systems allow such other resources to be associated with a region of memory that, when collected, causes the work of reclaiming these resources.

The basic principles of garbage collection are to find data objects in a program that cannot be accessed in the future, and to reclaim the resources used by those objects.

- In C/C++, programmer is responsible for both creation and destruction of objects. Usually programmer neglects destruction of useless objects. Due to this negligence, at certain point, for creation of new objects, sufficient memory may not be available and entire program will terminate abnormally causing OutOfMemoryErrors.
- But in Java, the programmer need not to care for all those objects which are no longer in use. Garbage collector destroys these objects.
- Garbage collector is best example of Daemon thread as it is always running in background.
- Main objective of Garbage Collector is to free heap memory by destroying unreachable objects.

Eligibility for garbage collection : 
- An object is said to be eligible for GC(garbage collection) iff it is unreachable.

- Even though the programmer is not responsible to destroy useless objects but it is highly recommended to make an object unreachable(thus eligible for GC) if it is no longer required.
There are generally four different ways to make an object eligible for garbage collection.
  1) Nullifying the reference variable
  2) Re-assigning the reference variable
  3) Object created inside method
  4) Island of Isolation

- Once we made object eligible for garbage collection, it may not destroy immediately by the garbage collector. Whenever JVM runs the Garbage Collector program, then only the object will be destroyed. But when JVM runs Garbage Collector, we can not expect.

- We can also request JVM to run Garbage Collector. There are two ways to do it :
  1) Using System.gc() method : 
      System class contain static method gc() for requesting JVM to run Garbage Collector.
  2) Using Runtime.getRuntime().gc() method : 
      Runtime class allows the application to interface with the JVM in which the application is running. Hence by using its gc() method, we can request JVM to run Garbage Collector.