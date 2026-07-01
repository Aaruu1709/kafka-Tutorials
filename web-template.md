# Synchronous Microservice Communication using WebClient (Interview Demo)

## Interview Introduction

> In this example, I will create two microservices:
>
> * Department Service (Provider)
> * Employee Service (Consumer)
>
> Employee Service will call Department Service using **WebClient**.
>
> WebClient is the modern replacement for RestTemplate. It supports both synchronous and asynchronous communication and provides better performance.

---

# Dependency

Add these dependencies:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

---

# Department Service (Provider)

## Create: Department.java

```java
public class Department {

    private Long id;
    private String name;
    private String location;

    // Getter and Setter
}
```

---

## Create: DepartmentController.java

### API 1: Get Department

```java
@RestController
@RequestMapping("/departments")
public class DepartmentController {

    @GetMapping("/{id}")
    public Department getDepartment(@PathVariable Long id) {

        Department dept = new Department();

        dept.setId(id);
        dept.setName("IT");
        dept.setLocation("Pune");

        return dept;
    }
}
```

---

### API 2: Get All Departments

```java
@GetMapping
public List<Department> getAllDepartments() {

    return List.of(
            new Department(101L, "IT", "Pune"),
            new Department(102L, "HR", "Mumbai")
    );
}
```

---

## application.properties

```properties
server.port=8081
```

---

# Employee Service (Consumer)

## Create: Employee.java

```java
public class Employee {

    private Long id;
    private String name;
    private Long departmentId;
    private Department department;

    // Getter and Setter
}
```

---

## Create: Department.java

(Same as Department Service)

```java
public class Department {

    private Long id;
    private String name;
    private String location;

    // Getter and Setter
}
```

---

## Create: WebClientConfig.java

```java
@Configuration
public class WebClientConfig {

    @Bean
    public WebClient webClient() {

        return WebClient.builder()
                .baseUrl("http://localhost:8081")
                .build();
    }
}
```

---

## Create: DepartmentClient.java

### API 1: Get Department by ID

```java
@Service
public class DepartmentClient {

    @Autowired
    private WebClient webClient;

    public Department getDepartment(Long id) {

        return webClient.get()
                .uri("/departments/" + id)
                .retrieve()
                .bodyToMono(Department.class)
                .block();
    }
}
```

---

### API 2: Get All Departments

```java
public List<Department> getAllDepartments() {

    return webClient.get()
            .uri("/departments")
            .retrieve()
            .bodyToFlux(Department.class)
            .collectList()
            .block();
}
```

---

## Create: EmployeeService.java

```java
@Service
public class EmployeeService {

    @Autowired
    private DepartmentClient departmentClient;

    public Employee getEmployee() {

        Employee emp = new Employee();

        emp.setId(1L);
        emp.setName("Arti");
        emp.setDepartmentId(101L);

        Department dept =
                departmentClient.getDepartment(101L);

        emp.setDepartment(dept);

        return emp;
    }
}
```

---

## Create: EmployeeController.java

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;

    @GetMapping
    public Employee getEmployee() {

        return employeeService.getEmployee();
    }
}
```

---

## application.properties

```properties
server.port=8080
```

---

# Testing

Run:

```text
Department Service → 8081
Employee Service   → 8080
```

---

## Test API

```text
GET http://localhost:8080/employees
```

---

# Output

```json
{
  "id": 1,
  "name": "Arti",
  "departmentId": 101,
  "department": {
    "id": 101,
    "name": "IT",
    "location": "Pune"
  }
}
```

---

# Important WebClient Methods (Interview)

## `get()`

> Used to make GET requests.

---

## `uri()`

> Specifies the API endpoint.

---

## `retrieve()`

> Fetches the response body.

---

## `bodyToMono(Class)`

> Converts a single JSON object into a Java object.

Example:

```java
bodyToMono(Department.class)
```

---

## `bodyToFlux(Class)`

> Converts multiple JSON objects into a stream of Java objects.

Example:

```java
bodyToFlux(Department.class)
```

---

## `block()`

> Waits for the response and converts asynchronous execution into synchronous execution.

---

# 30-Second Interview Explanation

> WebClient is the modern HTTP client introduced by Spring as a replacement for RestTemplate. It supports both synchronous and asynchronous communication. In this example, I created a separate DepartmentClient class that uses WebClient to call Department Service APIs. The methods `bodyToMono()` and `bodyToFlux()` convert JSON responses into Java objects, and `block()` is used to make the call synchronous.

---

# 5 Important Interview Points

* WebClient is the replacement for RestTemplate.
* It supports both synchronous and asynchronous communication.
* It is non-blocking and reactive by default.
* `bodyToMono()` is used for a single object.
* `bodyToFlux()` is used for multiple objects.

---

# One-Line Summary

> WebClient is a modern, reactive HTTP client that supports both synchronous and asynchronous microservice communication.
