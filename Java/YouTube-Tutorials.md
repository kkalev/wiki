* [Reference](https://www.youtube.com/watch?v=GoXwIVyNvX0)
* Lambda [Expressions](https://www.youtube.com/watch?v=LEJ1kGHSXdA)
## Arrays
```
String[] newArr = new String[5];

newArr[0] = "hello";
newArr[1] = "world";
newArr[2] = "jim";
newArr[3] = "bill";
newArr[4] = "tim";
newArr[5] = "jane";

int[] nums = { 1, 3, 10, 5 };

System.out.println(newArr[0]);
```
## Foreach Loops
* Loop in an array
```
int[] arr = { 1, 3, 5, 7 };
for (int element:arr) {
  System.out.println(element);
}
```
## Sets and Lists
* `Set`: A collection of elements that are **unique**
  - `HashSet`: Unordered
  - `TreeSet`: Ordered
```
import java.util.Set;
import java.util.HashSet;
Set<Integer> t = new HashSet<Integer>();

t.add(1);
t.add(2);
t.add(3);
t.add(4);
t.add(4);
t.add(5);

System.out.println("Print the set elements:" + t);
System.out.println("Print the size of the set: " + t.size());
```
* `List`: Like an array but it can change size
```
package project1;

import java.util.ArrayList;

public class Main {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<Integer>();

        list.add(1);
        list.add(2);
        list.add(3);

        System.out.println("List contents: " + list);
    }
}
```
## Maps
* `Map`: **Key-Value pair**
  - `HashMap`: Unordered
  - `TreeMap`: Ordered. The types for the keys need to be the same
  - `LinkedHashMap`: Maintain the order in which we added elements
```
      Map m = new HashMap<>();

      m.put("tim", 4);
      m.put("joe", "jane");

      System.out.println(m.get("tim"));
      System.out.println(m.get("joe"));
      System.out.println(m);
```
* Map Example
```
      Map m = new HashMap<>();

      String str = "Hello my name is Kostas";

      for (char x: str.toCharArray()) {
          if (m.containsKey(x)) {
              m.put(x, (int) m.get(x) + 1);
          } else {
              m.put(x, 1);
          }
      }

      System.out.println("Character frequencies: " + m);
    }
```
## Classes
* `Animal.java`
```
class Animal {
    private String name;
    private int age;

    Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    void setAge(int age) {
        this.age = age;
    }

    void setName(String name) {
        this.name = name;
    }

    void speak() {
        System.out.println("My name is " + name + " and I am " + age + " years old.");
    }
}
```
* `Main.java`
```
package project1;

public class Main {
    public static void main(String[] args) {

        Animal Dog = new Animal("Dog", 5);
        Dog.speak();
    }
}
```
## Inherritance
* Set the variables in the `Animal` class to protected:
```
    protected String name;
    protected int age;
```
* Extend the `Animal` class in `Cat.java`
```
package project1;

public class Cat extends Animal {

    public Cat(String name, int age) {
        super(name, age);
    }

    @Override
    public void speak() {
        System.out.println("Meow! I am " + this.name + " and I am " + this.age + " years old.");
    }
}
```
* Update `Main.java`
```
package project1;

public class Main {
    public static void main(String[] args) {

        Animal Dog = new Animal("Dog", 5);
        Dog.speak();

        Cat cat = new Cat("Whiskers", 3);
        cat.speak();
    }
}
```
## Static
* Create a `Dog` class with:
  - A static `count` variable that belongs to the class itself instead of the class **instances**
  - A static method that prints the `count` variable. The static method will **not** have access to the instance variables
```
package project1;

public class Dog extends Animal {

    private static int count;
    public Dog(String name, int age) {
        super(name, age);
        Dog.count = 0; // Initialize count to 0
    }

    @Override
    public void speak() {
        System.out.println("Woof! I am " + this.name + " and I am " + this.age + " years old.");
    }

    public static void printCount() {
        System.out.println("Total number of Dog instances: " + Dog.count);
    }
  
}
```
* Update the `Main` class
```
package project1;

public class Main {
    public static void main(String[] args) {

        Dog dog1 = new Dog("Dog", 5);
        Dog dog2 = new Dog("Buddy", 4);
        dog1.speak();
        dog2.speak();
        Dog.printCount();

        Cat cat = new Cat("Whiskers", 3);
        cat.speak();
    }
}
```
* We don't use the class instance to access/call the (static) variables or function but the class name itself
## Object Comparisons
```
package project1;

public class Student {
    private String name;

    public Student( String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
    public boolean isEqual(Student other) {
        if (this.name == null || other.name == null) {
            return false;
        }
        return this.name.equals(other.name);
    }
}
```
## Object String Representation
```
public class Student {
    private String name;

    public Student( String name) {
        this.name = name;
    }

    public String toString() {
        return "Student{name='" + name + "'}";
    }
}

public class Main {
    public static void main(String[] args) {

        Student student1 = new Student("Alice");

        System.out.println("Student 1: " + student1);
    }
}
```
## Interfaces
* Completely abstract
* We can only have public methods
* A class `implements` an interface
```
public interface Vehicle {
    void start();
    void stop();
    int getSpeed();
    void setSpeed(int speed);
    String getType();
    void setType(String type);
    String toString();
    boolean isEqual(Vehicle other);

}

public class Car implements Vehicle {
    private String type;
    private int speed;

    public Car(String type) {
        this.type = type;
        this.speed = 0; // Default speed
    }

    @Override
    public void start() {
        System.out.println("Car is starting.");
    }

    @Override
    public void stop() {
        System.out.println("Car is stopping.");
        this.speed = 0; // Reset speed to 0 when stopped
    }

    @Override
    public int getSpeed() {
        return this.speed;
    }

    @Override
    public void setSpeed(int speed) {
        this.speed = speed;
    }

    @Override
    public String getType() {
        return this.type;
    }

    @Override
    public void setType(String type) {
        this.type = type;
    }

    @Override
    public String toString() {
        return "Car{type='" + type + "', speed=" + speed + "}";
    }

    @Override
    public boolean isEqual(Vehicle other) {
        if (other == null || !(other instanceof Car)) {
            return false;
        }
        Car otherCar = (Car) other;
        return this.type.equals(otherCar.type) && this.speed == otherCar.speed;
    }

}
```
## Enums
* Collection of constants
```
public enum Level {
  HIGH,
  MEDIUM,
  LOW;
}
    public static void main(String[] args) {
        Level lvl = Level.HIGH;
    }
```

## Lambdas
* Also known as an anonymous method. A shorter way to write **anonymous classes with only one method**
* Need to use a **functional interface of only one abstract method** (ie `ActionListener`, `Runnable`)
* Syntax: `(arguments) -> {statement/s}`
* Define a simple functional interface (example):
```
@FunctionalInterface
public interface MyInterface {
  public void message();
}
```
* If not using lambda:
```
MyInterface myInterface = new MyInterface {
  public void message {
    // our code
  }
}
```
* Using lambda syntax:
```
MyInterface myInterface = () -> System.out.println("message");
myinterface.message();
```
