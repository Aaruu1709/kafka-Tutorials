# Synchronous Microservice Communication using RestTemplate (Industry Approach)

## Scenario

We have two microservices:

```text
Department Service (Port: 8081)
        ↑
        │ RestTemplate
        │
Employee Service (Port: 8080)
```

Employee Service calls Department Service and gets department details.

---

# Dependency

Add:

```xml
spring-boot-starter-web
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

## Create: RestTemplateConfig.java

```java
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() {

        return new RestTemplate();

    }
}
```

---

## Create: DepartmentClient.java

(Responsible only for external API calls)

```java
@Service
public class DepartmentClient {

    @Autowired
    private RestTemplate restTemplate;

    public Department getDepartment(Long id) {

        return restTemplate.getForObject(
                "http://localhost:8081/departments/" + id,
                Department.class
        );
    }
}
```

---

## Create: EmployeeService.java

(Contains business logic)

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

# Interview Explanation

> In this example, Employee Service communicates with Department Service using RestTemplate, which is a synchronous communication approach. I created a separate DepartmentClient class to handle external API calls, while EmployeeService contains only business logic. This improves separation of concerns, reusability, and testability.

---

# Flow

```text
EmployeeController
        ↓
EmployeeService
        ↓
DepartmentClient
        ↓
RestTemplate
        ↓
Department Service API
```

---

# Why Separate DepartmentClient?

* Keeps business logic separate from API calls.
* Easy to reuse in multiple services.
* Easy to unit test.
* Easy to migrate to Feign Client or WebClient later.
* Follows clean architecture principles.

---

# One-Line Summary

> EmployeeService handles business logic, DepartmentClient handles external API communication, and RestTemplate performs the synchronous REST call.
