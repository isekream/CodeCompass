# Java Rules (General)

## Naming & Formatting Conventions

* **Style Guide:** Follow a consistent style guide. The Google Java Style Guide is a widely accepted standard.
* **Formatting:**
    * Use 4 spaces for indentation (not tabs).
    * Use K&R style "Egyptian brackets" for blocks (opening brace on the same line, no line break before it).
    * Use curly braces `{}` for all `if`, `else`, `for`, `do`, `while` statements, even single-line ones.
    * Follow standard whitespace usage around operators, commas, colons, semicolons.
    * Limit line length (e.g., 100 characters) for readability.
    * Use tools like `google-java-format` or IDE formatters (configured consistently) to enforce formatting automatically.
* **Naming:**
    * **Packages:** All lowercase, single words or concatenated words (e.g., `com.example.project.utilities`). Use reverse domain name convention.
    * **Classes/Interfaces/Enums/Annotations:** `PascalCase`, typically nouns or noun phrases (e.g., `UserService`, `Readable`).
    * **Methods:** `camelCase`, typically verbs or verb phrases (e.g., `calculateTotal`, `getUser`).
    * **Variables (Fields, Parameters, Local):** `camelCase`, starting with lowercase. Names should be descriptive. Avoid single-letter names except for temporary/loop variables with very small scope.
    * **Constants (`static final`):** `SCREAMING_SNAKE_CASE` (all uppercase with underscores) (e.g., `MAX_RETRIES`, `DEFAULT_PORT`).
    * **Type Parameters (Generics):** Single capital letter (e.g., `T`, `E`, `K`, `V`) or descriptive `PascalCase` name if more complex.
* **Imports:**
    * Organize imports: `java`, `javax`, `org`, `com`, etc., then static imports last.
    * Do not use wildcard imports (`import java.util.*;`), import specific classes.
    * Use IDE "Optimize Imports" feature to clean up unused imports and sort them.

## Object Creation & Destruction

* **Static Factory Methods:** Consider static factory methods instead of public constructors where appropriate (e.g., for more descriptive names, potential caching, returning subtypes). (Effective Java Item 1)
* **Builder Pattern:** Use the Builder pattern when constructors have many parameters, especially optional ones, for better readability and safety. (Effective Java Item 2)
* **Singletons:** Enforce singleton properties using a private constructor and a public static factory method or, preferably, a single-element enum type. (Effective Java Item 3)
* **Dependency Injection:** Prefer dependency injection (DI) frameworks (like Spring, Guice) or manual DI (passing dependencies via constructors) over hardwiring resources or using singletons for dependencies. (Effective Java Item 5)
* **Avoid Unnecessary Objects:** Reuse immutable objects. Avoid creating new objects needlessly, especially within loops (e.g., prefer `Integer.valueOf(1)` over `new Integer(1)` - relies on caching). Be mindful of autoboxing performance implications in tight loops. (Effective Java Item 6)
* **Resource Management:** Use `try-with-resources` for resources that implement `AutoCloseable` (like streams, connections) to ensure they are always closed correctly. Avoid `try-finally` for resource closing. (Effective Java Item 9)
* **Avoid Finalizers/Cleaners:** Do not rely on `finalize()` methods or `java.lang.ref.Cleaner`. They are unpredictable, often dangerous, and negatively impact performance. Use `try-with-resources` or explicit termination methods for resource cleanup. (Effective Java Item 8)

## Classes & Interfaces

