
# YAML

YAML is a human-readable data serialization language that is often used for writing configuration files. Depending on whom you ask, YAML stands for yet another markup language or YAML ain’t markup language (a recursive acronym), which emphasizes that YAML is for data, not documents.

YAML is a popular programming language because it is designed to be easy to read and understand. It can also be used in conjunction with other programming languages.

YAML files use a `.yml` or `.yaml` extension, and follow specific syntax rules. YAML is also a superset of JSON, so JSON files are valid in YAML.

YAML is CASE sensitive

the structure of a YAML file is a map or a list, and it follows a hierarchy depending on the indentation, and how you define your key values. Maps allow you to associate key-value pairs. Each key must be unique, and the order doesn't matter.

A map in YAML needs to be resolved before it can be closed, and a new map is created. A new map can be created by either increasing the indentation level or by resolving the previous map and starting an adjacent map.

A list includes values listed in a specific order and may contain any number of items needed. A list sequence starts with a dash (-) and a space, while indentation separates it from the parent.

YAML also contains scalars, which are arbitrary data (encoded in Unicode) that can be used as values such as strings, integers, dates, numbers, or booleans.

YAML does not natively support variable placeholders.

Anchors and Aliases almost provide the desired functionality, but these do not work as variable placeholders that can be inserted into arbitrary regions throughout the YAML text. They must be placed as separate YAML nodes.

There are some add-on libraries that support arbitrary variable placeholders, but they are not part of the native YAML specification.

