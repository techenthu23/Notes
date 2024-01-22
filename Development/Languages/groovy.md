---
layout: default
title:  "Groovy Quick Bits"
---
- [Introduction](#introduction)
  - [Pros](#pros)
  - [Cons](#cons)
- [Groovy Terminology](#groovy-terminology)
- [Running Groovy](#running-groovy)
- [Data Types](#data-types)
- [Variables](#variables)
- [Arrays](#arrays)
- [Operators](#operators)
  - [ARITHMETIC OPERATORS](#arithmetic-operators)
  - [BOOLEAN/LOGICAL COMPARISON](#booleanlogical-comparison)
  - [BOOLEAN/LOGICAL OPERATORS](#booleanlogical-operators)
  - [BITWISE OPERATORS](#bitwise-operators)
  - [ASSIGNMENT AND REFLEXIVE ASSIGNMENT OPERATORS](#assignment-and-reflexive-assignment-operators)
  - [OTHER OPERATORS](#other-operators)
- [Flow of Control](#flow-of-control)
- [Functions](#functions)
- [DYNAMIC FUNCTION CALLS](#dynamic-function-calls)
- [CLOSURES](#closures)
- [Exceptions and Asserts](#exceptions-and-asserts)
- [Groovy Configuration File](#groovy-configuration-file)
- [Misc Observations](#misc-observations)
- [Errata](#errata)
- [Quick help](#quick-help)

# Introduction

Groovy is a scripting language based on Java.

It can be downloaded from <http://groovy.codehaus.org>..

Not surprising, Groovy pre-compiler is also written in Java and Java must be installed first in order to run Groovy.

The Theory in One Sentence

What if we redesign Java to be more like Perl, but looking like Java so Java developers wouldn't have to learn Perl, would developers be more productive?

## Pros

- Looks like Java.
- Can access all Java classes.
- More intuitive than Java: good for scripts and prototyping.
- Good scripting features (compared to Java).

## Cons

- Poor language vision and design.
- More error-prone (than Java).
- Poorer readability (than Java).
- Possibly less scalable and maintainable (than Java). Doesn't always honour "private", etc.
- Uses more resources than Java (especially closures that cause the environment to persist).
- Lack of good, organized documentation with real-world examples.

# Groovy Terminology

- Auto-boxing - converting a primitive data type to its equivalent object without an explicit type cast
- Closure - a lamda function with a parental context
- Groovlets - servlets using Groovy
- Meta-programming - using Groovy to investigate and modify a program while the program is running.

# Running Groovy

You can start the interactive Groovy Console by typing

```bash
groovyConsole &
```

or you can edit a groovy script in your favourite text editor and run the script from the shell with

```bash
groovy myscript
```

Or try a small piece of Goovy with

```bash
groovy -e "statements" arguments
```

> Groovy scripts can also be embedded in JSP documents as Groovy servlets ("groovlets") by loading a special Groovy Java class. Similar techinques can be used for distributed objects (Groovy Beans), or text/html templates.

**A First Groovy Script**

Here is a very simple example of Groovy in action.

```groovy
class Foo {
   int i = 2;
   void print_i() {
      println "The value of i is " + i;
   }
}
Foo f = new Foo();

f.print_i();
```

```text
The value of i is 2
```

You can also create the script with a text editor and run it from the shell prompt. If the name of the script file is "first.gvy", you can run it this way:

```bash
$ groovy first.gvy
The value of i is 2
```

Let's look at the components of this script. These concepts should be familar to you if you've used the Java programming language.

- `class Foo` - this defines a new class called "Foo"
- `int i` - Foo contains a variable named "i"
- `void print_name` - Foo also contains a function called "print_name"
- `Foo f = new Foo()` - f is a new object which as the type of "Foo"
- `f.print_name` - execute the print_name function in f, showing the value of i (which is 2)
- Semi-colons are optional

> Notice in Groovy that some features of Java are optional: Groovy doesn't require "System." before println and it doesn't require paratheses around println's parameter. You can use them if you want to.

```groovy
  // Four ways to say "hello world"
  System.out.println( "hello world" );
  println ( "hello world" );
  println ( "hello world" )
  println "hello world";
  println "hello world"
```

Which way is the "right" way?

# Data Types

There are 7 primitive data types in Groovy (Java has 5). That is, Groovy provides ways of creating literals for these types and all other types are built from these types:

```groovy
boolean - x = true
integer - x = 1
float x = 15.2
string x = "apple" ; x2 = /blueberry/
classes x = new widgit()
lists x = ["apple", "blueberry", "cherry"]
maps (an associative array) x = ["apple":10, "blueberry":15, "cherry":20]
```

Lists and maps are extensions added to Java. They are objects so there are built-in functions availble to tell you more about the objects. The keyword null is used to represent a missing value.

```groovy
// File: hash.groovy
fruit_total = [ "apple":10, "blueberry":15, "cherry":20 ];
println "The fruit_total map looks like this: " + fruit_total;
println "The class of the map is " + fruit_total.getClass();
println "The size of the map is " + fruit_total.size();
println "The size of an empty map is " + [:].size();
println "The total number of apples is " + fruit_total["apple"];
fruit_total["blueberry"] = 25;
println "The total number of blueberries is now " + fruit_total["blueberry"];
println "The total number of snozberries is " + fruit_total["snozberry"];
```

```bash
$ groovy hash.groovy
The fruit_total map looks like this: [apple:10, blueberry:15, cherry:20]
The class of the map is class java.util.LinkedHashMap
The size of the map is 3
The size of an empty map is 0
The total number of apples is 10
The total number of blueberries is now 25
The total number of snozberries is null
```

- Groovy will perform value substitutions when it finds a dollar sign in a double-quote string.

- Type conversions are achieved with "(type) expr".

- Arbitrary expressions can be put in a double-quote string with ${..}. The evaluation doesn't happen when the string literal is defined but when the string is used.

```groovy
//File: sub.groovy
total = 15;
print "The total is $total";
$ groovy sub.groovy
The total is 15
```

# Variables

- Variables (unless they are in the main program) must be declared. If they are delcared with def, the variable type is not defined. If they are declared with a type (a primitive data type or a class), they must contain values of that type.

```groovy
x = true
println x
```

- Java: "Auto Boxing" means automatically converting a primitive to its equivalent object, such as an int to an Integer, without an explicit clast.

```groovy
println 2.getClass();
class java.lang.Integer
```

In Java, you would have to convert 2 to an Integer object first.

- Constants are declared with final.

# Arrays

Java: array literals use square brackets like list literals so as not to conflict with closures, not curly brackets as in Java.

# Operators

All your standard Java operators are supported:

## ARITHMETIC OPERATORS

- \+ (addition or concetenation),
- \- (subtraction),
- \* (multiplication),
- / (division),
- % (modulo),
- ++ (increment),
- -- (decrement)

## BOOLEAN/LOGICAL COMPARISON

==, !=, >, >=, <, <=, ==~ (regular expression match)

**Java:** == with classes will compare values, not object identity as in Java

## BOOLEAN/LOGICAL OPERATORS

- `&&` (and)
- `||` (or)
- `!` (not)

## BITWISE OPERATORS

- `&` (and)
- `|` (or)
- `~` (not/complement)
- `^` (exclusive or)
- `<<` (bit shift left)
- `>>` (arithmetic/signed bit shift right)
- `>>>` (logical/unsigned bit shift right)

## ASSIGNMENT AND REFLEXIVE ASSIGNMENT OPERATORS

- `=` (assignment)
- `+=`
- `-=`
- `*=`
- `/=`
- `%=`
- `&=`
- `|=`
- `^=`
- `<<=`
- `>>=`
- `>>>=`

## OTHER OPERATORS

- `? :` (ternary)
- `.` (object access)
- `[]` (array access)
- `new` (object creation)
- `instanceof` (type comparison)

```groovy
// Regular expressions

def fruit = "apple"

if ( fruit ==~ /^a[a-z]*/ ) {
   println "It looks like it might be an apple"
} else {
   println "It doesn't look like apple"
}
```

```
It doesn't look like apple
```

# Flow of Control

```groovy
if ( expression ) statement;

if ( expression ) statement else if statement else statement;

switch (item) case value-list|value-range|value-regex|type-list...default:..}

for (var in in-expr) statement;

for (init-expr; test-expr; incr-expression) statement

while ( expr ) statement

do statement while ( expression );
```

Loops may be named with a proceeding label with a colon.

Loop exit statements are break and continue. These may have a loop label to exit multiple loops.

# Functions

Functions can have a return value or can be declared with def for no specific return value.

Values that are not assigned are implicitly used as a return value. A return statement is not required (it is implied).

```groovy
// implied_return
int one() { 1 } // implies "return 1"
println one()
```

```
1
```

```groovy
// function that returns an int or string
def divide( int d ) {
  ( d != 0 ) ? 100/d : "undefined";
}

println divide( 5 )
println divide( 0 )
```

```
2E+1
undefined
```

An array values can be converted to an actual parameter list with a leading asterisk.

> The biggest difference between a Groovy function and a Java function is that the calls declared in the function are not checked until the function is executed.

For example, in Java this is an error:

```java
  public static void main( String[] args) {
          // This is an error in Java...get must exist at compile-time!
          get( "I don't exist" );
  }
```

But in Groovy it isn't.

```groovy
private static void some_function() {
    // OK in Groovy
    get( "I don't exist" );
}
```

get() doesn't need to exist when the function is defined but it must exist in scope when the function runs.

some_function()  # exception thrown if get doesn't exist

This behavior only applies to function calls. Variables come from where the function is defined.

# DYNAMIC FUNCTION CALLS

Functions are called by name. However, functions may also be called dynamically with a name in a variable through string expansion. The function name must be in quotes

```groovy
String s = "one"
def one() { "one" }

println "$s"()

class Numbers {
  String one() { "one" }
  String two() { "two" }
}

Numbers numbers = new Numbers();

s = "one"
println numbers."$s"()
```

```
one
one
```

Putting the class name prefix within the quotes will not work. In the above example, `s = "numbers.one"` will not work. Nor can you make the containing class name dynamic.

# CLOSURES

Closures are anonymous (unnamed) functions assigned to variables. That is, lamda functions. They have the same property as Goovy functions: there are no errors if calls inside the closure don't exist when the closure is created.

```groovy
// Closures

def some_function = { 2 * 2 }

println some_function
println some_function()

some_function = { 2 * it }  // it is a parameter
println some_function( 3 )
```

However the main purpose of closures is to pass these anonymous functions to routines that require a function callback. You can create a function without having to give the function a name.

```groovy
def fruit = ['apple', 'banana', 'pear' ];
fruit.collect { println 'The fruit is ' + it }
```

```
The fruit is apple
The fruit is banana
The fruit is pear
```

Result: [null, null, null]

- These will also work:

```groovy
fruit.collect( { println 'The fruit is ' + it } );
```

```groovy
print_fruit2 =  { println 'The fruit is ' + it }
fruit.collect print_fruit2
```

- Why doesn't this work:

```groovy
public void print_fruit( String f ) {
   println( 'The fruit is ' + f );
}
// fruit.collect( print_fruit );
```

Closures see the parent's variables, even if they are private. A side-effect of groovy programs is they use more memory because they must keep the parent's context around for the duration of the closure.

```
// Closures see the parent's variables

private int i = 3;
def c = { println "i is " + i; };
c();        // prints "i is 3"
```

Java: return is not required.

# Exceptions and Asserts

Exceptions are handled with try, catch and an optional finally.

```groovy
try {
  throw new Exception( "die!" )
} catch (e) {
  println "The exception was caught: " + e.getMessage();
} finally {
  println "Nothing to tidy up after a catch"
}
```

```
The exception was caught: die!
Nothing to tidy up after a catch
```

Catch statements can set the function return value.

Assertions will throw an exception if the expression is false.

```
assert 1 == 1
```

# Groovy Configuration File

By default, Groovy provides (uses) the following packages: java.io.*, java.lang.*, java.math.BigDecimal, java.math.BigInteger, java.net.*, java.util.*, groovy.lang.*and groovy.util.*. You do not include a use statement to use these packages.

Groovy can load Java JAR archives to make those classes available to your scripts. The Groovy configuration file (usually /usr/share/groovy/conf/groovy-starter.conf in Linux) lists directories were JAR files will be loaded from.

# Misc Observations

```groovy
// File: nodecl.groovy

total = 15;
println "The total is $total";

int total = 15; // Total already exists but is allowed
println "The total is $total";

int total = 15; // Duplicate detected at run-time
println "The total is $total";

def total = 15;  // Duplicate not detected until here at compile time
println "The total is $total";
```

Optional things that really aren't optional: cases where def, semi-colons, etc. are required. Long lines broken into two lines--the second line may be ignored because semi-colons are not required (Groovy doesn't know it's one statement).

Why "def" and not "typeless" or "multitype"?

Groovy's typing short-cuts result in readability problems.

Missing things are not errors.

To use Java classes, still have to work with Java's bureaucratic class conventions.

If they got rid of semi-colons, why not get rid of statement blocks and be more like Python and Turing?

# Errata

Groovy supports documentation annotations.

Groovy has classes to examine programs while they are running, such as being able to add new functions to an object that already exists.

Groovy supports generics.

# Quick help

```groovy
/*
  Set yourself up:

  1) Install SDKMAN - http://sdkman.io/
  2) Install Groovy: sdk install groovy
  3) Start the groovy console by typing: groovyConsole

*/

//  Single line comments start with two forward slashes
/*
Multi line comments look like this.
*/

// Hello World
println "Hello world!"

/*
  Variables:

  You can assign values to variables for later use
*/

def x = 1
println x

x = new java.util.Date()
println x

x = -3.1499392
println x

x = false
println x

x = "Groovy!"
println x

/*
  Collections and maps
*/

//Creating an empty list
def technologies = []

/*** Adding a elements to the list ***/

// As with Java
technologies.add("Grails")

// Left shift adds, and returns the list
technologies << "Groovy"

// Add multiple elements
technologies.addAll(["Gradle","Griffon"])

/*** Removing elements from the list ***/

// As with Java
technologies.remove("Griffon")

// Subtraction works also
technologies = technologies - 'Grails'

/*** Iterating Lists ***/

// Iterate over elements of a list
technologies.each { println "Technology: $it"}
technologies.eachWithIndex { it, i -> println "$i: $it"}

/*** Checking List contents ***/

//Evaluate if a list contains element(s) (boolean)
contained = technologies.contains( 'Groovy' )

// Or
contained = 'Groovy' in technologies

// Check for multiple contents
technologies.containsAll(['Groovy','Grails'])

/*** Sorting Lists ***/

// Sort a list (mutates original list)
technologies.sort()

// To sort without mutating original, you can do:
sortedTechnologies = technologies.sort( false )

/*** Manipulating Lists ***/

//Replace all elements in the list
Collections.replaceAll(technologies, 'Gradle', 'gradle')

//Shuffle a list
Collections.shuffle(technologies, new Random())

//Clear a list
technologies.clear()

//Creating an empty map
def devMap = [:]

//Add values
devMap = ['name':'Roberto', 'framework':'Grails', 'language':'Groovy']
devMap.put('lastName','Perez')

//Iterate over elements of a map
devMap.each { println "$it.key: $it.value" }
devMap.eachWithIndex { it, i -> println "$i: $it"}

//Evaluate if a map contains a key
assert devMap.containsKey('name')

//Evaluate if a map contains a value
assert devMap.containsValue('Roberto')

//Get the keys of a map
println devMap.keySet()

//Get the values of a map
println devMap.values()

/*
  Groovy Beans

  GroovyBeans are JavaBeans but using a much simpler syntax

  When Groovy is compiled to bytecode, the following rules are used.

    * If the name is declared with an access modifier (public, private or
      protected) then a field is generated.

    * A name declared with no access modifier generates a private field with
      public getter and setter (i.e. a property).

    * If a property is declared final the private field is created final and no
      setter is generated.

    * You can declare a property and also declare your own getter or setter.

    * You can declare a property and a field of the same name, the property will
      use that field then.

    * If you want a private or protected property you have to provide your own
      getter and setter which must be declared private or protected.

    * If you access a property from within the class the property is defined in
      at compile time with implicit or explicit this (for example this.foo, or
      simply foo), Groovy will access the field directly instead of going though
      the getter and setter.

    * If you access a property that does not exist using the explicit or
      implicit foo, then Groovy will access the property through the meta class,
      which may fail at runtime.

*/

class Foo {
    // read only property
    final String name = "Roberto"

    // read only property with public getter and protected setter
    String language
    protected void setLanguage(String language) { this.language = language }

    // dynamically typed property
    def lastName
}

/*
  Methods with optional parameters
*/

// A method can have default values for parameters
def say(msg = 'Hello', name = 'world') {
    "$msg $name!"
}

// It can be called in 3 different ways
assert 'Hello world!' == say()
// Right most parameter with default value is eliminated first.
assert 'Hi world!' == say('Hi')
assert 'learn groovy' == say('learn', 'groovy')

/*
  Logical Branching and Looping
*/

//Groovy supports the usual if - else syntax
def x = 3

if(x==1) {
    println "One"
} else if(x==2) {
    println "Two"
} else {
    println "X greater than Two"
}

//Groovy also supports the ternary operator:
def y = 10
def x = (y > 1) ? "worked" : "failed"
assert x == "worked"

//Groovy supports 'The Elvis Operator' too!
//Instead of using the ternary operator:

displayName = user.name ? user.name : 'Anonymous'

//We can write it:
displayName = user.name ?: 'Anonymous'

//For loop
//Iterate over a range
def x = 0
for (i in 0 .. 30) {
    x += i
}

//Iterate over a list
x = 0
for( i in [5,3,2,1] ) {
    x += i
}

//Iterate over an array
array = (0..20).toArray()
x = 0
for (i in array) {
    x += i
}

//Iterate over a map
def map = ['name':'Roberto', 'framework':'Grails', 'language':'Groovy']
x = ""
for ( e in map ) {
    x += e.value
    x += " "
}
assert x.equals("Roberto Grails Groovy ")

/*
  Operators

  Operator Overloading for a list of the common operators that Groovy supports:
  http://www.groovy-lang.org/operators.html#Operator-Overloading

  Helpful groovy operators
*/
//Spread operator:  invoke an action on all items of an aggregate object.
def technologies = ['Groovy','Grails','Gradle']
technologies*.toUpperCase() // = to technologies.collect { it?.toUpperCase() }

//Safe navigation operator: used to avoid a NullPointerException.
def user = User.get(1)
def username = user?.username


/*
  Closures
  A Groovy Closure is like a "code block" or a method pointer. It is a piece of
  code that is defined and then executed at a later point.

  More info at: http://www.groovy-lang.org/closures.html
*/
//Example:
def clos = { println "Hello World!" }

println "Executing the Closure:"
clos()

//Passing parameters to a closure
def sum = { a, b -> println a+b }
sum(2,4)

//Closures may refer to variables not listed in their parameter list.
def x = 5
def multiplyBy = { num -> num * x }
println multiplyBy(10)

// If you have a Closure that takes a single argument, you may omit the
// parameter definition of the Closure
def clos = { print it }
clos( "hi" )

/*
  Groovy can memoize closure results [1][2][3]
*/
def cl = {a, b ->
    sleep(3000) // simulate some time consuming processing
    a + b
}

mem = cl.memoize()

def callClosure(a, b) {
    def start = System.currentTimeMillis()
    mem(a, b)
    println "Inputs(a = $a, b = $b) - took ${System.currentTimeMillis() - start} msecs."
}

callClosure(1, 2)
callClosure(1, 2)
callClosure(2, 3)
callClosure(2, 3)
callClosure(3, 4)
callClosure(3, 4)
callClosure(1, 2)
callClosure(2, 3)
callClosure(3, 4)

/*
  Expando

  The Expando class is a dynamic bean so we can add properties and we can add
  closures as methods to an instance of this class

  http://mrhaki.blogspot.mx/2009/10/groovy-goodness-expando-as-dynamic-bean.html
*/
  def user = new Expando(name:"Roberto")
  assert 'Roberto' == user.name

  user.lastName = 'Pérez'
  assert 'Pérez' == user.lastName

  user.showInfo = { out ->
      out << "Name: $name"
      out << ", Last name: $lastName"
  }

  def sw = new StringWriter()
  println user.showInfo(sw)


/*
  Metaprogramming (MOP)
*/

//Using ExpandoMetaClass to add behaviour
String.metaClass.testAdd = {
    println "we added this"
}

String x = "test"
x?.testAdd()

//Intercepting method calls
class Test implements GroovyInterceptable {
    def sum(Integer x, Integer y) { x + y }

    def invokeMethod(String name, args) {
        System.out.println "Invoke method $name with args: $args"
    }
}

def test = new Test()
test?.sum(2,3)
test?.multiply(2,3)

//Groovy supports propertyMissing for dealing with property resolution attempts.
class Foo {
   def propertyMissing(String name) { name }
}
def f = new Foo()

assertEquals "boo", f.boo

/*
  TypeChecked and CompileStatic
  Groovy, by nature, is and will always be a dynamic language but it supports
  typechecked and compilestatic

  More info: http://www.infoq.com/articles/new-groovy-20
*/
//TypeChecked
import groovy.transform.TypeChecked

void testMethod() {}

@TypeChecked
void test() {
    testMeethod()

    def name = "Roberto"

    println naameee

}

//Another example:
import groovy.transform.TypeChecked

@TypeChecked
Integer test() {
    Integer num = "1"

    Integer[] numbers = [1,2,3,4]

    Date date = numbers[1]

    return "Test"

}

//CompileStatic example:
import groovy.transform.CompileStatic

@CompileStatic
int sum(int x, int y) {
    x + y
}

assert sum(2,5) == 7
```
