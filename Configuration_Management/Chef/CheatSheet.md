- [Determine if a Chef role is in the current run context](#determine-if-a-chef-role-is-in-the-current-run-context)
- [Determine if a Chef cookbook is in the current run\_context and retrieve its version](#determine-if-a-chef-cookbook-is-in-the-current-run_context-and-retrieve-its-version)
- [Determine if a Chef recipe is included in the node’s run\_context](#determine-if-a-chef-recipe-is-included-in-the-nodes-run_context)


# Determine if a Chef role is in the current run context

If you want to check whether a node run_list includes a specific role (upon expansion), then you could use role? method on the Node object:

`node.role?('name')`

Alternatively, you can see whether either would work for you:

`node.roles.include?('name')`

`node.run_list?('role[name]')`

To check if a Chef role is in the current run context, you can use the following methods:

1. **Using `node.role?('name')`:** This method checks whether a node's run-list includes a specific role. Replace `'name'` with the actual role name you want to verify. For example:

   ```ruby
   if node.role?('my_role')
     # Role 'my_role' is in the run-list
   else
     # Role 'my_role' is not in the run-list
   end
   ```

2. **Using `node.roles.include?('name')`:** This approach checks if the role is part of the fully resolved set of roles for the node. Again, replace `'name'` with the desired role name:

   ```ruby
   if node.roles.include?('my_role')
     # Role 'my_role' is in the fully resolved set
   else
     # Role 'my_role' is not in the fully resolved set
   end
   ```

Remember to adjust the role name accordingly in your Chef recipe. These methods allow you to determine whether a specific role is present during the Chef run. ³²

# Determine if a Chef cookbook is in the current run_context and retrieve its version

To determine if a Chef cookbook is in the current run_context and retrieve its version, you can use the following approach:

```
node.run_context.cookbook_collection[cookbook_name].metadata,version
```

# Determine if a Chef recipe is included in the node’s run_context

To determine if a Chef recipe is included in the `node`'s `run_context`, you have a couple of options:

1. **Using `node.recipe?`:** In principle, `node.recipe?` is the right way to go. However, during the compile phase of your Chef run, `node['recipes']` initially contains only the recipes explicitly defined in your run list. It gets completed during compilation with every `include_recipe` that occurs. So, if you evaluate `node.recipe?('postgresql::server')` before encountering an `include_recipe 'postgresql::server'`, it will return false⁵.

2. **Using `node['recipes']`:** You can check if a recipe is included by using `node['recipes'].include?('postgresql::server')`⁵. This approach considers all recipes that are part of the compilation process, including those added via `include_recipe`.

Remember to choose the method that best suits your specific use case! 😊



In Ruby, the `::` (double colon) is a **namespace resolution operator**. Here's what it does:

1. **Accessing Constants and Methods**:
   - It allows you to access **constants**, **instance methods**, and **class methods** defined within a class or module from anywhere outside that class or module³⁴.
   - For example, if you have a module called `SomeModule`, you can access its constants or methods using `SomeModule::CONSTANT_NAME` or `SomeModule.some_method`⁴.

2. **Namespace Separation**:
   - It helps organize code by providing a way to specify the context or namespace for a particular constant or method.
   - When you see `Module::Constant`, it means `Constant` is defined within `Module`.

Remember, `::` is a powerful tool for managing namespaces and accessing items across different parts of your Ruby code! 😊 ³⁴

Source: Conversation with Copilot, 7/7/2024
(1) Ruby - Operators - Online Tutorials Library. https://www.tutorialspoint.com/ruby/ruby_operators.htm.
(2) syntax - What is Ruby's double-colon `::`? - Stack Overflow. https://stackoverflow.com/questions/3009477/what-is-rubys-double-colon.
(3) Everything You Need to Know About Ruby Operators - RubyGuides. https://www.rubyguides.com/2018/07/ruby-operators/.
(4) Operators - The Ruby Programming Language [Book] - O'Reilly Media. https://www.oreilly.com/library/view/the-ruby-programming/9780596516178/ch04s06.html.
(5) en.wikipedia.org. https://en.wikipedia.org/wiki/Ruby_(programming_language).