```yaml
---  # Indicate start of a document

# Comments in YAML look like this.
# YAML supports single-line comments, but does not support block comments.

################
# SCALAR TYPES #
################

# Our root object (which continues for the entire document) will be a map,
# which is equivalent to a dictionary, hash or object in other languages.

key: value
another_key: Another value goes here.
a_number_value: 100
scientific_notation: 1e+12
hex_notation: 0x123  # evaluates to 291
octal_notation: 0123 # evaluates to 83

# The number 1 will be interpreted as a number, not a boolean. 
# If you want it to be interpreted as a boolean, use true.
boolean: true
null_value: null
another_null_value: ~
key with spaces: value

# Yes and No (doesn't matter the case) will be evaluated to boolean 
# true and false values respectively.
# To use the actual value use single or double quotes.
no: no            # evaluates to "no": false
yes: No           # evaluates to "yes": false
not_enclosed: yes # evaluates to "not_enclosed": true
enclosed: "yes"   # evaluates to "enclosed": yes

# Notice that strings don't need to be quoted. However, they can be.
however: 'A string, enclosed in quotes.'
'Keys can be quoted too.': "Useful if you want to put a ':' in your key."
single quotes: 'have ''one'' escape pattern'
double quotes: "have many: \", \0, \t, \u263A, \x0d\x0a == \r\n, and more."
# UTF-8/16/32 characters need to be encoded
Superscript two: \u00B2

# Special characters must be enclosed in single or double quotes
special_characters: "[ John ] & { Jane } - <Doe>"

# Multiple-line strings can be written either as a 'literal block' (using |), 
# or a 'folded block' (using '>').
# Literal block turn every newline within the string into a literal newline (\n).
# Folded block removes newlines within the string.
literal_block: |
  This entire block of text will be the value of the 'literal_block' key,
  with line breaks being preserved.

  The literal continues until de-dented, and the leading indentation is
  stripped.

      Any lines that are 'more-indented' keep the rest of their indentation -
      these lines will be indented by 4 spaces.

folded_style: >
  This entire block of text will be the value of 'folded_style', but this
  time, all newlines will be replaced with a single space.

  Blank lines, like above, are converted to a newline character.

      'More-indented' lines keep their newlines, too -
      this text will appear over two lines.

# |- and >- removes the trailing blank lines (also called literal/block "strip")
literal_strip: |-
  This entire block of text will be the value of the 'literal_block' key,
  with trailing blank line being stripped.
block_strip: >-
  This entire block of text will be the value of 'folded_style', but this
  time, all newlines will be replaced with a single space and 
  trailing blank line being stripped.

# |+ and >+ keeps trailing blank lines (also called literal/block "keep")
literal_keep: |+
  This entire block of text will be the value of the 'literal_block' key,
  with trailing blank line being kept.

block_keep: >+
  This entire block of text will be the value of 'folded_style', but this
  time, all newlines will be replaced with a single space and 
  trailing blank line being kept.

####################
# COLLECTION TYPES #
####################

# Nesting uses indentation. 2 space indent is preferred (but not required).
a_nested_map:
  key: value
  another_key: Another Value
  another_nested_map:
    hello: hello

# Maps don't have to have string keys.
0.25: a float key

# Keys can also be complex, like multi-line objects
# We use ? followed by a space to indicate the start of a complex key.
? |
  This is a key
  that has multiple lines
: and this is its value

# YAML also allows mapping between sequences with the complex key syntax
# Some language parsers might complain
# An example
? - Manchester United
  - Real Madrid
: [ 2001-01-01, 2002-02-02 ]

# Sequences (equivalent to lists or arrays) look like this
# (note that the '-' counts as indentation):
a_sequence:
  - Item 1
  - Item 2
  - 0.5  # sequences can contain disparate types.
  - Item 4
  - key: value
    another_key: another_value
  - - This is a sequence
    - inside another sequence
  - - - Nested sequence indicators
      - can be collapsed

# Since YAML is a superset of JSON, you can also write JSON-style maps and
# sequences:
json_map: { "key": "value" }
json_seq: [ 3, 2, 1, "takeoff" ]
and quotes are optional: { key: [ 3, 2, 1, takeoff ] }

#######################
# EXTRA YAML FEATURES #
#######################

# YAML also has a handy feature called 'anchors', which let you easily duplicate
# content across your document.
# Anchors identified by & character which define the value.
# Aliases identified by * character which acts as "see above" command.
# Both of these keys will have the same value:
anchored_content: &anchor_name This string will appear as the value of two keys.
other_anchor: *anchor_name

values: &ref
  - Will be
  - reused below
  
other_values:
  i_am_ref: *ref

# Equivleant JSON
# {
#   "values": [
#     "Will be",
#     "reused below"
#   ],
#   "other_values": {
#     "i_am_ref": [
#       "Will be",
#       "reused below"
#     ]
#   }
# }


# Anchors can be used to duplicate/inherit properties
base: &base
  name: Everyone has same name

# The regexp << is called 'Merge Key Language-Independent Type'. It is used to
# indicate that all the keys of one or more specified maps should be inserted
# into the current map.
# NOTE: If key already exists alias will not be merged
foo:
  <<: *base # doesn't merge the anchor
  age: 10
  name: John
bar:
  <<: *base # base anchor will be merged
  age: 20

# foo and bar would also have name: Everyone has same name

# YAML also has tags, which you can use to explicitly declare types.
# Syntax: !![typeName] [value]
explicit_boolean: !!bool true
explicit_integer: !!int 42
explicit_float: !!float -42.24
explicit_string: !!str 0.5
explicit_datetime: !!timestamp 2022-11-17 12:34:56.78 +9
explicit_null: !!null null

# Some parsers implement language specific tags, like this one for Python's
# complex number type.
python_complex_number: !!python/complex 1+2j

# We can also use yaml complex keys with language specific tags
? !!python/tuple [ 5, 7 ]
: Fifty Seven
# Would be {(5, 7): 'Fifty Seven'} in Python

####################
# EXTRA YAML TYPES #
####################

# Strings and numbers aren't the only scalars that YAML can understand.
# ISO-formatted date and datetime literals are also parsed.
datetime_canonical: 2001-12-15T02:59:43.1Z
datetime_space_separated_with_time_zone: 2001-12-14 21:59:43.10 -5
date_implicit: 2002-12-14
date_explicit: !!timestamp 2002-12-14

# The !!binary tag indicates that a string is actually a base64-encoded
# representation of a binary blob.
gif_file: !!binary |
  R0lGODlhDAAMAIQAAP//9/X17unp5WZmZgAAAOfn515eXvPz7Y6OjuDg4J+fn5
  OTk6enp56enmlpaWNjY6Ojo4SEhP/++f/++f/++f/++f/++f/++f/++f/++f/+
  +f/++f/++f/++f/++f/++SH+Dk1hZGUgd2l0aCBHSU1QACwAAAAADAAMAAAFLC
  AgjoEwnuNAFOhpEMTRiggcz4BNJHrv/zCFcLiwMWYNG84BwwEeECcgggoBADs=

# YAML also has a set type, which looks like this:
set:
  ? item1
  ? item2
  ? item3
or: { item1, item2, item3 }

# Sets are just maps with null values; the above is equivalent to:
set2:
  item1: null
  item2: null
  item3: null

...  # document end
```

