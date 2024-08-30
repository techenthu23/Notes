The Go programming language was created at Google, but it's completely open source and free to whomever wants to use it. It's also being improved continuously, with new versions being released about every six months with new features, performance improvements, and bug fixes. Go's syntax is influenced by C and other C-style languages, but it emphasizes greater simplicity and concision in its syntax. And it's a strongly typed language that compiles to operating system-specific binary executables resulting in truly blazing runtime speed. You can build many kinds of software with Go, but it's particularly popular with developers who build solutions for cloud-based services. Among well known products, Go has been used on Docker, Kubernetes, Fedora CoreOS, and also on many other cloud-based products and services. 

You can use Go for many types of software from simple applications that you can run from a command prompt to web-based applications including RESTful web services, all the way to compilers, and other low level systems software. Go is a compiled language, and it's based mostly on the C programming language, but it's influenced by many other languages. Some of the most common languages that you'll hear me refer to in this course include C, C++, C# and Java. This course is built for developers who already have experience with at least one other programming language and understand the basic vocabulary used by all programmers. If you understand terms like function, variable, method, and conditional logic, you should be able to work through this course successfully. If you'd like some information about those foundational topics, look at this course. It's designed to help new programmers understand the nature of programming and the building blocks that make up an application. 

In addition to basic programming concepts, Go depends on some common object-oriented principles. For example, 
- **encapsulation** means that you can build entities in a language that are wrappers for complex functionality. These make it easy to use that functionality with simple calls from other parts of the application. Go implements encapsulation with something called types and structs, also known as data structures. 
- **Polymorphism** is another important object-oriented principle. It refers to the ability to deal with a type or an object as though it's another type. Polymorphism is frequently associated with inheritance in other languages, but Go doesn't support type inheritance, unlike C++, C#, Java or other object-oriented languages. But it does have interfaces, contracts that can be implemented and satisfied by multiple types. So understanding polymorphism is also important to being a successful Go programmer. 
- 
- If you need more information about the concepts of object-oriented programming, you can watch this course all about object-oriented design. And again, Go is based on other existing languages, and if you want to learn those languages, there are courses for those too, including courses on C, C++, C# and Java. These courses can help you improve your foundational knowledge as you start to learn Go.
Enable interactive transcripts



# Go : A complied language and static type language

One of the first questions programmers frequently ask about a language is whether it's compiled or interpreted.?

An example of an interpreted language is JavaScript. JavaScript's source code is read directly by the browser when you're running in the browser and then executed at runtime. There is no precompilation step and while in some interpreted environments there's an intermediate format such as bytecode, there's no precompilation step that you have to follow. 

A compiled language in contrast is transformed into a format that's specific to an operating system. 

C and C++ are both compiled languages. Go is a compiled language. Unlike Java which is compiled to bytecode that can run on multiple operating systems, Go is compiled to a form that can only run on a single operating system and it's also ***statically typed***, which means that its variables have specific types. 

You don't always have to explicitly declare the types. There are sometimes inferred by the compiler. But they're always known at compilation time. 

It can sometimes feel with Go like you're working with an interpreted language because you can run a source code file without precompiling. But what's really happening in the background is that the application is being compiled to a temporary executable. When you're creating applications that you're going to deliver to users though, you'll normally use the compilation tool known simply as Go, and as I mentioned, the compiled executable will only run on one operating system. So if you compile for Windows, that executable can only be run on Windows. If you compile for Mac, it can only run on Mac and so on. 

***Applications built with Go contain a statically linked runtime***. That is there's a run-time component that's packaged with the application during the compilation process. You'll notice that the size of a compiled application is much larger than its source code file and that's because that runtime is being included. But there is no external virtual machine. 

So for example, in Java, you have an operating system specific virtual machine or JVM. It's expected to be installed on the client and therefore the Java application which is compiled into bytecode can be very, very small. ***With Go, there is no external virtual machine and so that runtime has to be included in every compiled application.*** 

## Is Go Object Oriented?
Another common question is whether a language is object-oriented and the answer with Go is yes, sort of. It implements some critical object-oriented features. For example, you can define custom interfaces in Go. 

An **interface** is a contract of sorts that defines a set of functions and then something that implements that interface has to implement those functions. In Go you can define **types**. Almost everything is a type in Go and every type implements at least one interface. 

When you add functions to a type in Go, they are now called **methods** and that's an object-oriented term. 

You can create your own structures or structs in Go and those can have member methods and member fields. So the types and structures of Go can feel a lot like the classes in a language such as Java or C#. But there were other object oriented features in those languages that aren't present in Go. For example, there is no type inheritance in Go. You can't create a type and say that's going to be the super type and then create another something that's called a subtype and inherit features from the super type. That's just not a part of the Go architecture. There's also no method or operator overloading in Go. Each function or method within package or type in Go has a specific signature but you can't have more than one function in a package or more than one method in a type that has the same name. Also, Go doesn't have structured exception handling such as you might expect in a language like C or C#. You don't have the try, catch, or finally key words that appear in those other languages. Instead, error objects are simply returned by functions which might generate those errors and then you use conditional logic to examine the error objects. There are also no implicit numeric conversions. You have to explicitly type every variable or implicitly type it by saying exactly where you're getting the data. If you want to convert a value from one type to another in Go, you have to do it explicitly by wrapping the value in a function that says I want it to be this type. The main reason you won't find these language features in Go is because the languages designers felt that these features which are common to very advanced languages make these languages harder to read and more susceptible to bugs. Everything you need to know about a Go program is right there on the surface. You don't have to remember a rule of the language because it's all there in the application's code. Go is based on a number of different languages. It was originally designed as a next-generation language that could do everything you can do with C. Systems programming, application development, and so on. So it borrows a lot of syntax from C and it's related languages, C++, C#, Java, and so on, but it also borrows syntax from other languages such as Pascal, Modula, Oberon, and other similar languages. At the end of the day, one of the attractions of Go is that you simply don't have to do as much typing. Go programming is very concise and there aren't a lot of unnecessary characters to get in the way. Now if you're already an expert in one of those C-style languages, you will be ahead of the game with Go. But really it doesn't matter that much. As long as you understand the fundamentals of programming, you should find Go to be a very learnable language.
Enable interactive transcripts