* **Minimize Accessibility:** Make classes and members as private as possible (use `private`, package-private (default), `protected`, `public` appropriately). Restrict accessibility to reduce coupling and improve maintainability. (Effective Java Item 15)
* **Immutability:** Favor immutability for classes where possible. Make fields `final` unless they need to be mutable. Immutable objects are simpler, inherently thread-safe, and can be shared freely. (Effective Java Item 17)
* **Composition over Inheritance:** Prefer composition and forwarding over implementation inheritance. Inheritance violates encapsulation and can be brittle. Use inheritance only when a true "is-a" relationship exists and benefits outweigh the risks. (Effective Java Item 18)
* **Interfaces over Abstract Classes:** Prefer interfaces to define types. Use interfaces for capabilities that can be implemented by unrelated classes. Use abstract classes only when providing substantial implementation assistance or when evolving the type requires adding methods later (though default methods in interfaces mitigate this). (Effective Java Item 20)
* **Design for Inheritance or Prohibit It:** If designing a class for inheritance, document its self-use patterns and provide protected hooks. Otherwise, declare the class or methods `final` or use private constructors to prohibit subclassing. (Effective Java Item 19)
* **Minimize Mutability:** If a class cannot be fully immutable, limit its mutability. Make fields `final` where possible. (Effective Java Item 17)

## Methods

* **Check Parameters:** Validate method parameters for validity at the beginning of the method, especially for public APIs. Throw appropriate exceptions (e.g., `IllegalArgumentException`, `NullPointerException`) on failure. Document preconditions. (Effective Java Item 49)
* **Defensive Copies:** Make defensive copies of mutable parameters in constructors and accessors for immutable classes to protect invariants. (Effective Java Item 50)
* **Method Signatures:** Design method signatures carefully. Avoid long parameter lists (consider builders or parameter objects). Choose parameter types based on interfaces rather than concrete implementations where possible. (Effective Java Item 51, 52)
* **Return `Optional` Judiciously:** Use `Optional<T>` as a return type to explicitly represent the potential absence of a result. Do *not* use `Optional` for fields, method parameters, or collection elements. (Effective Java Item 55)
* **Return Empty Collections/Arrays:** Return empty collections or zero-length arrays instead of `null` when a method returns a collection/array and there are no results. (Effective Java Item 54)
* **Keep Methods Short:** Methods should be small and focused, ideally doing one thing well.

## Error Handling (Exceptions)

* **Checked vs. Unchecked:** Use checked exceptions for recoverable conditions where the caller should be forced to handle the failure. Use unchecked exceptions (subclasses of `RuntimeException`) for programming errors (preconditions violated, impossible states) or unrecoverable system issues. (Effective Java Item 70, 71)
* **Use Standard Exceptions:** Prefer standard Java exceptions (`IllegalArgumentException`, `IllegalStateException`, `NullPointerException`, `IndexOutOfBoundsException`) over custom ones where they accurately represent the failure condition. (Effective Java Item 72)
* **Document Exceptions:** Document all exceptions thrown by a method (both checked and unchecked) using the `@throws` Javadoc tag. (Effective Java Item 74)
* **Include Failure-Capture Information:** Exceptions should include detailed information relevant to the failure (e.g., invalid parameter values) in their message. (Effective Java Item 75)
* **Avoid Empty Catch Blocks:** Never ignore exceptions by leaving catch blocks empty. At minimum, log the exception.
* **Cleanup:** Use `try-with-resources` or, if necessary, `try-finally` to ensure resources are released even when exceptions occur.

## Concurrency

* **Synchronize Access:** When multiple threads access mutable shared state, synchronize access using `synchronized` blocks/methods, `java.util.concurrent.locks`, or concurrent collections. (Effective Java Item 78)
* **Minimize Synchronization Scope:** Keep `synchronized` blocks as small as possible to reduce contention. Do not synchronize long-running operations or operations that might block (like I/O).
* **Prefer Concurrency Utilities:** Favor high-level concurrency utilities from `java.util.concurrent` (Executors, concurrent collections, synchronizers like CountDownLatch, CyclicBarrier, Semaphore) over low-level primitives like `wait`, `notify`, and raw `Thread` manipulation. (Effective Java Item 81)
* **Use Concurrent Collections:** Use concurrent collections (e.g., `ConcurrentHashMap`, `CopyOnWriteArrayList`) instead of externally synchronized standard collections (`Collections.synchronizedMap`, `Collections.synchronizedList`) for better scalability. (Effective Java Item 81)
* **`volatile`:** Use `volatile` cautiously for simple flags or ensuring visibility of references, but it does not guarantee atomicity for compound actions (like `i++`). Prefer atomic variables (`AtomicInteger`, `AtomicReference`) for atomic operations. (Effective Java Item 78)
* **Thread Pools:** Use `ExecutorService` and thread pools for managing threads effectively instead of creating threads manually. (Effective Java Item 80)
* **Avoid Deadlocks:** Be careful with lock ordering when acquiring multiple locks to prevent deadlocks.

