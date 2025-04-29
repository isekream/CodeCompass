# Spring Boot Rules

## Project Structure & Conventions

- Follow standard Maven/Gradle project structure (src/main/java, src/main/resources, src/test/java).
- Use reverse domain name convention for package names (e.g., com.example.project).
- Place the main application class (annotated with @SpringBootApplication) in a root package above other classes.
- Organize code into layers: controller, service, repository (or dao/connector), dto, model (or entity), exception, config.
- Use meaningful and descriptive names for classes (PascalCase), methods (camelCase), and variables (camelCase).

## Spring Core & Configuration

- Use constructor injection for dependencies. Annotate constructors with @Autowired. Avoid field injection.
- Annotate classes with @Service for business logic, @Repository for data access, and @Component for other general components.
- Prefer application.yml over application.properties for configuration due to better readability.
- Externalize configuration values; avoid hardcoding. Use @ConfigurationProperties for type-safe configuration binding.
- Utilize Spring Profiles (e.g., dev, prod, test) for environment-specific configurations.

## Spring Web (MVC)

- Create REST controllers using @RestController.
- Use specific mapping annotations (@GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping) with clear path definitions.
- Use Data Transfer Objects (DTOs) for request and response payloads to decouple API contracts from internal domain models. Use Lombok (@Data, @Value, @Builder) for DTOs.
- Handle request bodies with @RequestBody and path variables with @PathVariable. Validate request DTOs using Bean Validation (@Valid annotation and constraints like @NotNull, @Size).
- Implement centralized exception handling using @ControllerAdvice and @ExceptionHandler. Define custom exception classes extending RuntimeException or specific Spring exceptions.

## Spring Data JPA

- Define JPA entities in the model or entity package using @Entity. Use Lombok (@Data, @NoArgsConstructor, @AllArgsConstructor) for entities, but be cautious with @Data due to potential performance/JPA issues.
- Create repository interfaces in the repository package extending JpaRepository (or other relevant Spring Data repository interfaces).
- Use Spring Data query methods or @Query annotation for custom JPQL or native SQL queries.
- Manage transactions using the @Transactional annotation, typically at the service layer. Apply it judiciously (read-only for read operations).

## Logging

- Use SLF4j for logging. Leverage Lombok's @Slf4j annotation for easy logger injection.
- Log meaningful messages at appropriate levels (DEBUG, INFO, WARN, ERROR). Log entry/exit points of key methods at DEBUG level. Log errors with stack traces. Avoid logging sensitive information.

## Code Quality

- Ensure all generated Java code adheres to the rules defined in checkstyle.xml (or other configured static analysis tool like PMD).
- Pay attention to rules regarding naming conventions, Javadoc, code complexity, potential bugs (e.g., empty catch blocks, unused variables), and formatting.

## Testing

- Write unit tests using JUnit 5 (@Test). Place tests in src/test/java following the same package structure as the source code.
- Use Mockito for mocking dependencies in unit tests. Use @Mock for non-Spring dependencies and @InjectMocks to inject them into the class under test. Initialize mocks using @ExtendWith(MockitoExtension.class).
- Write integration tests using @SpringBootTest. Use test slices like @WebMvcTest (for controllers), @DataJpaTest (for repositories), @JsonTest (for JSON serialization) where appropriate.
- Use @MockBean to mock Spring-managed dependencies within integration tests (@SpringBootTest or test slices).
- Use assertions from JUnit Jupiter (org.junit.jupiter.api.Assertions) or libraries like AssertJ.
- Aim for high test coverage, using tools like Jacoco to measure it.

## Documentation

- Write Javadoc comments for all public classes, interfaces, methods, and constructors.
- Include a summary sentence, @param tags for all parameters, @return tag for non-void methods, and @throws tag for declared exceptions.
- Use <code> tags for code elements and follow standard Javadoc formatting conventions (3rd person descriptive).
- Use @since to document when features were added and @author for class/interface authorship.

## Lombok Usage

- Utilize Lombok annotations (@Data, @Value, @Getter, @Setter, @ToString, @EqualsAndHashCode, @Builder, @NoArgsConstructor, @AllArgsConstructor, @Slf4j) to reduce boilerplate code.
- Be mindful of the implications of each annotation (e.g., mutability with @Data).

## Security

- Include spring-boot-starter-security for basic security configuration.
- Configure security rules appropriately (e.g., permit/deny access to endpoints).
- Handle sensitive configuration (passwords, API keys) securely using environment variables or secrets management tools, not hardcoded in source code or application.yml.
