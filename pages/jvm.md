---
layout              : page-fullwidth
title               : "JVM Structure"
subheadline         : "Foundation of Java virture machine"
teaser              : "<emJVM</em> is the core concepts in the backend of Java. Understanding the basic of it is one of software engineer's essential requirements."
header:
   image_fullwidth  : "header_roadmap_3.jpg"
permalink           : "/tech/java/JVM"
---

This post demonstrates the basic concept of JVM and memory recycling technique.
<div class="medium-4 medium-push-8 columns" markdown="1">
<div class="panel radius" markdown="1">
**Table of Contents**
{: #toc }
*  TOC
{:toc}
</div>
</div><!-- /.medium-4.columns -->

<div class="medium-8 medium-pull-4 columns" markdown="1">
### JVM Structure

![JVM Structure](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595307735921&di=836279f3f1da9dec5cd15a04defd2b0c&imgtype=0&src=http%3A%2F%2Fimg1.imgtn.bdimg.com%2Fit%2Fu%3D2689548016%2C1335677577%26fm%3D214%26gp%3D0.jpg)
<center>JVM Structure</center>

In the JAVA Virtual Machine, There are mainly 4 parts.

+ **Class Loader**
+ **Execution Engine**
+ **Native Interface**
+ **Runtime Data Area**

### Class Loader

Class Loader is in charge of loading the `class` files. The `class` files are specified by particular file markers at the file header, and **ClassLoader** is only in charge of the loading process of the class files. As for the execution of the file, it is decided by the `Execution Engine`. 

### Native Interface

The function of **Native interface** is to consilidate other programming language to be used by Java, the original intention of it is to fuse C/C++ programs. The invention period of **Java** is the time when C/C++ occupies the programming world. To establish its popularity, it has to be able to call C/C++ programs, thus, there develops an area to cope with the code specified by `native`. The detailed method is to register `native` method in the **Native Method Stack**, and load native libraries when executing the **Executing Engine**.

This method is fading out gradually, unless when coping with the utilization of the haedwares, such as the execution of printing machine by the drive of `Java` and controll the production machines by `Java`, which are rare in today's enterprise application.

### Method Area

Method area is sharing by all the `Threads`, including all the characters all the bytes and some special methods shuch as constructors, besides, `Interfaces` are also defined here. In a nutshell, all the information of the definition of methods are kept in this area, which is a communal space.

### PC Register

Every threads has a program conuter, like a pointer, pointing to the **Method Bytecodes**(The execution code next to be executed), and then the **execution engine** points to the next order, which is a tiny space, almost negligible. 

### Native Method Stack

The execution process is to register the **native methods** in the **Native Method Stack**. When the **Execution Engine** runs, it laods native libraries.S 

### Stack

**Stack** is also called  **Stack Memory** which is in charge of the operation of **Java program**. It is created when the threads are created, and its lifecycle is also followed by the lifecycle of the threads, which means that the **Stack memory is released when the threads are end**. As for the **Stack**， there is not the problem of recycling the memory, it is owned privately by the threads. **Primative variables and the reference of the objects are all allowcated in the function's stack memory.**

The Stack mainly stores three types of data:
+ **Local variables**: Input parameters, output parameters and the variables in the function.
+ **Operand Stack**: Record the operation of **stack input** and **stack output**.
+ **Frame Data**: Includes **class files**, **functions** and so on. 

#### The Principle of Stack

Data in the stack exists as a form of **Stack Frame**. **Stack Frame** is a memory area, which can be described as a data set relating to the data produced by the execution of Methods and runtime data. For example, when a method A is called, it produces a **stack frame** `F1`, and it is pushed into the stack. When method A calls method B, the produced **stack frame** `F2` is also pushed into the **Stack**. When method B calls method C, the produced **Stack Frame** `F3` is also pushed into the **stack**

.....

After the execution, `F3` is firstly poped out of the stack, then `F2`, then, `F1` ...... which follows the principle of FIFO(First in First out).

![Stack Frame](https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3670189158,2104733237&fm=26&gp=0.jpg)
<center>Stack Frame</center>

### Heap

Heap structure:

![Heap](https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2980987,987093251&fm=26&gp=0.jpg)
<center>Heap Sreucture</center>

+ Young Generation Area

As the picture demonstrated above, **Young Generation Area** is the birtn, growth, fade area of a **class**. A **class** is born here, utilized here, and collected by the garbage recycling mechnism in the end and terminated its life. The **Young Generation Area** is divided into two parts: **Eden space** and **Survivor Space**, all the **Classes** are ***new*** in the **Eden Space**. The **Survivor spaces** has two instances: 0 area(**Survivor 0 Space**) and 1 area(**Survivor 1 Space**). When the space in the **Eden Space** runs out, and the program has to create new `objects`, the garbage recycling mechanism in ***JVM0*** will make the garbage recycling in **Eden Space**(Minor GC), and terminate the objects that is on longer referenced by other objects in the **Eden Space**. Then, it will transfer the remaining objects to **Survivor 0 Space**. If unfortunately, the **Survivor 0 Space** is also full, it will make garbage recycling in this area, then move the survivors to the **Survivor 1 area**. What if the **Survivor 1 Space** is also ful? it will transfer the surviving objects to the **Tenured**. Then, if the **Tenured** is also full, the **Major GC(Full GC)** will be generated, and make cleaning in the **Tenured** spaces. If after the **Fiull GC** execution in the **Tenured**  space, it can not save the newly-created objects neither, there will produce a ***OOM*** exception *OutOfMemoryError*.

+ Tenured Generation Area

**Tenured Generation Area** is used to keep the *JAVA* objects filtered in the **Young Generation Area**. 

+ Permanent Area

**Permanent Area** is a space used to store the residnet data, it is ultilized to deposit metadata of `class` and `interface` in the ***JDK***. In other words, it stores the required *class information* when operating the ***JDK*** environment, the data loaded into this area can not be recyled by the garbage collector. Only through shutting down the ***JVM*** can it release the memory it takes.    

+ JDK1.6 & before: There is permanent area
+ JDK1.7: There is permenent area, however, the permanent area has stepped out. The constant pool is in **heap**
+ JDK1.6: There does not exist permanent area. The constant pool is in metaspace.

In terms of actual, **Method area** is like **Heap**, is the memory that threads share, and it is used to store the data JVM loads: metadata + normal consants + static constants + the codes after compilation etc. Although *JVM* describe the **Method Area** as a logical part of the **heap**, it has another name called **Non-Heap**, in order to distinguish with the **Heap**.

**Constant Pool** is a part of **method area**, except the methods, interfaces, fileds, versions that **Class** files contains, another information is constant pool. This part of information will be stored in the **run-time constant pool** after the loading process of class.  

For example, an demo Java program describing the memory distribution is demonstrated below:

![Memory Distribute](https://img-blog.csdnimg.cn/20200731161959837.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70)
<center>Memory Distribution</center>

+ As soon as we run the program, it loads all the **Runtime classes** into the **Heap space**. When `main()` method is found at line 1, *Java* **Runtime** creates stack memory to be used by main() method thead.
+ We are creating primitive local variables at line 2, so it is created and stored in the stack memory of `main()` method.
+ Since we are creating an **Object** in line 3, it's created in **Heap memory** and **Stack Memory** contains the reference for it. Similar process occurs when we create *Memory object* in line 4.
+ Now, when we call `foo()` method in line 5, a **block** in the top of the stack is crteated to be used by foo() method. Since *Java* is passby value, a **new reference** to Object is created in the foo() stack block in line 6.
+ A `String` is created in line7, it goes in the ***String Pool*** in the **heap space** and a **reference** is created in the foo() stack space for it.
+ `foo()` method is terminated in line8, at this time memory block allocated for foo() in stack becomes free.
+ in line 9, main() method terminates and the stack memory created for main() method is destroyed. Also, the program ends at this line. Hence, *Java* **Runtime** frees all the memory and end the execution of the program.  

## Java Garbage Collection 

### Automatic Garbage Collection

 The **automatic garbage collection mechanism** is a process of looking at heap memory, distinguishing between objects in use and unused objects, and deleting unused objects. As for using objects or referencing objects, it means that your program holds a reference to that object. For unused or unreferenced objects, no part of your program holds a reference.Therefore, the memory used by unreferenced objects can be recycled.

 In C - like programming languages, memory allocation and collection are manual.In Java, memory collection is handled automatically by the **garbage collector**.The basic steps can be described as follows:

 + Step 1: Marking

 The first step is marking, which distinguishes which memory is in use and which is not.

 ![step1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2Jsb2cvNDg4NTczLzIwMTMxMi8wNDIyMTA1NS00NzI1MmNmYjNlOGY0MDYwODEyMjQzNTkyYjI0NGE3Ni5qcGc?x-oss-process=image/format,png)
 <center>Marking</center>

 Referenced objects are identified in blue and unreferenced objects in gold. In the **marking** phase, it will scan all objects and determine. If all objects in the system are to be scanned, this step can be time-consuming.

 + Step 2: Deleting normally

 Remove the unreferenced objects and leave the referenced objects and the free pointers.

 ![Normal deletion](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2Jsb2cvNDg4NTczLzIwMTMxMi8wNDIyMTc1My04NWM5ZjdjMjY5ZDE0MGNkYjNiYmQyMDJmZmNkZjEzMi5qcGc?x-oss-process=image/format,png)
 <center>Normal Deletion</center>

 The memory allocator holds references to free memory, which is linked to a List and can be allocated to new objects when needed.

 + Step 3: Deleting With Compacting

 To further improve performance, in addition to removing unreferenced objects, users can also **compress live referenced objects**. Moving reference objects together makes it faster and easier to allocate new memory.

 ![Deleting with compacting](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2Jsb2cvNDg4NTczLzIwMTMxMi8wNDIyMjI1MS04YTAyZDQyNjk3NDY0NTY0OGE2MWY0NDRhODQ0YjNlNi5qcGc?x-oss-process=image/format,png)
 <center>Deleting With Compacting</center>

### Steps of GC (Garbage Collection)

When `JVM` make GC process, it usually does not apply this mechanism in **Tenured Area** and **Permanent Area**. Almost all the GC process plays its role in **Eden Area**. Thus, GC are divided into two kinds, one is **Minor GC** and another one is **Major GC** or (Full GC).

+ Minor GC: Only executes in the **Eden Area**.

+ Major GC: Aiming at **Tenured Area**, constantly Eden Area GC and Permanent GC.

#### Minor GC

For Minor GC, it will transfer all the living objects to the **Survivor Space**. If the **Survivor Space** cannot hold the objects, the remaining objects will be trandfered into the **Old Generation**. Once the collection has finished, **Eden Area** becomes empty.

+ When the objects are created in the **Eden Area**(Including a **Survivor space**, here refer to it as **From space**), after a **Minor GC**, if the objects are living, and can be contained in another **Survivor Space**(here refer to it as **To Space**, and **To Space** had enough memory to hold the living objects from **Edne Area** and **From Space**),`JVM` can use copying algorithm to copy these living objects to another **Survivor Space**(To Space) and clean the used **Eden Area** and **Survivor Space**(From Space). Then, the age of living instances will be set to 1. From now on, once the objects make through a **Minor GC** its age will be increased by 1 and when its age reaches a threshold(Defult 15, can be configured through `-XX:MaxTenuringThreshold`), these objects can be refered to as *Old Generation*.  

+ HotSpot JVM divides the **Young Generation** into three parts: one **Eden Area** and two **Survivor Spaces**(called **From Space** and **To Space**). The defult ratio of these parts are 8:1:1. Under normal conditon, the newly-created objects can be allowcated into **Eden Area**. After one **Minor GC** operation, if these instances are living, they will be moved to **Survivor Space**. The objects will increase its age by 1 if it make through a **Minor GC**, and when the age reaches a certain degree, it will be transfered into **Old Generation**. Due to the fact that almost all the `Objects` fade away after its creation(Over 80%), the GC algorithm in **Young Generation is `Copying`**. The fundamental thoughts of **`Copying ` Algorithm** is to divide the memory into two parts, every time only use one of them, when the memory runs out, copy the living `objects` over another piece of memory. **Copying Algorithm does not produce fragment**.

+ When the **Minor GC** begins, objects can only exist in the **Survivor space** named **From**, and the space in the **To** are empty. Then, after one GC, the living objects in the **Eden Area** are all copied into **To**, while in the **From**, the living objects decide their next pace according to their age. If their age reach a certain threshold, they will be transfered in to the **Old Generation Area**, and the ones whose age do not reach the threshold will be copied into **To**. After this GC operation, **Eden Area** and **From** are all cleared, meanwile, **From** and **To** will change their character, in other words, the *new **To** (The **From** before the last GC) will become **From**(The **To** before the last GC)*. No matter under what condition, the space named **To** will always be kept empty. **Minor GC** will repete these process untill **To** is full. After the fulfillment of **To**, all the objects will be moved into **Old Generation**.  

The efficency of this algorithm is depend on the living ratio of the object. If the ratio is high, the efficiency will be low. The reason is that the utilizing spaces are comsumed by the copying spaces. 

#### Major GC

Major GC is usually the combinataion of **Mark-sweep** and **mark-compact**

For **Mark-Sweep**:

1. Mark
    Begin with the root collection, mark the living objects.

2. Sweep
    Scan all the memory space, recycle the unmarked objects, and keep record of this area by using free-list.

For the shortcomings, this method has a low efficiency(Including recursion and traversal). Besides, when going through GC, the program has to stop, which lead to bad experience of users.

In addition, the main disdvantage is that the spaces this method frees is discrete, whichis not confusion. And the fading objects are seperated in every corner of the spaces, after the cleaning, the natural layout of the space will become a mass. 

For **Mark-Compact**:

The only shortcoming of this algorithm is that the efficiency of this method is not high. It not only has to mark all the living instances, but also needs to neaten the reference location of the living objects. 

Comparisions:

Memory efficiency:

Copying > Mark-Sweep > Mark-Compact

Memory uniformity:

Copying = Mark-Compact > Mark-Sweep 

Memory utilizatino:

Mark-Compact = Mark-Sweep > Copying