## Performance

* **Write Good Code First:** Focus on clean design and correct implementation first; optimize only when necessary and guided by profiling.
* **Measure Performance:** Use profiling tools (JProfiler, YourKit, VisualVM) to identify actual performance bottlenecks before attempting optimization. Don't guess.
* **String Concatenation:** Use `StringBuilder` (or `StringBuffer` if thread safety is needed, though usually not) for concatenating multiple strings, especially in loops. Avoid using the `+` operator repeatedly in loops as it creates intermediate String objects.
* **Prefer Primitives:** Use primitive types (`int`, `float`, `boolean`) instead of their wrapper equivalents (`Integer`, `Float`, `Boolean`) where possible to avoid autoboxing overhead and reduce memory usage.
* **Efficient Data Structures:** Choose appropriate collection types (e.g., `ArrayList` vs. `LinkedList`, `HashMap` vs. `TreeMap`) based on access patterns and performance characteristics. Be mindful of their time/space complexity.
* **Streams API:** Use the Streams API (`java.util.stream`) for expressive data manipulation, but be aware that for performance-critical hot paths, traditional loops might sometimes be faster. Profile if necessary.
* **Avoid Unnecessary Object Creation:** Be mindful of object creation within tight loops or frequently called methods. Reuse objects where feasible.

## Generics & Collections

* **Use Generics Properly:** Provide the appropriate generic types when using collections to enable compile-time type checking.
* **Wildcards:** Use bounded wildcards (`? extends T`, `? super T`) appropriately to maximize API flexibility.
* **PECS Principle:** "Producer Extends, Consumer Super" - Use `<? extends T>` when you only get values out (reading), and `<? super T>` when you only put values in (writing).
* **Collection Choice:** Select the appropriate collection implementation based on the specific requirements:
  * Need ordered, indexed access? → ArrayList
  * Frequent insertions/removals at both ends? → LinkedList or ArrayDeque
  * Need unique elements? → HashSet or LinkedHashSet (if order matters)
  * Need key-value pairs? → HashMap or TreeMap (if sorted keys needed)
  * Need thread safety? → ConcurrentHashMap, CopyOnWriteArrayList
* **Prefer Interfaces:** Declare variables using collection interfaces (`List`, `Set`, `Map`) rather than specific implementations (`ArrayList`, `HashSet`, `HashMap`) to allow for flexibility in implementation.

## Java 8+ Features

* **Lambdas:** Use lambda expressions for concise implementation of functional interfaces. Keep lambdas small and focused.
* **Method References:** Prefer method references (`Class::method`) over lambdas when they express the same functionality more clearly.
* **Stream API:** Use the Stream API for operations on sequences of elements, particularly for transforming collections. Chain operations like filter, map, and collect for expressive data processing.
* **CompletableFuture:** Use CompletableFuture for asynchronous programming with complex composition of future operations.
* **Optional:** Use Optional to represent values that may be absent, not as a general-purpose null replacement.
* **Default Methods:** Leverage default methods in interfaces to evolve APIs without breaking existing implementations.
* **Date-Time API:** Use the modern `java.time` package (JSR-310) instead of legacy date/time classes.

## General

* **Use Standard Libraries:** Prefer using functionalities provided by the Java Standard Library and well-established third-party libraries over reinventing the wheel.
* **Keep Dependencies Updated:** Regularly update dependencies to benefit from bug fixes, performance improvements, and security patches. Use build tool features or vulnerability scanners to manage dependencies.
* **Code Reviews:** Participate in code reviews to share knowledge, ensure quality, and maintain consistency.
