#Enumerable

In Ruby, the term "enumerable" refers to a module called `Enumerable` that provides a set of methods for working with collections such as arrays, hashes, and other iterable objects. The `Enumerable` module is part of the Ruby core library and is included by various collection classes.

Key characteristics of `Enumerable` include:

1. **Iteration:** Enumerable provides a rich set of iteration methods, allowing you to iterate over each element of a collection and perform operations on them.

   ```ruby
   [1, 2, 3, 4, 5].each { |item| puts item }
   ```

2. **Transformation:** Enumerable methods allow you to transform elements in a collection without changing the original collection.

Methods: map, collect


   ```ruby
   doubled_numbers = [1, 2, 3, 4, 5].map { |item| item * 2 }
   ```

3. **Filtering:** Enumerable provides methods to filter elements based on specific criteria.

Methods: select, reject

   ```ruby
   even_numbers = [1, 2, 3, 4, 5].select { |item| item.even? }
   ```

4. **Aggregation:** Enumerable supports methods for aggregating or combining elements, such as `reduce` or `inject`.

Methods: reduce (or inject), sum, count, min, max

   ```ruby
   sum = [1, 2, 3, 4, 5].reduce(0) { |acc, item| acc + item }
   ```

5. **Searching:** Enumerable includes methods for searching elements within a collection.

Method: find, detect, any?, all?

   ```ruby
   contains_five = [1, 2, 3, 4, 5].include?(5)
   
   numbers = [1, 2, 3, 4, 5]
   found_number = numbers.find { |num| num > 2 }
   ```

Classes like Arrays, Hashes, Ranges, and others include the `Enumerable` module, making these methods available to instances of those classes.

```ruby
[1, 2, 3, 4, 5].class.included_modules.include?(Enumerable) # true
```

By leveraging `Enumerable`, you can write more expressive and concise code when working with collections in Ruby.


In Ruby, the `with_index` method is often used in conjunction with iteration to obtain both the element and its index. This is particularly useful when iterating over an enumerable object, such as an array. Here's an example:

```ruby
fruits = ['apple', 'banana', 'cherry']

fruits.each_with_index do |fruit, index|
  puts "Index: #{index}, Fruit: #{fruit}"
end
```

Output:
```
Index: 0, Fruit: apple
Index: 1, Fruit: banana
Index: 2, Fruit: cherry
```

In this example, `each_with_index` iterates over the array `fruits`, providing both the element (`fruit`) and its index (`index`) on each iteration.

`with_index` can also be used with other enumerable methods like `map`, `select`, and `reduce`. Here's an example using `map`:

```ruby
squared_indices = fruits.map.with_index { |fruit, index| index**2 }

puts squared_indices
```

Output:
```
[0, 1, 4]
```

In this case, `with_index` is used with `map` to create a new array where each element is the square of its index.

The `with_index` method is a convenient way to iterate over collections while keeping track of the index without needing an additional counter variable.


In Ruby, both `map` and `each` are methods used for iterating over elements in a collection, but they serve different purposes.

### `each`:

- **Purpose:**
  - `each` is primarily used for iteration. It iterates through each element of the collection and executes a block of code for each element.
- **Return Value:**
  - The return value of `each` is the original collection; it doesn't create a new collection.
- **Example:**
  ```ruby
  numbers = [1, 2, 3, 4, 5]

  numbers.each do |num|
    puts num
  end
  ```

### `map`:

- **Purpose:**
  - `map` is used for transformation. It iterates through each element of the collection, applies a block of code to each element, and creates a new collection containing the results of applying the block.
- **Return Value:**
  - The return value of `map` is a new array containing the results of applying the block to each element.
- **Example:**
  ```ruby
  numbers = [1, 2, 3, 4, 5]

  squared_numbers = numbers.map { |num| num**2 }

  puts squared_numbers
  ```

### Key Differences:

1. **Return Value:**
   - `each` returns the original collection.
   - `map` returns a new collection transformed based on the block.

2. **Use Case:**
   - Use `each` when you want to iterate over elements without creating a new collection.
   - Use `map` when you want to transform elements and create a new collection.

3. **Usefulness:**
   - `each` is used for its side effects (e.g., printing, updating variables).
   - `map` is used when you want to create a new collection based on the transformation of each element.

Choose between `each` and `map` based on your specific use case. If you need to perform an operation on each element and don't care about the results, use `each`. If you want to transform elements and create a new collection, use `map`.



In Ruby, when you assign a hash to another variable, you're assigning a reference to the same hash, not creating a copy. So, when you modify another variable within the method, it actually modifies the original hash passed to the method.

The behavior of variable assignment and reference in Ruby is consistent both inside and outside of methods. When you assign a complex object like a hash to a variable, you're actually assigning a reference to that object. This holds true whether the assignment occurs inside a method or outside of it.

To avoid this behavior and ensure that modifications to a variable or object don't affect the original, you can create a copy of the object using methods like dup or deep_dup for hashes or arrays.


Yes, this behavior applies to all mutable data types in Ruby, including arrays, hashes, and other objects. When you assign a variable to another variable or pass it as an argument to a method, you're passing a reference to the same underlying object in memory.