> - XML, JSON and YAML are the most popular data serialization languages. This means we use them to represent data structures and values, which enables data storage, transfer and distribution, often for use in configurations.
> - XML is a markup language, whereas JSON and YAML are data formats. XML uses tags to define the elements and stores data in a tree structure, whereas data in JSON is stored like a map with key/value pairs. YAML, on the other hand, allows representation of data both in list or sequence format and in the form of a map with key/value pairs.

# Parsing YAML

## Python

```python
import yaml

# Parse YAML file
with open('example.yml', 'r') as file:
    data = yaml.safe_load(file)

# Upload data (example: printing it)
print(data)

# Modify data (example: add a new key-value pair)
data['new_key'] = 'new_value'

# Upload data back to YAML file
with open('example.yml', 'w') as file:
    yaml.dump(data, file)

```

## Ruby

```ruby
require 'yaml'

# Parse YAML file
data = YAML.load_file('example.yml')

# Upload data (example: printing it)
puts data

# Modify data (example: add a new key-value pair)
data['new_key'] = 'new_value'

# Upload data back to YAML file
File.open('example.yml', 'w') { |file| file.write(data.to_yaml) }

```

## Java

Make sure you have the SnakeYAML library in your project. You can add it using Maven:

```xml
<dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>1.28</version>
</dependency>
```

```java
import org.yaml.snakeyaml.Yaml;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.Map;

public class YAMLExample {
    public static void main(String[] args) throws IOException {
        // Parse YAML file
        try (InputStream input = new FileInputStream("example.yml")) {
            Yaml yaml = new Yaml();
            Map<String, Object> data = yaml.load(input);

            // Upload data (example: printing it)
            System.out.println(data);

            // Modify data (example: add a new key-value pair)
            data.put("new_key", "new_value");

            // Upload data back to YAML file
            yaml.dump(data, writer);
        }
    }
}
```

## Groovy

```groovy
@Grab('org.yaml:snakeyaml:1.28')

import org.yaml.snakeyaml.Yaml

// Parse YAML file
def yaml = new Yaml()
def data = yaml.load(new File('example.yml').text)

// Upload data (example: printing it)
println data

// Modify data (example: add a new key-value pair)
data.put("new_key", "new_value")

// Upload data back to YAML file
new File('example.yml').text = new Yaml().dump(data)
```

## Node.js

Make sure you have the js-yaml library installed. You can install it using npm

`npm install js-yaml`

```js
const fs = require('fs');
const yaml = require('js-yaml');

// Parse YAML file
const data = yaml.safeLoad(fs.readFileSync('example.yml', 'utf8'));

// Upload data (example: printing it)
console.log(data);

// Modify data (example: add a new key-value pair)
data['new_key'] = 'new_value';

// Upload data back to YAML file
fs.writeFileSync('example.yml', yaml.safeDump(data));

```

## C #

Make sure you have the YamlDotNet library in your project. You can add it using NuGet:

`Install-Package YamlDotNet`

```csharp
using System;
using System.IO;
using YamlDotNet.Serialization;

class Program
{
    static void Main()
    {
        // Parse YAML file
        var deserializer = new DeserializerBuilder().Build();
        var data = deserializer.Deserialize<Dictionary<string, object>>(
            new StreamReader("example.yml"));

        // Upload data (example: printing it)
        foreach (var kvp in data)
        {
            Console.WriteLine($"{kvp.Key}: {kvp.Value}");
        }

        // Modify data (example: add a new key-value pair)
        data["new_key"] = "new_value";

        // Upload data back to YAML file
        var serializer = new SerializerBuilder().Build();
        File.WriteAllText("example.yml", serializer.Serialize(data));
    }
}

```
