# To check what all classes are available to a recipe using binding.pry in Ruby:

Once you're in the pry console, use the ls command to list all available local variables and their methods. This will include the classes currently in scope for your recipe, including any classes included through modules or mixins.

For example:

pry(main)> ls
=> :ingredients, :ingredient, :block_given?, :class
4. Investigate further (optional):

Using the class names from the ls output, you can explore them further with other pry commands such as:

show-doc <class_name>: See the documentation for a particular class.
methods <class_name>: List all methods available for a specific class.
cd <class_name>: Change the current scope to the chosen class for deeper inspection.
By combining binding.pry and these pry commands, you can effectively debug your recipes, understand the available tools at different points, and experiment with different classes and methods to enhance your recipe logic.