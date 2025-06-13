# Tutorials
* Simple Python [tutorial](https://learnbyexample.github.io/100_page_python_intro/cover.html)
* Analytical Python [tutorial](https://www.programiz.com/python-programming/first-program)
* venv [tutorial](https://realpython.com/python-virtual-environments-a-primer/)

# Main notes
## Data Types
*	Integers (int)
*	Floating point (float)
*	Strings (str)
*	Lists (list): `[10, “hello”, 200.3 ]`
*	Dictionaries (dict): Unordered Key:Value pair `{“mykey” : “value”, “name” : “Frankie” }`
*	Tuples (tup): Ordered immutable sequence of objects: `(10, “hello”, 200.3 )`
*	Sets (set): Unordered collection of unique objects: `{ “a”, “b” }`
*	Booleans (bool)

## Formatting
* Using the `.format()` method: `'String here {} then also {}'.format('something1','something2`)`
* Using indexing (indexing starts a 0): `print('The {2} {1} {0}'.format('fox','brown','quick'))
  - Results: "The quick brown fox"
* Assigning keywords: `print('The {q} {b} {f}'.format(f='fox',b='brown',q='quick'))`
* Float formatting follows "{value:width.precision f}": `print("The result was {r:1.5f}".format(r=result))` (`result` is a float)

## Lists
* Lists are **ordered sequences** that can hold a variety of object types
* Form: `[1, 2, 3]`
* Access: `list[<index>]`
* `.append()` method allows you to append a new item to the end of the list
* `.pop()` method allows you to pop an item from the end of the list. If you pass an index location (positive or negative) it will remove the item at that index location.
* `.remove('value')` method removes the item with the passed value
* `.sort()` method sorts the list
* `.reverse()` method reverses the list
* `len(list)` method returns the length of the list
* `del list[<index>]` to delete a specific list member (`del()` does not return the value as `pop()` does)

## Dictionaries
* Dictionaries are **unordered mappings** for storing objects
* Form: `{'key1':'value1','key2':'value2'}`
* Access: `dict['key']`
* When do you choose a dictionary and when a list?
  - In dictionaries objects are retrieved by **key name**. They are unordered and cannot be sorted
  - In lists objects are retrieved by **location**. They can be indexed or sliced
* `del dict['key']` to delete a specific dictionary key
* `.get('key','<message if key not found>')` method to return the value of a key or a specific value if the key was not found (instead of raising an exception). If the second argument is ommited, then the `get()` method will retrun the value `None`
* `.items()` to return the items of a dictionary: `for key, value in <dict>.items():`
* `.keys()` to return all keys of a dictionary
  - Looping in a sorted way: `for name in sorted(<dict>.keys()):`
* `.value()` to return all values of a dictionary

## I/O and Files
* Read user input: `variable = input('<Message>')`
* File descriptor: `myfile = open('<filiename>')`
* Seek into file: `myfile.seek(<index>)`
* Simple read: `contents = myfile.read()`
* Read lines in list context: `myfile.readlines()`

## Operators
* and, or and not

## If Statements
* Control flow syntax makes use of **colons and identation (whitespaces)**
* Syntax of an `if/else` statement (with multiple conditions:
```
if some_condition:
   # execute some code
elif some_other_condition:
   # execute something different
else:
   # execute something else
```
## For Loops
* Syntax of a for loop:
```
my_iterable = [1,2,3]
for item_name in my_iterable:
   print(item_name)
```
## While Loops
* Syntax of a while loop:
```
while some_boolean_condition:
   #do something
```
* You can combine with an else statement:
```
while some_boolean_condition:
   #do something
else:
   #do something else
```
* `break`: Break out of the current closest enclosing loop
* `continue`: Goes to the top of the closest enclosing loop
* `pass`: Does nothing at all. Useful to fill a while/for loop which does nothing

## Useful Operators
* `range`
  - If we include only one number then the operator returns a list of numbers from `0` up to the number before the number we entered
  - If we include two numbers, the the starting number is the one we entered
  - A third argument sets the step size
 ```
 for num in range(0,11,2):
    print(num)
# prints 0, 2, 4, 6, 8, 10
```
* To read from the user we can use thh `input` method: `result = input('What is your name? ')`

## Functions
* Use the `def` keyword to define a function
* You can pass parameter names
* You can set default values for function parameter values
* Use the `return` keyword to return back data from a function
```
def name_of_function(name='Default'):
   '''
   Docstring explains function
   '''
```
### Importing fuctions
* `import <module name>`: Import **all** functions in the file/module `<module name>.py`
  - The function can be called in a dot notation: `<module>.<function>()`
* `from <module> import <function 1>, <function 2>`: Import specific functions from a module
  - You do not need a dot notation in this case

### Object-Oriented Programming
* Class:
```
class NameOfClass():
   # Class Object attribute
   species = 'mammal'
   
   def __init__(self,param1,param2):
      self.param1 = param1
      self.param2 = param2
      
   def some_method(self):
      #perform action
      print(self.param1)
```
* Inheritance:
```
classs Animal():
   def __init__(self):
      print('Animal Created')
   def who_am_i(self):
      print('I am an animal')
   def eat(self):
      print('I am eating')
 
 class Dog(Animal):
    def __init__(self):
      Animal.__init__(self)
   def who_am_i(self):
      print('I am a dog')
```

## Modules and Packages
* We can use `pip install` to install additional libraries
* Import: `from mymodule improt my_func`
* To create a package you have to create a directory with an `__init__.py` file in it (even if empty)
* If we are calling a `.py` file directly then the variable `__name__` will have a value of `__main__`

## Errors and Exceptions handling
* Main keywords:
  - `try`: This is the block of code to be attempted (which may result in an error)
  - `except`: This block of code will execute if there is **an error** in the `try` block
  - `finally`: A final block of code to be executed **regardless** of an error

## Generators
* [Reference](https://www.programiz.com/python-programming/generator)
* In Python, a generator is a function that returns an iterator that produces a sequence of values when iterated over. Similar to defining a normal function, we can define a generator function using the `def` keyword, but instead of the `return` statement we use the `yield` statement.
* Here's an example of a generator function that produces a sequence of numbers
```
def my_generator(n):

    # initialize counter
    value = 0

    # loop until counter is less than n
    while value < n:

        # produce the current value of the counter
        yield value

        # increment the counter
        value += 1

# iterate over the generator object produced by my_generator
for value in my_generator(3):

    # print each value produced by generator
    print(value)
```
* The `yield` keyword is used to produce a value from the generator and **pause** the generator function's execution until the next value is requested.
### Python Generator Expression
* In Python, a generator expression is a concise way to create a generator object. It is similar to a list comprehension, but instead of creating a list, it creates a generator object that can be iterated over to produce the values in the generator.
* Syntax: `(expression for item in iterable)`
* Here, `expression` is a value that will be returned **for each** item in the `iterable`.
* Example: `squares_generator = (i * i for i in range(5))`
* They are **memory efficient**. A normal function to return a sequence will create the entire sequence in memory before returning the result. This is an overkill, if the number of items in the sequence is very large. Generator implementation of such sequences is memory friendly and is preferred since it only produces one item at a time.
