


The event loop is a programming construct that waits for and dispatches events or messages in a program. It works by calling some internal or external "event provider", which generally blocks until an event has arrived, and then calls the relevant event handler ("dispatches the event"). 


The reactor is the core of the event loop within Twisted -- the loop which drives applications using Twisted. The reactor provides basic interfaces to a number of services, including network communications, threading, and event dispatching.

There are multiple implementations of the reactor, each modified to provide better support for specialized features over the default implementation. More information about these and how to use a particular implementation is available via Choosing a Reactor

The loop is a “reactor” because it waits for and then reacts to events. For that reason it is also known as an event loop. 

Twisted’s reactor loop doesn’t start until told to. You start it by calling reactor.run().
The reactor loop runs in the same thread it was started in. In this case, it runs in the main (and only) thread.
Once the loop starts up, it just keeps going. The reactor is now “in control” of the program (or the specific thread it was started in).
If it doesn’t have anything to do, the reactor loop does not consume CPU.
The reactor isn’t created explicitly, just imported.

Twisted actually contains multiple reactor implementations. the select call is just one method of waiting on file descriptors. It is the default method that Twisted uses, but Twisted does include other reactors that use other methods. For example, twisted.internet.pollreactor uses the poll system call instead of select.

To use an alternate reactor, you must install it before importing twisted.internet.reactor. If you import twisted.internet.reactor without first installing a specific reactor implementation, then Twisted will install the selectreactor for you. For that reason, it is general practice not to import the reactor at the top level of modules to avoid accidentally installing the default reactor. Instead, import the reactor in the same scope in which you use it.

```python
from twisted.internet import pollreactor
pollreactor.install()
 
from twisted.internet import reactor
reactor.run()

```


```
def hello():
    print 'Hello from the reactor loop!'
    print 'Lately I feel like I\'m stuck in a rut.'
 
from twisted.internet import reactor
 
reactor.callWhenRunning(hello)
 
print 'Starting the reactor.'
reactor.run()
```

Notice the hello function is called after the reactor starts running. That means it is called by the reactor itself, so Twisted code must be calling our function. We arrange for this to happen by invoking the reactor method callWhenRunning with a reference to the function we want Twisted to call. And, of course, we have to do that before we start the reactor.

We use the term callback to describe the reference to the hello function. A callback is a function reference that we give to Twisted (or any other framework) that Twisted will use to “call us back” at the appropriate time, in this case right after the reactor loop starts up. Since Twisted’s loop is separate from our code, most interactions between the reactor core and our business logic will begin with a callback to a function we gave to Twisted using various APIs.

- The reactor pattern is single-threaded.
- A reactive framework like Twisted implements the reactor loop so our code doesn’t have to.
- Our code still needs to get called to implement our business logic.
- Since it is “in control” of the single thread, the reactor loop will have to call our code.
- The reactor can’t know in advance which part of our code needs to be called.
- Our callback code runs in the same thread as the Twisted loop.
- When our callbacks are running, the Twisted loop is not running. And vice versa.
- The reactor loop resumes when our callback returns.

During a callback, the Twisted loop is effectively “blocked” on our code. So we should make sure our callback code doesn’t waste any time. In particular, we should avoid making blocking I/O calls in our callbacks. Otherwise, we would be defeating the whole point of using the reactor pattern in the first place. Twisted will not take any special precautions to prevent our code from blocking, we just have to make sure not to do it. As we will eventually see, for the common case of network I/O we don’t have to worry about it as we let Twisted do the asynchronous communication for us.

Other examples of potentially blocking operations include reading or writing from a non-socket file descriptor (like a pipe) or waiting for a subprocess to finish. Exactly how you switch from blocking to non-blocking operations is specific to what you are doing, but there is often a Twisted API that will help you do it. Note that many standard Python functions have no way to switch to a non-blocking mode. For example, the os.system function will always block until the subprocess is finished. That’s just how it works. So when using Twisted, you will have to eschew os.system in favor of the Twisted API for launching subprocesses.

you can tell the Twisted reactor to stop running by using the reactor’s stop method. 



# Interfaces and Adapters
Object oriented programming languages allow programmers to reuse portions of existing code by creating new classes of objects which subclass another class. When a class subclasses another, it is said to inherit all of its behaviour. The subclass can then override and extend the behavior provided to it by the superclass. Inheritance is very useful in many situations, but because it is so convenient to use, often becomes abused in large software systems, especially when multiple inheritance is involved. One solution is to use delegation instead of inheritance where appropriate. Delegation is simply the act of asking another object to perform a task for an object. To support this design pattern, which is often referred to as the components pattern because it involves many small interacting components, interfaces and adapters were created by the Zope 3 team.

Interfaces are simply markers which objects can use to say I implement this interface. Other objects may then make requests like Please give me an object which implements interface X for object type Y. Objects which implement an interface for another object type are called adapters.

The superclass-subclass relationship is said to be an is-a relationship. When designing object hierarchies, object modellers use subclassing when they can say that the subclass is the same class as the superclass. 



Factories create Protocol instances.

What this means is that the factory will use the protocol to figure out how it should listen and send data





https://krondo.com/wp-content/uploads/2009/08/twisted-intro.html