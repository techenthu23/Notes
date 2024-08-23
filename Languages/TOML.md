---
title: "TOML"
draft: false
---

TOML stands for Tom’s Obvious, Minimal Language. It is a data serialisation language designed to be a minimal configuration file format that’s easy to read due to obvious semantics.

It is an alternative to YAML and JSON. It aims to be more human friendly than JSON and simpler that YAML. TOML is designed to map unambiguously to a hash table. TOML should be easy to parse into data structures in a wide variety of languages.

TOML already has implementations in most of the most popular programming languages in use today: C, C#, C++, Clojure, Dart, Elixir, Erlang, Go, Haskell, Java, Javascript, Lua, Objective-C, Perl, PHP, Python, Ruby, Swift, Scala... and plenty more.

Be warned, TOML’s spec is still changing a lot. Until it’s marked as 1.0, you should assume that it is unstable and act accordingly. This document follows TOML v0.4.0.

Filename Extension : TOML files should use the extension .toml.

MIME Type:  When transferring TOML files over the internet, the appropriate MIME type is application/toml

```toml

# Comments
# This is a TOML comment

# This is a multiline
# TOML comment

# Indentation is treated as whitespace and ignored

# Powerful Strings
str1 = "I'm a string."
str2 = "You can \"quote\" me."
str3 = "Name\tJos\u00E9\nLoc\tSF."

# Multi-line basic strings
# Include a line ending backslash to automatically trim whitespace preceeding any non-whitespace characters:

str1 = """
Roses are red
Violets are blue"""

str2 = """\
  The quick brown \
  fox jumps over \
  the lazy dog.\
  """

# Literal strings
# surrounded by single quotes. No escaping is performed so what you see is what you get:

path = 'C:\Users\nodejs\templates'
path2 = '\\User\admin$\system32'
quoted = 'Tom "Dubs" Preston-Werner'
regex = '<\i\c*\s*>'

# multi-line literal strings
re = '''\d{2} apps is t[wo]o many'''
lines = '''
The first newline is
trimmed in raw strings.
All other whitespace
is preserved.
'''

str = "I'm a string. \"You can quote me\". Name\tJos\u00E9\nLocation\tSF."

# Numbers
# integers
int1 = +99
int2 = 42
int3 = 0
int4 = -17

# hexadecimal with prefix `0x`
hex1 = 0xDEADBEEF
hex2 = 0xdeadbeef
hex3 = 0xdead_beef

# octal with prefix `0o`
oct1 = 0o01234567
oct2 = 0o755

# binary with prefix `0b`
bin1 = 0b11010110

# fractional
float1 = +1.0
float2 = 3.1415
float3 = -0.01

# exponent
float4 = 5e+22
float5 = 1e06
float6 = -2E-2

# both
float7 = 6.626e-34

# separators
float8 = 224_617.445_991_228

# infinity
infinite1 = inf     # positive infinity
infinite2 = +inf    # positive infinity
infinite3 = -inf    # negative infinity

# not a number
not1 = nan
not2 = +nan
not3 = -nan 

# Dotted Keys
name = "Orange"
physical.color = "orange"
physical.shape = "round"
site."google.com" = true

# JSON Representation
# {
#  "name": "Orange",
#  "physical": {
#    "color": "orange",
#    "shape": "round"
#  },
#  "site": {
#    "google.com": true
#  }
#}

fruit.name = "banana"     # this is best practice
fruit. color = "yellow"    # same as fruit.color
fruit . flavor = "banana"   # same as fruit.flavor

# DO NOT DO THIS
# Defining a key multiple times is invalid.
name = "Tom"
name = "Pradyun"

# bare keys and quoted keys are equivalent
spelling = "favorite"
"spelling" = "favourite"


# Dates and Times

# offset datetime
odt1 = 1979-05-27T07:32:00Z
odt2 = 1979-05-27T00:32:00-07:00
odt3 = 1979-05-27T00:32:00.999999-07:00

# local datetime
ldt1 = 1979-05-27T07:32:00
ldt2 = 1979-05-27T00:32:00.999999

# local date
ld1 = 1979-05-27

# local time
lt1 = 07:32:00
lt2 = 00:32:00.999999


# Arrays
integers = [ 1, 2, 3 ]
colors = [ "red", "yellow", "green" ]
nested_arrays_of_ints = [ [ 1, 2 ], [3, 4, 5] ]
nested_mixed_array = [ [ 1, 2 ], ["a", "b", "c"] ]
string_array = [ "all", 'strings', """are the same""", '''type''' ]

# Mixed-type arrays are allowed
numbers = [ 0.1, 0.2, 0.5, 1, 2, 5 ]
contributors = [
  "Foo Bar <foo@example.com>",
  { name = "Baz Qux", email = "bazqux@example.com", url = "https://example.com/bazqux" }
]

integers3 = [
  1,
  2, # this is ok
]

# Tables (also known as hash tables or dictionaries)
# Under that, and until the next header or EOF, are the key/values of that table. Key/value pairs within tables are not guaranteed to be in any specific order.

[table]

[table-1]
key1 = "some string"
key2 = 123

[table-2]
key1 = "another string"
key2 = 456

[dog."tater.man"]
type.name = "pug"

[dog."tater.man"]
type.name = "pug"     # JSON: { "dog": { "tater.man": { "type": { "name": "pug" } } } }


# [x] you
# [x.y] don't
# [x.y.z] need these
[x.y.z.w] # for this to work

[x] # defining a super-table afterward is ok

#Like keys, you cannot define a table more than once. Doing so is invalid.

# DO NOT DO THIS

[fruit]
apple = "red"

[fruit]
orange = "orange"


[fruit.apple]
texture = "smooth"

#============
# Top-level table begins.
name = "Fido"
breed = "pug"

# Top-level table ends.
[owner]
name = "Regina Dogman"
member_since = 1999-08-04

fruit.apple.color = "red"
# Defines a table named fruit
# Defines a table named fruit.apple

fruit.apple.taste.sweet = true
# Defines a table named fruit.apple.taste
# fruit and fruit.apple were already created

#inline table

[name]
first = "Tom"
last = "Preston-Werner"

[point]
x = 1
y = 2

[animal]
type.name = "pug"

# Array of Tables

[[products]]
name = "Hammer"
sku = 738594937

[[products]]  # empty table within the array

[[products]]
name = "Nail"
sku = 284758393

color = "gray"

# JSON: {
#   "products": [
#    { "name": "Hammer", "sku": 738594937 },
#    { },
#    { "name": "Nail", "sku": 284758393, "color": "gray" }
#  ]
#}



# INVALID TOML DOC
[fruit.physical]  # subtable, but to which parent element should it belong?
color = "red"
shape = "round"

[[fruit]]  # parser must throw an error upon discovering that "fruit" is
           # an array rather than a table
name = "apple"

# INVALID TOML DOC
[[fruits]]
name = "apple"

[[fruits.varieties]]
name = "red delicious"

# INVALID: This table conflicts with the previous array of tables
[fruits.varieties]
name = "granny smith"

[fruits.physical]
color = "red"
shape = "round"

# INVALID: This array of tables conflicts with the previous table
[[fruits.physical]]
color = "green"
#-------------
```
