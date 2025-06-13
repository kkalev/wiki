## @Override explained
The `@Override` annotation in Java is used to indicate that a method is intended to override a method declared in a superclass (or implement a method from an interface). It is one of the most commonly used built-in annotations in Java.

### Purpose of `@Override`
- **Compiler check:** The main benefit is that it tells the compiler to check that you are actually overriding a superclass method. If there’s no matching method in the superclass (or interface), the compiler will generate an error.
- **Readability:** It makes your intentions explicit to other programmers reading your code.

### Example: Using `@Override`

```java
class Animal {
    void makeSound() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Bark");
    }
}
```
Here, `Dog` overrides the `makeSound` method from `Animal`. The `@Override` annotation tells both the compiler and readers that this is an intentional override.

### What happens if you don’t use `@Override`?
Your code will still work, but:
- If you make a typo in the method name or signature, the compiler will not warn you, and you may accidentally create a new method rather than overriding the intended one.
    ```java
    // No error, but this does NOT actually override the superclass method!
    void makesound() { // typo: should be makeSound
        System.out.println("Bark");
    }
    ```
- With `@Override`, you would get a compile-time error:  
    `method does not override or implement a method from a supertype`

### Summary Table

| Scenario                          | With @Override    | Without @Override     |
|------------------------------------|-------------------|-----------------------|
| Correct override                  | Compiles          | Compiles              |
| Typo in method name/signature      | Compile error     | No error, silent bug  |
| Implements interface method        | Allowed           | Allowed               |

---

**In summary:**  
`@Override` is a best practice annotation that makes code safer and more readable, by ensuring your method actually overrides a superclass or interface method. It helps prevent subtle bugs and clarifies your intent.

## Java Beans
**Java Beans** are reusable software components for Java that can be manipulated visually in a builder tool. They are simple Java classes that follow specific conventions, allowing them to be easily used, configured, and combined in graphical environments or frameworks.

### Key Features and Conventions of Java Beans

1. **Public No-Argument Constructor:**  
   Java Beans must have a public constructor with no arguments so that they can be easily instantiated.

2. **Properties with Getters and Setters:**  
   Properties of a bean are accessed via public "getter" and "setter" methods (also called accessor and mutator methods). These follow a naming convention:
   - `getPropertyName()` for getting a property.
   - `setPropertyName(value)` for setting a property.
   - For boolean properties, `isPropertyName()` is used for the getter.

3. **Serializable:**  
   Beans should implement the `Serializable` interface so their state can be saved and restored.

**Example Java Bean:**
```java
import java.io.Serializable;

public class Person implements Serializable {
    private String name;
    private int age;

    public Person() { } // No-arg constructor

    public String getName() { // Getter
        return name;
    }

    public void setName(String name) { // Setter
        this.name = name;
    }

    public int getAge() { // Getter
        return age;
    }

    public void setAge(int age) { // Setter
        this.age = age;
    }
}
```

### Why are Java Beans Useful?
- **Reusability:** Beans can be reused across different applications and contexts.
- **Introspection:** IDEs and frameworks can inspect (introspect) beans to discover their properties, events, and methods.
- **Interoperability:** Beans can be manipulated in builder tools or frameworks (like JavaServer Faces, Spring, etc.).
- **Encapsulation:** Properties are accessed via methods, not directly.

### Usage Examples
- GUI components in Swing (e.g., `JButton`) follow bean conventions.
- Enterprise JavaBeans (EJB) build on the JavaBeans concept for server-side components.
- Beans are common in frameworks like Spring for dependency injection.

---

**Summary:**  
Java Beans are standard Java classes that follow specific conventions (no-arg constructor, getters/setters, Serializable) to allow easy reuse, configuration, and manipulation in Java applications and tools.

## Lambda Syntax and Chained Style
Let’s clarify **lambda syntax** and **chained style** in Java, especially as commonly used in frameworks like Spring.

---

### 1. Lambda Syntax

A **lambda expression** is a concise way to represent an anonymous function (a function without a name) in Java. Lambdas were introduced in Java 8 and are commonly used for passing behavior (code) as data, especially to methods that take **functional interfaces** as parameters.

**Basic Syntax:**
```java
(parameters) -> expression_or_block
```

**Examples:**
```java
// A lambda that takes x and returns x squared:
x -> x * x

// A lambda that takes two numbers and adds them:
(a, b) -> a + b

// A lambda with a code block:
x -> {
    System.out.println(x);
    return x + 1;
}
```

