# Repositories, Services and Controllers
Here’s a clear explanation of **repositories**, **services**, and **controllers** in Spring Boot, how they are related, and a simple diagram to illustrate the relationship.

---

## 1. **Repositories**
- **Meaning:**  
  A repository is a mechanism for encapsulating storage, retrieval, and search behavior, typically for a collection of objects (e.g., database access).  
- **Annotation:** `@Repository`
- **Role:** Data Access Layer
- **Example:**  
  ```java
  @Repository
  public interface UserRepository extends JpaRepository<User, Long> {
      // CRUD methods inherited from JpaRepository
  }
  ```

---

## 2. **Services**
- **Meaning:**  
  A service contains business logic. It processes data, applies rules, and calls repositories to fetch or save data.
- **Annotation:** `@Service`
- **Role:** Business Logic Layer
- **Example:**  
  ```java
  @Service
  public class UserService {
      private final UserRepository userRepository;
      public UserService(UserRepository userRepository) {
          this.userRepository = userRepository;
      }
      public User getUserById(Long id) {
          return userRepository.findById(id).orElse(null);
      }
  }
  ```

---

## 3. **Controllers**
- **Meaning:**  
  A controller handles HTTP requests/responses. It receives input from the client, calls the appropriate service methods, and returns data or views.
- **Annotation:** `@RestController` or `@Controller`
- **Role:** Web/API Layer
- **Example:**  
  ```java
  @RestController
  @RequestMapping("/users")
  public class UserController {
      private final UserService userService;
      public UserController(UserService userService) {
          this.userService = userService;
      }
      @GetMapping("/{id}")
      public User getUser(@PathVariable Long id) {
          return userService.getUserById(id);
      }
  }
  ```

---

## **How are they related?**

- **Controller** receives HTTP requests and delegates to the **Service**.
- **Service** contains business logic and interacts with **Repository** for data access.
- **Repository** directly interacts with the database.

---

## **Diagram**

````markdown name=layer-diagram.md
```plaintext
+------------------+         +-----------------+         +-------------------+
|    Controller    | ----->  |     Service     | ----->  |    Repository     |
|  (Web/API Layer) |         | (Business Logic)|         | (Data Access)     |
+------------------+         +-----------------+         +-------------------+
        |                          |                             |
    Handles HTTP              Processes data                Interacts with
    requests, parses          and business logic,           Database (CRUD)
    input, prepares           calls repositories
    responses                 for data
```
````

---

**Summary:**  
- **Controller**: Entry point for HTTP requests.
- **Service**: Holds business logic, coordinates between controller and repository.
- **Repository**: Handles all data-related operations.
- **Flow:** Controller → Service → Repository → Database (and back).
----
# CrudRepository vs JpaRepository
The main differences between `JpaRepository` and `CrudRepository` in Spring Data JPA are as follows:

---

### `CrudRepository`

- **Purpose**: Provides basic CRUD (Create, Read, Update, Delete) operations.
- **Methods**: Includes methods like `save()`, `findById()`, `findAll()`, `deleteById()`, `deleteAll()`, etc.
- **Use Case**: Suitable when you only need simple CRUD functionality.
- **Hierarchy**: It is the root interface for Spring Data repositories.

```java
public interface CrudRepository<T, ID> extends Repository<T, ID>
```

---

### `JpaRepository`

- **Purpose**: Extends `CrudRepository` and `PagingAndSortingRepository` and provides JPA-specific methods.
- **Methods**: In addition to all methods from `CrudRepository` and `PagingAndSortingRepository`, it adds:
  - Batch operations: `saveAll()`, `deleteInBatch()`, `deleteAllInBatch()`, etc.
  - JPA-specific methods: `flush()`, `getOne()`, `findAll(Pageable pageable)`, etc.
  - Pagination and Sorting support.
- **Use Case**: When you are using JPA and want additional features like pagination, sorting, and batch operations.
- **Hierarchy**: Sub-interface of `PagingAndSortingRepository`, which is a sub-interface of `CrudRepository`.

```java
public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID>
```

---

## **Summary Table**

| Feature                  | CrudRepository | JpaRepository         |
|--------------------------|:-------------:|:---------------------:|
| Basic CRUD               |      ✔️       |          ✔️           |
| Pagination & Sorting     |      ❌       |          ✔️           |
| Batch Operations         |      ❌       |          ✔️           |
| JPA-specific Methods     |      ❌       |          ✔️           |
| Suitable for JPA         |      ✔️       |          ✔️           |
| Suitable for Simple CRUD |      ✔️       |          ✔️           |
| Suitable for Advanced    |      ❌       |          ✔️           |

---

**In short:**  
- Use `CrudRepository` for simple CRUD operations.
- Use `JpaRepository` for more advanced features like batch operations and pagination, especially when working with JPA.  
- `JpaRepository` **extends** `CrudRepository`, so it includes all of its methods plus more.
----

# RESTful project
Here’s a step-by-step explanation and code snippets for creating a simple **Spring Boot** RESTful API for managing `Student` entities with MariaDB as the database. This uses key Spring Boot features such as annotations, beans, and dependency injection.

