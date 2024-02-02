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