In Spring Security configuration:
```java
http.csrf(csrf -> csrf.disable());
```
Here, `csrf -> csrf.disable()` is a lambda expression where `csrf` is the parameter.

---

### 2. Chained Style

**Chained style** (also known as method chaining or fluent interface) is a programming pattern where you call multiple methods in a single statement, one after another, because each method returns an object that allows the next method to be called.

**Example:**
```java
String result = "hello"
    .trim()
    .toUpperCase()
    .substring(1, 3);
```

In Spring Security configuration:
```java
http
    .authorizeRequests()
    .anyRequest().permitAll()
    .and()
    .csrf().disable();
```
Each method returns an object that lets you call the next method.

---

### Putting It Together

**Lambda syntax** is about providing a function as a parameter:
```java
list.forEach(item -> System.out.println(item));
```

**Chained style** is about calling multiple methods in a row:
```java
builder.setA(1).setB(2).setC(3);
```

**Spring Security Example (combining both):**
```java
http
    .authorizeHttpRequests(authorize -> authorize.anyRequest().permitAll())
    .csrf(csrf -> csrf.disable());
```
- **Lambda syntax**: `authorize -> authorize.anyRequest().permitAll()`
- **Chained style**: Each configuration method returns an object to allow further configuration.

---

### **Summary Table**

| Term          | What it means                      | Example                                  |
|---------------|------------------------------------|------------------------------------------|
| Lambda syntax | Passing a function as a parameter  | `x -> x + 1`                             |
| Chained style | Multiple method calls in a row     | `obj.methodA().methodB().methodC();`     |

---

**In short:**  
- **Lambda syntax** lets you pass bits of code (functions) as data.
- **Chained style** lets you write a sequence of method calls in a single, readable line.  
Both are used together for expressive, concise code—especially in modern Java frameworks!

## More on Lambda Syntax
Java **lambda expressions** are a way to write anonymous functions—blocks of code that can be passed around and executed later. Lambdas are especially useful when working with functional interfaces (interfaces with a single abstract method), such as those used in collections, streams, or event handling.

---

### **Lambda Syntax**

The basic syntax of a lambda is:
```
(parameters) -> expression_or_block
```

- **parameters**: The input parameters (can be omitted if none, types are optional if inferred).
- **->**: The lambda operator.
- **expression_or_block**: The body, which can be a single expression or a block of statements.

---

### **Examples**

#### 1. **No Parameters**

```java
Runnable r = () -> System.out.println("Hello, world!");
r.run(); // prints: Hello, world!
```
- Here, `Runnable` is a functional interface with a single method `run()`.

---

#### 2. **One Parameter**

```java
Consumer<String> printer = s -> System.out.println(s);
printer.accept("Lambda!"); // prints: Lambda!
```
- `Consumer<T>` takes one input and returns nothing.

---

#### 3. **Multiple Parameters**

```java
BinaryOperator<Integer> sum = (a, b) -> a + b;
System.out.println(sum.apply(2, 3)); // prints: 5
```

---

#### 4. **With Type Declarations (Optional if types can be inferred)**

```java
BiFunction<Integer, Integer, Integer> multiply = (Integer a, Integer b) -> a * b;
System.out.println(multiply.apply(4, 5)); // prints: 20
```

---

#### 5. **With a Block Body**

```java
Function<Integer, String> evenOdd = n -> {
    if (n % 2 == 0) return "Even";
    else return "Odd";
};
System.out.println(evenOdd.apply(7)); // prints: Odd
```

---

#### 6. **In Collections and Streams**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
names.forEach(name -> System.out.println(name));
```

**With filter:**
```java
List<String> filtered = names.stream()
    .filter(name -> name.startsWith("A"))
    .collect(Collectors.toList());
