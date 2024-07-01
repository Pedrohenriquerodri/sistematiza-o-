# sistematizacao
Projeto A: API REST com Gerenciamento Completo e Persistência de Dados

Etapas de Implementação
Configuração do Ambiente de Desenvolvimento
Java Development Kit (JDK)
Spring Boot
MySQL ou PostgreSQL
IDE (IntelliJ IDEA, Eclipse, etc.)
GitHub para controle de versão

Criação do Projeto Spring Boot
Utilize o Spring Initializr para criar um novo projeto Spring Boot com as seguintes dependências:
Spring Web
Spring Data JPA
MySQL Driver ou PostgreSQL Driver
Spring Boot DevTools (opcional, para desenvolvimento mais ágil)

Configuração do Banco de Dados
Configure as credenciais do banco de dados no arquivo application.properties ou application.yml.
Exemplo de configuração para MySQL:
properties
spring.datasource.url=jdbc:mysql://localhost:3306/mgs_db
spring.datasource.username=root
spring.datasource.password=senha
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect


Definição das Entidades e Repositórios
Crie a entidade Employee para armazenar as informações dos funcionários.
Defina o repositório EmployeeRepository que estende JpaRepository.
@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    private String phoneNumber;
    private String medicalConditions;
    private String allergies;

    // Getters and setters
}
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
}

Criação dos Controladores REST
Implemente os controladores REST para gerenciar as operações CRUD.
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {
    @Autowired
    private EmployeeRepository employeeRepository;

    @PostMapping
    public Employee createEmployee(@RequestBody Employee employee) {
        return employeeRepository.save(employee);
    }

    @GetMapping("/{id}")
    public Employee getEmployeeById(@PathVariable Long id) {
        return employeeRepository.findById(id).orElseThrow(() -> new ResourceNotFoundException("Employee not found"));
    }

    @PutMapping("/{id}")
    public Employee updateEmployee(@PathVariable Long id, @RequestBody Employee employeeDetails) {
        Employee employee = employeeRepository.findById(id).orElseThrow(() -> new ResourceNotFoundException("Employee not found"));
        employee.setName(employeeDetails.getName());
        employee.setEmail(employeeDetails.getEmail());
        employee.setPhoneNumber(employeeDetails.getPhoneNumber());
        employee.setMedicalConditions(employeeDetails.getMedicalConditions());
        employee.setAllergies(employeeDetails.getAllergies());
        return employeeRepository.save(employee);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<?> deleteEmployee(@PathVariable Long id) {
        Employee employee = employeeRepository.findById(id).orElseThrow(() -> new ResourceNotFoundException("Employee not found"));
        employeeRepository.delete(employee);
        return ResponseEntity.ok().build();
    }
}

Tratamento de Exceções
Crie uma classe ResourceNotFoundException e um manipulador de exceções globais para lidar com erros.
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<?> resourceNotFoundExceptionHandler(ResourceNotFoundException ex) {
        Map<String, String> errorDetails = new HashMap<>();
        errorDetails.put("error", ex.getMessage());
        return new ResponseEntity<>(errorDetails, HttpStatus.NOT_FOUND);
    }
}
