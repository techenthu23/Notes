- [Programming Paradigm](#programming-paradigm)
  - [Functional Programming](#functional-programming)
    - [Advantages of pure functions](#advantages-of-pure-functions)
  - [Imperative (procedural) programming](#imperative-procedural-programming)
- [first-class objects](#first-class-objects)
- [Transpiler and Compiler](#transpiler-and-compiler)

---

# Programming Paradigm

Although most languages were designed to support a specific programming paradigm, many general languages are flexible enough to support multiple paradigms. For example, most languages that contain function pointers can be used to credibly support functional programming. Furthermore, C# and Visual Basic include explicit language extensions to support functional programming, including lambda expressions and type inference. LINQ technology is a form of declarative, functional programming.

## Functional Programming

The functional programming paradigm was explicitly created to support a pure functional approach to problem solving. Functional programming is a form of declarative programming.

A functional approach involves composing the problem as a set of functions to be executed. You define carefully the input to each function, and what each function returns.

### Advantages of pure functions

The primary reason to implement functional transformations as pure functions is that pure functions are composable: that is, self-contained and stateless. These characteristics bring a number of benefits, including the following:

- Increased readability and maintainability. This is because each function is designed to accomplish a specific task given its arguments. The function doesn't rely on any external state.
- Easier reiterative development. Because the code is easier to refactor, changes to design are often easier to implement. For example, suppose you write a complicated transformation, and then realize that some code is repeated several times in the transformation. If you refactor through a pure method, you can call your pure method at will without worrying about side effects.
- Easier testing and debugging. Because pure functions can more easily be tested in isolation, you can write test code that calls the pure function with typical values, valid edge cases, and invalid edge cases.

## Imperative (procedural) programming

most mainstream languages, including object-oriented programming (OOP) languages such as C#, Visual Basic, C++, and Java, were designed to primarily support imperative (procedural) programming. With an imperative approach, a developer writes code that specifies the steps that the computer must take to accomplish the goal. This is sometimes referred to as algorithmic programming.

| Characteristic | Imperative approach | Functional approach |
| :------------- | :------------------ | :------------------ |
| Programmer focus | How to perform tasks (algorithms) and how to track changes in state. | What information is desired and what transformations are required. |
| State changes | Important. | Non-existent. |
| Order of execution | Important. | Low importance. |
| Primary flow control | Loops, conditionals, and function (method) calls. | Function calls, including recursion. |
| Primary manipulation unit | Instances of structures or classes. | Functions as first-class objects and data collections. |

---

# first-class objects

Programming language theorists define a “first-class object” as a program entity that can be:
• Created at runtime
• Assigned to a variable or element in a data structure
• Passed as an argument to a function
• Returned as the result of a function

Functions, Integers, strings, and dictionaries in Python are first-class objects

---

# Transpiler and Compiler

A compiler is a software that converts high-level language to low-level assembly language

A transpiler is another software, sometimes called a source-to-source compiler, which converts a high-level language to another high-level language.

The reason we need a transpiler is that recoding a large program into another language is time-consuming. engaging an entire developer team in recoding the whole software in another language, with no added improvements, is economically inefficient. Hence it is better to write a language-to-language transpiler that does the job. The Javascript transpilers essentially do the same thing. Web browsers cannot compile TypeScript, CoffeeScript, or Kotlin natively and we need a transpiler for converting them to Javascript.

| Compiler | Transpiler |
| :------- | :--------- |
| It converts a source code written in a high-level language to an output code in a low-level language. | It converts a source code written in a high-level language to an output code in a different high-level language. |
| The source code has a higher level of abstraction than the output code. | The source code, as well as the output code generated, are of the same level of abstraction. |
| Output code is in assembly language and is readily executable after linking and decoding into machine language. | Output code is still in high-level programming language and requires a compiler to convert into low-abstraction assembly language. |
| In a compiler, the source code is scanned, parsed, transformed into an abstract syntax tree semantically analyzed, then converted into an intermediate code, and finally into the assembly language. | In a transpiler, the source code is parsed, and transformed into an abstract syntax tree, which is then converted to an intermediate model. This then transforms into an abstract syntax tree of the target language and code is generated. |
| Converting Java code into assembly language instructions is an example of compilation. | Converting Java code into C++ code is an example of transpilation. |
---

# Primitive

In computer science, a primitive refers to a basic data type or operation that is built into a programming language or provided by the underlying hardware. Primitives are fundamental building blocks used to represent and manipulate data.

Examples of primitives include:

1. Basic data types such as integers, floating-point numbers, characters, and booleans.
2. Arithmetic operations such as addition, subtraction, multiplication, and division.
3. Logical operations such as AND, OR, and NOT.
4. Control flow constructs such as loops and conditional statements.

Primitives are essential for expressing more complex computations and algorithms in programming languages. They serve as the foundation upon which higher-level abstractions and data structures are built.
---

# MUTEX

A mutex (short for mutual exclusion) is a synchronization primitive used in concurrent programming to control access to shared resources, such as variables or data structures, by multiple threads of execution. It ensures that only one thread can access the shared resource at a time, preventing race conditions and data inconsistencies.

Mutexes typically provide two main operations: "lock" and "unlock." When a thread wants to access the shared resource, it acquires the mutex by locking it. If another thread has already locked the mutex, the requesting thread will wait until the mutex becomes available. Once the thread finishes using the shared resource, it releases the mutex by unlocking it, allowing other threads to acquire it.

Mutexes are essential for coordinating access to shared resources in multi-threaded environments, ensuring data integrity and preventing conflicts between concurrent operations.

In the context of the Linux kernel, a mutex implemented and managed by the kernel itself is typically referred to as a "kernel mutex." These kernel mutexes provide synchronization mechanisms within the kernel space to control access to shared resources among different kernel threads or processes. They function similarly to mutexes used in user space, but they operate within the kernel's environment and are designed to handle kernel-specific scenarios and requirements.

There are several types of mutexes used in concurrent programming, each with its own characteristics and use cases:

1. **Binary Mutex**: Also known as a binary semaphore, this type of mutex has only two states: locked and unlocked. It allows only one thread at a time to access a shared resource.

2. **Counting Mutex**: Unlike a binary mutex, a counting mutex allows multiple threads to hold the lock simultaneously up to a certain limit, specified by its count. Once the limit is reached, additional threads attempting to acquire the lock will be blocked.

3. **Recursive Mutex**: A recursive mutex allows a thread to acquire the lock multiple times from within the same thread without deadlocking. Each acquisition must be matched with a corresponding release to fully unlock the mutex.

4. **Timed Mutex**: This type of mutex allows threads to attempt to acquire the lock with a timeout. If the lock cannot be acquired within the specified time, the operation fails, allowing the thread to continue execution without being blocked indefinitely.

5. **Priority Inheritance Mutex**: In systems where threads have different priorities, priority inheritance mutexes ensure that a low-priority thread holding a mutex inherits the priority of a high-priority thread waiting for the same mutex. This prevents priority inversion and helps avoid priority-related issues in real-time systems.

6. **Read/Write Mutex**: Also known as a readers-writer lock, this type of mutex distinguishes between readers (threads that only read shared data) and writers (threads that modify shared data). It allows multiple readers to hold the lock simultaneously but ensures that only one writer can hold the lock exclusively, preventing concurrent writes.

These are some common types of mutexes used in concurrent programming, each designed to address specific synchronization requirements and scenarios.

---

# FUTEX

FUTEX (Fast Userspace Mutex) is a Linux kernel system call that provides a fast method for implementing basic synchronization between threads of the same process. It's commonly used for implementing synchronization primitives like mutexes and condition variables efficiently in user space. FUTEX operations typically involve waiting for a specific condition to become true or notifying other threads when a condition is met, all while minimizing kernel involvement to improve performance.