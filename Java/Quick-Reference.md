## Hello World
* The first step in creating this program is to add a public class to your MyApp.java source file. The class must have the same name as the physical source file without the file extension.
* It is legal to have more than one class per file in Java, but **only one public class is allowed, and that name must match the filename**.
* Class block: `public class MyApp {}`
* Next, add the `main` method inside the class. This is the starting point of the application and must always be included:
```
public class MyApp {
 public static void main(String[] args) {
   System.out.print("Hello World");
 }
}
```
## Compile and Run
* Compile: `javac MyApp.java`
* Run: `java MyApp`
## Variables
* Java has eight types that are built into the language. These are called primitives.
* The integer (whole number) types are `byte`, `short`, `int` and `long`.
*  The `float` and `double` types represent floating-point numbers (real numbers).
* The `char` type holds a Unicode character and the `boolean` type contains either a true or false value.
*  Except for these primitive types, every other type in Java is represented by either a class, an interface or an array.
![image](https://github.com/user-attachments/assets/6e3d0779-374d-4196-a93f-9f04bea9c18c)
* Declare variable: `int myInt;`
* Assign value: `myInt = 10;`
* Combine declaration and assignment: `int myInt = 10;`
* Octal and hexademical representation:
```
int myHex = 0xF; // hexadecimal (base 16)
int myOct = 07; // octal (base 8)
```
* constant floating-point numbers in Java are always kept internally as doubles. Therefore, if you try to assign a double to a float you will get an error, because a double has a higher precision than a float. To assign it correctly you can append an `F` character to the constant, which says that the number is in fact a `float`. A more common and useful way to do this is by using an explicit cast.
```
float myFloat = 3.14;   // error: possible loss
                        // of precision
float myFloat = 3.14F; // ok
float myFloat = (float)3.14;
```
* The boolean type can store a Boolean value, which is a value that can only be either true or false. These values are specified with the `true` and `false` keywords.
## String
* The String class in Java is a data type that can hold string literals. String literals are delimited by double quotes. Strings in Java are **immutable**.
```
String a = "Hello";
String b = new String(" World");
```
* The plus sign is used to combine two strings. It is known as the concatenation operator (+) in this context.
* The way to compare two strings is by using the `equals` method of the String class. If the equality operator (==) is used, the memory addresses will be compared instead. `boolean x = a.equals(b);`
* For cases when you need a modifiable string you can use the `StringBuffer` class, which is a mutable string object. `StringBuffer sb = new StringBuffer("Hello");`
* This class has several methods to manipulate strings, such as `append`, `delete` and `insert`.
```
sb.append(" World");   // add to end of string
sb.delete(0, 5);       // remove 5 first characters
sb.insert(0, "Hello"); // insert string at beginning
```
* A `StringBuffer` object can be converted back into a regular string with the `toString` method. `String s = sb.toString();`
## Arrays
* An array is a data structure used for storing a collection of values.
* Declaration:
```
int[] x;
int y[];
```
* The array is allocated with the `new` keyword, followed again by the data type and a set of square brackets containing the length of the array. This is the fixed number of elements that the array can contain. Once the array is created, the elements will automatically be assigned to the default values for that data type. `int y[] = new int[3];`
* Assignment:
```
int[] x = new int[] {1,2,3};
int[] x = {1,2,3};
```
* An important thing to keep in mind about arrays is that **their length is fixed and there are no methods available to change their size**.
* Size of array: `int size = x.length;`
* For cases when a resizable array is needed the `ArrayList` class can be used, which is located in the `java.util` package. `java.util.ArrayList a = new java.util.ArrayList();`
*  Items in the `ArrayList` are stored as the generic `Object` type. 
* The `ArrayList` class has several useful methods to change the array, including: `add`, `set` and `remove`.
```
a.add("Hi");       // add an element
a.set(0, "Hello"); // change first element
a.remove(0);       // remove first element
```
* To retrieve an element from the ArrayList the `get` method is used. The element then **has to be explicitly cast back to its original type**. `String s = (String)a.get(0); `
## Loops
* The `for` loop is used to go through a code block a specific number of times. It uses three parameters. The first parameter initializes a counter and is always executed once, before the loop. The second parameter holds the condition for the loop and is checked before each iteration. The third parameter contains the increment of the counter and is executed at the end of each iteration.
```
for (int i = 0; i < 10; i++)
{ System.out.print(i); }
```
* The first and third parameters can be split into several statements by using the comma operator.
```
for (int k = 0, l = 10; k < 10; k++, l--)
{ System.out.print(k + l); }
```
* The `foreach` loop gives an easy way to iterate through arrays. On each iteration the next element in the array is assigned to the specified variable, and the loop continues to execute until it has gone through the entire array
```
int[] array = { 1,2,3 };
for (int element : array) { System.out.print(element); }
```
## Methods
* Methods are reusable code blocks that will only execute when called.
* The parentheses that follow the method name are used to pass arguments to the method.
```
void myPrint(String s)
{
 System.out.print(s);
}
```
* A method can return a value. The `void` keyword is then replaced with the data type the method will return, and the `return` keyword is added to the method body with an argument of the specified return type.
```
String getPrint()
{
 return "Hello";
}
```
* It is possible to declare multiple methods with the same name as long as the parameters vary in type or number
* **Java is different from many other languages in that all method parameters are passed by value. In fact, they cannot be passed by reference.**
## Class
* A class is a template used to create objects. They are made up of members, the main two of which are fields and methods. Fields are variables that hold the state of the object, while methods define what the object can do.
```
class MyRectangle
{
 int x, y;
 int getArea() { return x * y; }
}
```
* To access a class’s fields and methods from outside the defining class, an object of the class must first be created. This is done by using the `new` keyword, which will create a new object in the system’s memory.
```
public class MyApp
{
 public static void main(String[] args)
 {
 // Create an object of MyRectangle
 MyRectangle r = new MyRectangle();
 }
}
```
* An object is also called an **instance**.
* In addition to creating the object, the members of the class that are to be accessible beyond their package need to be declared as `public` in the class definition.
* The members of this object can now be reached by using the dot operator after the instance name.
```
public static void main(String[] args)
{
 MyRectangle r = new MyRectangle();
 r.x = 10;
 r.y = 5;
 int z = r.getArea() // 50 (5*10)
}
```
* The class can have a constructor. This is a special kind of method used to instantiate (construct) the object. It always has **the same name as the class** and does not have a return type
* To be accessible from another class not in its package it needs to be declared with the `public` access modifier.
* The constructor can have a parameter list, just as any other method.
* Inside the constructor, as well as in other methods belonging to the object, a special keyword called `this` can be used. This keyword is **a reference to the current instance of the class**.
* To support different parameter lists the constructor can be overloaded.
* The `this` keyword can also be used to call one constructor from another. 
```
public MyRectangle()             { this(10,20); }
public MyRectangle(int a)        { this(a,a); }
public MyRectangle(int a, int b) { x = a; y = b; }
```
* The built-in constant `null` is used to represent an uninitialized object. It can only be assigned to objects and not to variables of primitive types. The equal to operator (==) can be used to test whether an object is null.
## Static
* The `static` keyword is used to create fields and methods that can be accessed without having to make an instance of the class. **Static (class) members only exist in one copy, which belongs to the class itself, whereas instance (non-static) members are created as new copies for each new object**.
* This means that static methods cannot use instance members since these methods are not part of an instance.  On the other hand, instance methods can use both static and instance members.
* To access a static member from outside the class, the class name is used followed by the dot operator. This operator is the same as the one used to access instance members, but to reach them an object reference is required. 
```
public static void main(String[] args)
{
 float f = MyCircle.pi;
 MyCircle c = new MyCircle();
 float g = c.r;
}
```
* A static initialization block can be used if the initialization of static fields requires more than one line, or some other logic. This block, in contrast to the constructor, will only be run once, at the same time as the static fields are initialized.
```
static int[] array = new int[5];
static
{
 int i = 0;
 for(int element : array)
   element = i++;
} 
```
## Inheritance
* Inheritance allows a class to acquire the members of another class.
```
// Superclass (parent class)
class Fruit
{
 public String flavor;
}
// Subclass (child class)
class Apple extends Fruit
{
 public String variety;
} 
```
* A class in Java **may only inherit from one superclass**, and if no class is specified it will implicitly inherit from Object. Therefore, Object is the root class of all classes.
* You can test to see whether an object can be cast to a specific class by using the `instanceof` operator. This operator returns true if the left side object can be cast into the right side type without causing an exception. `Apple c = (f instanceof Apple) ? (Apple)f : null;`
## Overriding
* A member in a subclass can redefine a member in its superclass. This is most often done to give instance methods new implementations.
```
class Rectangle
{
 public int w = 10, h = 10;
 public int getArea() { return w * h; }
}
class Triangle extends Rectangle
{
 public int getArea() { return w * h / 2; }
} 
```
* In order to show that this override was intentional, the `@Override` annotation should be placed before the method.
* To prevent an instance method from being overridden in subclasses, it can be declared with the `final` method modifier. `public final int getArea() { return w * h; }`
* An overridden method can still be accessed from inside the subclass’s instance methods by using the `super` keyword. 
## Packages and Import
* Packages are used to avoid naming conflicts and to organize code files into different directories. 
* In Java, **the directory a file belongs to, relative to the project’s source directory, corresponds to the package name**.
* To assign a code file to a package, for example “mypackage”, it must be moved to a folder by that name, under the project directory. Furthermore, **the file must specify which package it belongs to by using the package keyword followed by the package name (and path)**.
* There may only be one package statement in each source file and it must be the first line of code, except for any comments. Note that the naming convention for packages is all lowercase. `package mypackage;`
* Packages may be any number of directory levels deep and the levels in the hierarchy are separated by dots. For example, if the “mypackage” folder containing the code file is placed in a project folder called “sub”, the package declaration would need to look like this: `package sub.mypackage;`
* Say this file contains a public class called `MyClass`. To access `MyClass` from another source file there are two options. The first is to type the fully qualified name: `sub.mypackage.MyClass m;`
* The second option is to shorten the fully qualified name by including the class with the `import` keyword. `import mypackage.sub.MyClass;`
* In addition to importing a specific class, all types inside of a package can be imported by using an asterisk (*). `import java.util.*;`
* A third variation of the import statement is the static import, which imports all static members of a class. Once the static members are imported, they can be used without having to specify the class name.
```
import static java.lang.Math.*;
// ...
double pi = PI; // Math.PI
```
## Access Levels
* There are four access levels available in Java. These are `public`, `protected`, `private` and `package-private`. `Package-private` cannot be explicitly declared by using a keyword. Instead, it is the **default** access level for every member in Java.
```
public int myPublic;        // unrestricted access
protected int myProtected;  // package or subclass access
int myPackage;              // package access
private int myPrivate;      // class access
```
* The most restrictive access level is `private`. Members with this level **can only be used inside of the enclosing (containing) class**.
```
package mypackage;
public class MyApp
{
 public int myPublic;
 protected int myProtected;
 int myPackage;
 private int myPrivate;
 void test()
 {
 myPublic = 0; // allowed
 myProtected = 0; // allowed
 myPackage = 0; // allowed
 myPrivate = 0; // allowed
 }
}
```
* `Package-private` members can be accessed **anywhere within the containing package, but not from another package**.
```
package mypackage;
public class MyClass
{
 void test(MyApp m)
 {
 m.myPublic = 0; // allowed
 m.myProtected = 0; // allowed
 m.myPackage = 0; // allowed
 m.myPrivate = 0; // inaccessible
 }
}
```
* `Protected` members are accessible **within subclasses and within the containing package**. 
```
package newpackage;
import mypackage.MyApp;
public class MyClass extends MyApp
{
 void test()
 {
 myPublic = 0; // allowed
 myProtected = 0; // allowed
 myPackage = 0; // inaccessible
 myPrivate = 0; // inaccessible
 }
} 
```
* The `public` modifier gives unrestricted access from anywhere the member can be referenced.
```
package newpackage;
import mypackage.MyApp;

public class MyClass
{
 void test(MyApp m)
 {
 m.myPublic = 0; // allowed
 m.myProtected = 0; // inaccessible
 m.myPackage = 0; // inaccessible
 m.myPrivate = 0; // inaccessible
 }
}
```
## Constants
* A variable in Java can be made into a constant by adding the `final` keyword before the data type.
## Interface
* An interface is a type that decouples “interface” from implementation. It specifies a contract between its implementors and objects that call its methods (and access its constants). They are defined with the `interface` keyword followed by a name and a code block. `interface MyInterface {}`
* When an interface is not nested inside another type, its access level can be either package-private or public, just as any other top-level member.
* **Interface members must always be public**, and since this is the default access level in interfaces this modifier can be left out.
* Any field created in an interface will **implicitly be declared as static final**, so these modifiers can also be left out.
* A class can implement an interface by using the `implements` keyword after the class name. Note that although a class can only inherit from one superclass it may implement any number of interfaces, by specifying them in a comma separated list.
## Enum
* An enumeration is a type that consists of a fixed list of named constants. To create one, the `enum` keyword is used followed by a name and a code block, containing a comma separated list of constant elements.
```
enum Speed
{
 STOP, SLOW, NORMAL, FAST
}
```
* The enum constants are accessed as if they were static fields of a class. `Speed s = Speed.SLOW;`
## Exception Handling
* We use a `try-catch` statement. This statement consists of a `try` block containing the code that may cause the exceptions, and one or more `catch` clauses.
*  If the `try` block executes successfully the program will continue running after the `try-catch` statement, but if an exception occurs, execution will then be passed to the first `catch` block able to handle that exception type.
```
import java.io.*;
// ...
try {
 FileReader in = new FileReader("Missing.file");
}
catch(FileNotFoundException e) {}
```
* Bear in mind that **a more general exception needs to be caught after a more specific exception**.
* As the last clause in a try-catch statement, a `finally` block can be added. This block is used to clean up resources allocated in the try block and will always execute whether or not there is an exception. 
* When a situation occurs that a method cannot recover from, it can generate its own exception to signal to the caller that the method has failed. This is done by using the `throw` keyword followed by a new instance of a `Throwable` type. `throw new Throwable("My Throwable");`
