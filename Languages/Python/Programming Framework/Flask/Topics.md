

# Active Directory Programming with Python — Flask API

## Active Directory Topology
Active Directory essentially helps in managing resources of an organization, like users, groups, computers, devices, conference rooms etc.

Active Directory is an abstraction of the organization structure and hierarchy. It has a root domain and subdomains if required. 

A domain is further divided into Organizational Units (OU), which maps to the organizational structure. An organization may have geographically separate offices. Each branch office requires its own domain controller for authentication.


# metaprogramming
metaprogramming—changing program behavior at runtime.

# decorator
A decorator is a callable  that takes another function as argument( i.e the decorated function) , performs some processing with the decorated function and returns it or replaces it with another function or callable object

They are executed immediately when the module is loaded/imported ( which is usually the import time) but the decorated function only run when they are explicitly invoked 

The decorator function is commonly employed in real code by defining the decorator in one module and applied to functions in other modules.

The decorator function is defined in the same module as the decorated functions are used in many Python web frameworks to add functions to some central registry—for example, a registry mapping URL patterns to functions that generate HTTP responses. Such registration decorators may or may not change the decorated function.