// filtered contains "Alice"
```

---

### **When to Use Lambdas**

- When you want to pass behavior as data (e.g., event listeners, strategies).
- To simplify code that uses functional interfaces (Collections, Streams, etc.).
- For concise and readable code, especially for simple operations.

---

### **Summary Table**

| Lambda Expression             | Equivalent Anonymous Class                                             |
|-------------------------------|----------------------------------------------------------------------|
| `() -> System.out.println("Hi")` | `new Runnable() { public void run() { System.out.println("Hi"); } }` |
| `x -> x * x`                  | `new Function<Integer, Integer>() { public Integer apply(Integer x) { return x * x; } }` |
| `(a, b) -> a + b`             | `new BinaryOperator<Integer>() { public Integer apply(Integer a, Integer b) { return a + b; } }` |

---

**In summary:**  
Lambdas provide a concise way to write code that can be passed as parameters, stored, or executed later—greatly simplifying the use of functional interfaces in Java! If you want more specific examples (e.g., event listeners, sorting, etc.), let me know!

## Generics
The syntax `Name<DataType>` that you see in many Java examples is called **generics**. Generics are a feature in Java that allow classes, interfaces, and methods to operate on types specified by the programmer **at compile time**. This enables you to write more flexible and type-safe code.

---

### **What does `Name<DataType>` mean?**

- **`Name`**: This is usually the name of a class or interface—commonly from the Java Collections Framework (like `List`, `Set`, `Map`, etc.), or a functional interface (like `Consumer`, `Function`, etc.).
- **`<DataType>`**: This is the type parameter you specify. It tells Java what type of object the class, interface, or method should work with.

---

### **Examples**

#### 1. Collections

```java
List<String> names = new ArrayList<>();
```
- `List<String>`: A list that **only holds String objects**.
- If you try to add an integer, the compiler will give an error.

#### 2. Functional Interfaces

```java
Consumer<Integer> printer = x -> System.out.println(x);
```
- `Consumer<Integer>`: A consumer that takes an Integer as input.

#### 3. Maps

```java
Map<String, Integer> ages = new HashMap<>();
```
- `Map<String, Integer>`: A map where **keys are Strings** and **values are Integers**.

---

### **Why Use Generics?**

- **Type Safety:** The compiler checks that you only put the correct type into your collection or interface.
- **No Casting Needed:** When you get something out, it's already the right type.
- **Code Reusability:** You can write generic classes and methods that work with any type.

---

### **Custom Generic Example**

You can also create your own generic classes:

```java
class Box<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

