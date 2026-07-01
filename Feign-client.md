# Synchronous Microservice Communication using Feign Client (Interview Demo)

## Interview Introduction

> In this example, I will create two microservices:
>
> * Department Service (Provider)
> * Employee Service (Consumer)
>
> Employee Service will call Department Service using **Feign Client**.
>
> Feign Client is a declarative REST client where we only create an interface, and Spring automatically provides the implementation.

---

# Dependency

Add these dependencies in **Employee Service**:

```xml
<!-- Web Dependency -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- OpenFeign Dependency -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
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

### API 1: Get Department By Id

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

## Enable Feign Client

### Main Class

```java
@SpringBootApplication
@EnableFeignClients
public class EmployeeServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(EmployeeServiceApplication.class, args);
    }
}
```

---

## Create: DepartmentClient.java

```java
@FeignClient(
        name = "department-service",
        url = "http://localhost:8081"
)
public interface DepartmentClient {

    @GetMapping("/departments/{id}")
    Department getDepartment(@PathVariable Long id);

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

Open:

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

# Important Feign Annotations (Interview)

## `@EnableFeignClients`

> Enables Feign Client support in the Spring Boot application.

---

## `@FeignClient`

> Creates a REST client automatically for another microservice.

Example:

```java
@FeignClient(
    name = "department-service",
    url = "http://localhost:8081"
)
```

---

## `@GetMapping`

> Maps the external API endpoint.

```java
@GetMapping("/departments/{id}")
Department getDepartment(Long id);
```

---

# 5 Important Interview Points

### 1. Feign Client is Declarative

> We only write an interface, and Spring creates the implementation automatically.

---

### 2. Reduces Boilerplate Code

> No need for `RestTemplate` or `WebClient` code.

---

### 3. Easy Integration with Eureka

> In real projects, Feign Client works well with service discovery using Eureka.

---

### 4. Supports Load Balancing

> It integrates with Spring Cloud LoadBalancer.

---

### 5. Most Common in Spring Boot Microservices

> Many companies prefer Feign because the code is clean and easy to maintain.

---

# 30-Second Interview Answer

> Feign Client is a declarative REST client provided by Spring Cloud for synchronous communication between microservices. We define an interface using `@FeignClient`, and Spring automatically generates the implementation. It reduces boilerplate code, integrates well with Eureka and load balancing, and is widely used in modern Spring Boot microservices.

---

# Flow

```text
EmployeeController
        ↓
EmployeeService
        ↓
DepartmentClient (Feign Interface)
        ↓
Department Service API
```

---

# One-Line Summary

> Feign Client = Interface-based REST communication where Spring automatically handles the HTTP calls.
