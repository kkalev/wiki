# Building a RESTful Web Service
* [Reference](https://spring.io/guides/gs/rest-service)
* Spring [Initializr](https://start.spring.io/)
  - Enable `Spring Web` in dependencies
* The service will handle `GET` requests for `/greeting`, optionally with a `name` parameter in the query string. The `GET` request should return a `200 OK` response with JSON in the body that represents a greeting. It should resemble the following output:
```json
{
    "id": 1,
    "content": "Hello, World!"
}
```
* The `id` field is a unique identifier for the greeting, and `content` is the textual representation of the greeting.
* To model the greeting representation, create a **resource representation class**. To do so, provide a Java record class for the `id` and `content` data, as the following listing (from `src/main/java/com/example/restservice/Greeting.java`) shows:
```java
package com.example.restservice;

public record Greeting(long id, String content) { }
```
* In Spring’s approach to building RESTful web services, **HTTP requests are handled by a controller**.
* These components are identified by the `@RestController` annotation, and the `GreetingController` shown in the following listing (from `src/main/java/com/example/restservice/GreetingController.java`) handles `GET` requests for `/greeting` by returning a new instance of the `Greeting` class:
```java
package com.example.restservice;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

	private static final String template = "Hello, %s!";
	private final AtomicLong counter = new AtomicLong();

	@GetMapping("/greeting")
	public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
		return new Greeting(counter.incrementAndGet(), String.format(template, name));
	}
}
```
* The `@GetMapping` annotation ensures that HTTP `GET` requests to `/greeting` are mapped to the `greeting()` method.
  - There are companion annotations for other HTTP verbs (e.g. `@PostMapping` for `POST`). There is also a `@RequestMapping` annotation that they all derive from, and can serve as a synonym (e.g. `@RequestMapping(method=GET)`).
* `@RequestParam` binds the value of the query string parameter `name` into the `name` parameter of the `greeting()` method. If the `name` parameter is absent in the request, the `defaultValue` of `World` is used.
* The implementation of the method body creates and returns a new `Greetinγ` object with `id` and `content` attributes based on the next value from the `counter` and formats the given name by using the `greeting` template.
* This code uses Spring `@RestController` annotation, which marks the class as a **controller** where every method returns a `domain` object instead of a view.
* The following listing (from `src/main/java/com/example/restservice/RestServiceApplication.java`) shows the application class:
```java
package com.example.restservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class RestServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(RestServiceApplication.class, args);
	}

}
```
----

# Accessing Data with JPA
* [Reference](https://spring.io/guides/gs/accessing-data-jpa)
* Spring [Initializr](https://start.spring.io/)
  - Enable `Spring Data JPA` and `H2 Database`
* In this example, you store `Customer` objects, each annotated as a JPA entity. The following listing shows the `Customer` class (in `src/main/java/com/example/accessingdatajpa/Customer.java`):
```java
package com.example.accessingdatajpa;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Customer {

  @Id
  @GeneratedValue(strategy=GenerationType.AUTO)
  private Long id;
  private String firstName;
  private String lastName;

  protected Customer() {}

  public Customer(String firstName, String lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  @Override
  public String toString() {
    return String.format(
        "Customer[id=%d, firstName='%s', lastName='%s']",
        id, firstName, lastName);
  }

  public Long getId() {
    return id;
  }

  public String getFirstName() {
    return firstName;
  }

  public String getLastName() {
    return lastName;
  }
}
```
* Here you have a `Customer` class with three attributes: `id`, `firstName`, and `lastName`.
* The default constructor exists only for the sake of JPA. You do not use it directly, so it is designated as `protected`. The other constructor is the one you use to create instances of `Customer` to be saved to the database.
* The `Customer` class is annotated with `@Entity`, **indicating that it is a JPA entity**. (Because no `@Table` annotation exists, it is assumed that this entity is mapped to a table named `Customer`.)
* The `Customer` object’s `id` property is annotated with `@Id` so that JPA recognizes it as the object’s ID. The id property is also annotated with `@GeneratedValue` to indicate that the ID should be generated automatically.
* The other two properties, `firstName` and `lastName`, are left unannotated. **It is assumed that they are mapped to columns that share the same names as the properties themselves**.
* Spring Data JPA focuses on using JPA to store data in a relational database. Its most compelling feature is the ability to create repository implementations automatically, at runtime, from a repository interface.
* To see how this works, create a **repository interface** that works with `Customer` entities as the following listing (in `src/main/java/com/example/accessingdatajpa/CustomerRepository.java`) shows:
```java
package com.example.accessingdatajpa;

import java.util.List;

import org.springframework.data.repository.CrudRepository;

public interface CustomerRepository extends CrudRepository<Customer, Long> {

  List<Customer> findByLastName(String lastName);

  Customer findById(long id);
}
```
* The type of entity and ID that it works with, `Customer` and `Long`, are specified in the generic parameters on `CrudRepository`.
* In a typical Java application, you might expect to write a class that implements `CustomerRepository`. However, that is what makes Spring Data JPA so powerful: You need not write an implementation of the repository interface. Spring Data JPA creates an implementation when you run the application.
* The following listing shows the finished `AccessingDataJpaApplication` class (in `src/main/java/com/example/accessingdatajpa/AccessingDataJpaApplication.java`):
```java
package com.example.accessingdatajpa;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class AccessingDataJpaApplication {

  private static final Logger log = LoggerFactory.getLogger(AccessingDataJpaApplication.class);

  public static void main(String[] args) {
    SpringApplication.run(AccessingDataJpaApplication.class);
  }

  @Bean
  public CommandLineRunner demo(CustomerRepository repository) {
    return (args) -> {
      // save a few customers
      repository.save(new Customer("Jack", "Bauer"));
      repository.save(new Customer("Chloe", "O'Brian"));
      repository.save(new Customer("Kim", "Bauer"));
      repository.save(new Customer("David", "Palmer"));
      repository.save(new Customer("Michelle", "Dessler"));

      // fetch all customers
      log.info("Customers found with findAll():");
      log.info("-------------------------------");
      repository.findAll().forEach(customer -> {
        log.info(customer.toString());
      });
      log.info("");

      // fetch an individual customer by ID
      Customer customer = repository.findById(1L);
      log.info("Customer found with findById(1L):");
      log.info("--------------------------------");
      log.info(customer.toString());
      log.info("");

      // fetch customers by last name
      log.info("Customer found with findByLastName('Bauer'):");
      log.info("--------------------------------------------");
      repository.findByLastName("Bauer").forEach(bauer -> {
        log.info(bauer.toString());
      });
      log.info("");
    };
  }

}
```
* The `AccessingDataJpaApplication` class includes a `demo()` method that puts the `CustomerRepository` through a few tests.
* The `demo()` method returns a `CommandLineRunner` bean that automatically runs the code when the application launches.

----
# Serving Web Content with Spring MVC
* [Reference](https://spring.io/guides/gs/serving-web-content)
* Spring [Initializr](https://start.spring.io/)
  - Enable `Spring Web`, `Thymeleaf`, and `Spring Boot DevTools`
* In Spring’s approach to building web sites, **HTTP requests are handled by a controller**. You can easily identify the controller by the `@Controller` annotation.
* In the following example, `GreetingController` handles `GET` requests for `/greeting` by returning the name of a `View` (in this case, `greeting`). A `View` is responsible for rendering the HTML content. The following listing (from `src/main/java/com/example/servingwebcontent/GreetingController.java`) shows the controller:
```java
package com.example.servingwebcontent;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class GreetingController {

	@GetMapping("/greeting")
	public String greeting(@RequestParam(name="name", required=false, defaultValue="World") String name, Model model) {
		model.addAttribute("name", name);
		return "greeting";
	}

}
```
* The `@GetMapping` annotation ensures that HTTP `GET` requests to `/greeting` are mapped to the `greeting()` method.
* `@RequestParam` binds the value of the query string parameter name into the `nam`e parameter of the `greeting()` method. This query string parameter is not required. If it is absent in the request, the `defaultValue` of `World` is used. The value of the name parameter is added to a `Model` object, ultimately making it accessible to the view template.
* The implementation of the method body relies on a view technology (in this case, `Thymeleaf`) to perform server-side rendering of the HTML. Thymeleaf parses the `greeting.html` template and evaluates the `th:text` expression to render the value of the `${name}` parameter that was set in the controller.
* The following listing (from `src/main/resources/templates/greeting.html`) shows the `greeting.html` template:
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head> 
    <title>Getting Started: Serving Web Content</title> 
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
    <p th:text="|Hello, ${name}!|" />
</body>
</html>
```