// Usage:
Box<String> stringBox = new Box<>();
stringBox.set("hello");
String str = stringBox.get(); // No cast needed!
```

---

### **Summary Table**

| Syntax                | Meaning                                  |
|-----------------------|------------------------------------------|
| `List<String>`        | List containing Strings only             |
| `List<Integer>`       | List containing Integers only            |
| `Map<String, Double>` | Map from String to Double                |
| `Consumer<Boolean>`   | Consumer that takes a Boolean as input   |
| `Box<T>`              | Box can hold any type T (generic class)  |

---

**In short:**  
`Name<DataType>` is Java's way of letting you specify what type of data a class, interface, or method should work with, making your code safer and easier to read.

## Generic Type
In Java, the `<T>` syntax denotes a **generic type parameter**. It's a placeholder for a type that will be specified when the class, interface, or method is used. This feature is called **generics**.

---

### What does `<T>` mean?

- **`T`** is just a name for a type parameter (it could be any identifier, but `T` for "Type" is the convention).
- When you see something like `class MyClass<T>`, it means `MyClass` can work with any type, and `T` will be replaced with that actual type when you use it.

---

### **Example: Generic Class**

```java
class Box<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}
```

- Here, `Box<T>` can hold any type.
- If you write `Box<String>` when creating a new `Box` object, then `T` is replaced with `String`.
- If you write `Box<Integer>` when creating a new `Box` object, then `T` is replaced with `Integer`.

---

### **Example: Generic Method**

```java
public <T> void printArray(T[] array) {
    for (T element : array) {
        System.out.println(element);
    }
}
```
- The `<T>` before the return type means this method is generic and works for any type `T`.

---

### **Why use `<T>`?**

- **Code reusability**: Write code once, use it with any type.
- **Type safety**: Errors are caught at compile time, not runtime.

---

### **Summary Table**

| Syntax            | Meaning                                      |
|-------------------|----------------------------------------------|
| `class MyClass<T>`| Generic class with type parameter T          |
| `T get()`         | Method returns a value of type T             |
| `<T> void foo()`  | Generic method with type parameter T         |

---

**In short:**  
`<T>` is a generic placeholder for a type, letting you write flexible, reusable, and type-safe code in Java.

## Bounded Type parameters for generics
**Bounded type parameters** in the declarations of generic classes in Java allow you to restrict the types that can be used as arguments for the generic type. This gives you more control over the types that can be substituted for the type parameter and allows you to safely use methods and properties of the bounded type within your generic class.

---

### **Syntax**

```java
class ClassName<T extends SomeClass> {
    // ...
}
```

- Here, `T` is a type parameter that must be either `SomeClass` or a subclass (or implementation) of `SomeClass`.

---

### **Why use bounded type parameters?**

- **Type safety:** You can use methods defined in `SomeClass` on `T` inside your generic class, knowing they exist.
- **Flexibility:** You restrict the class to only work with a certain family of types.

---

### **Examples**

#### 1. **Upper Bound with `extends`**

```java
class Box<T extends Number> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
    public double doubleValue() { return value.doubleValue(); }
}
```
- `T` can be any subclass of `Number` (`Integer`, `Double`, `Float`, etc.), but **not** `String` or unrelated types.
- You can safely call `doubleValue()` on `T` because all subclasses of `Number` have this method.

---

#### 2. **Multiple Bounds**

You can specify multiple bounds (at most one class, and multiple interfaces):

```java
class DataProcessor<T extends Number & Comparable<T>> {
    // T must be a subclass of Number and also implement Comparable<T>
}
```

---

#### 3. **Generic Methods with Bounded Type Parameters**

This also works for methods:

```java
public <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) > 0 ? a : b;
}
```

---

### **Summary Table**

| Syntax                                         | Meaning                          |
|------------------------------------------------|----------------------------------|
| `class Box<T extends Number> { ... }`          | T must be Number or subclass     |
| `class Bar<T extends Foo & Serializable> {}`   | T must extend Foo and implement Serializable |
| `<T extends Comparable<T>> void sort(List<T>)` | T must implement Comparable      |

---

**In short:**  
**Bounded type parameters** restrict what types can be used as generic type arguments, enabling you to safely use certain methods or properties in your generic class or method. Use `extends` for classes/interfaces, and you can combine multiple bounds (one class, any number of interfaces).

## Multiple Generic Types
In Java, a generic method or class can have **multiple type parameters**. This allows you to create methods or classes that work with more than one type in a type-safe way.

---

### **Syntax for Multiple Generic Parameters**

When you declare a generic method with multiple type parameters, you list them inside angle brackets, separated by commas:

```java
public <T, U> void printPair(T first, U second) {
    System.out.println("First: " + first);
    System.out.println("Second: " + second);
}
```

- Here, `T` and `U` are two different type parameters.

---

### **Example in Use**

```java
printPair("Hello", 123);      // T=String, U=Integer
printPair(3.14, true);        // T=Double, U=Boolean
```

---

### **With Return Type**

You can also have methods that return one of the generic types:

```java
public <T, U> T getFirst(T a, U b) {
    return a;
}
```

---

### **With Bounded Types**

You can put bounds on each parameter:

```java
public <T extends Number, U extends CharSequence> void display(T number, U text) {
    System.out.println(number + ": " + text);
}
```
- Here, `T` must be a subclass of `Number`, `U` must be a subclass of `CharSequence` (like `String`).

---

### **Multiple Generics in Classes**

Classes can also have multiple type parameters:

```java
public class Pair<T, U> {
    private T first;
    private U second;
    // constructor, getters, setters, etc.
}
```

---

### **Summary Table**

| Syntax Example                           | Description                       |
|------------------------------------------|-----------------------------------|
| `<T, U>`                                | Two generic type parameters       |
| `<K, V>` (common for maps)               | Key and Value generics            |
| `<T extends Number, U extends CharSequence>` | Bounded generics                  |

---

**In short:**  
You can use multiple generic parameters in Java methods or classes by listing them in angle brackets, giving you flexible and type-safe APIs that can operate on more than one type.

## Foreach Statement
Here’s a simple example of using a Java foreach statement (the enhanced for loop) to iterate through the elements of an `ArrayList`:

```java
import java.util.ArrayList;

public class ForeachExample {
    public static void main(String[] args) {
        ArrayList<String> fruits = new ArrayList<>();
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Cherry");

        // Using foreach (enhanced for loop) to iterate over the ArrayList
        for (String fruit : fruits) {
            System.out.println(fruit);
        }
    }
}
```

**Output:**
```
Apple
Banana
Cherry
```

This loop goes through each element in the ArrayList `fruits` and prints it out.