---

## 1. **Project Setup**

Use [Spring Initializr](https://start.spring.io/) to generate a new project with these dependencies:
- Spring Web
- Spring Data JPA
- MariaDB Driver

The `build.gradle` is as follows:
```groovy name=build.gradle
plugins {
    id 'org.springframework.boot' version '3.2.5'
    id 'io.spring.dependency-management' version '1.1.4'
    id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.mariadb.jdbc:mariadb-java-client:3.3.3'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```
---

## 2. **Configure application.properties**

Set up your database connection:
```properties name=src/main/resources/application.properties
spring.datasource.url=jdbc:mariadb://localhost:3306/studentdb
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

## 3. **Define the Student Entity**

Use `@Entity` to make the class a JPA entity, and `@Id` to specify the primary key.

```java name=src/main/java/com/example/demo/entity/Student.java
package com.example.demo.entity;

import jakarta.persistence.*;

@Entity
public class Student {
    @Id
    private String username;

    private String name;
    private String surname;
    @Column(length = 10)
    private Long vatNumber; // 10 digit integer, use Long for large numbers

    // Getters and setters
    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getSurname() { return surname; }
    public void setSurname(String surname) { this.surname = surname; }
    public Long getVatNumber() { return vatNumber; }
    public void setVatNumber(Long vatNumber) { this.vatNumber = vatNumber; }
}
```
- **Annotations used:**
  - `@Entity`: Marks the class as a database entity.
  - `@Id`: Marks `username` as the primary key.
  - `@Column(length = 10)`: Ensures the correct column size for VAT number.

---

## 4. **Create the Repository**

Spring Data JPA provides the `JpaRepository` interface. By extending it, you get CRUD operations for free.

```java name=src/main/java/com/example/demo/repository/StudentRepository.java
package com.example.demo.repository;

import com.example.demo.entity.Student;
import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository extends JpaRepository<Student, String> {
    // You can add custom queries here if needed
}
```
- **Annotations used:**
  - None needed; extending `JpaRepository` is enough!

---

## 5. **Create the Service Layer (Optional but recommended)**

This layer contains business logic. Use `@Service` to make it a Spring bean.

```java name=src/main/java/com/example/demo/service/StudentService.java
package com.example.demo.service;

import com.example.demo.entity.Student;
import com.example.demo.repository.StudentRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class StudentService {
    @Autowired
    private StudentRepository studentRepository;

    public Optional<Student> getStudentByUsername(String username) {
        return studentRepository.findById(username);
    }

    public List<Student> getAllStudents() {
        return studentRepository.findAll();
    }
}
```
- **Annotations used:**
  - `@Service`: Registers the class as a Spring bean.
  - `@Autowired`: Injects the repository.

---

## 6. **Create the REST Controller**

Use `@RestController` to make a RESTful web service, and `@GetMapping` for GET endpoints.

```java name=src/main/java/com/example/demo/controller/StudentController.java
package com.example.demo.controller;

import com.example.demo.entity.Student;
import com.example.demo.service.StudentService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/students")
public class StudentController {
    @Autowired
    private StudentService studentService;

    @GetMapping("/{username}")
    public ResponseEntity<Student> getStudentByUsername(@PathVariable String username) {
        return studentService.getStudentByUsername(username)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }

    @GetMapping
    public List<Student> getAllStudents() {
        return studentService.getAllStudents();
    }
}
```
- **Annotations used:**
  - `@RestController`: Marks the class as a REST controller.
  - `@RequestMapping`: Sets the base URL.
  - `@Autowired`: Injects the service.
  - `@GetMapping`: Maps HTTP GET requests.
  - `@PathVariable`: Binds the URL path to a method parameter.

---

## 7. **Run Your Application**

Make sure your MariaDB is running and a database named `studentdb` exists. Then run your Spring Boot app (`DemoApplication.java` is generated for you).

- **GET** `/api/students` → Returns all students.
- **GET** `/api/students/{username}` → Returns a student with the given username.

---

## 8. **How Spring Boot Features Help**

- **Annotations** handle all the wiring (`@Entity`, `@RestController`, `@Autowired`, etc.).
- **Beans** are created automatically for your services and repositories.
- **Spring Data JPA** eliminates boilerplate CRUD code.
- **Spring Boot auto-configuration** sets up defaults, so you need minimal config.

---

### **Summary**

1. **Define your entity** with `@Entity`.
2. **Create a repository** by extending `JpaRepository`.
3. **(Optionally) Add a service layer** for logic.
4. **Implement a REST controller** with `@RestController` and mappings.
5. **Configure MariaDB** in `application.properties`.
6. **Run and test your API!**

If you want the project structure or more details on any step, let me know!

----
### How to use Optional<Student>
```java
Optional<Student> studentOpt = studentRepository.findById("username");

if (studentOpt.isPresent()) {
    Student student = studentOpt.get();
    // do something with student
} else {
    // handle student not found
}
```
