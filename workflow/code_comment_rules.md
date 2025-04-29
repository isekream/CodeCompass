# Code Comment Rules

## Purpose & Philosophy

* **Why, Not What:** Comments should primarily explain *why* something is done a certain way, or provide high-level context, rather than simply restating *what* the code does. Well-written, self-explanatory code should minimize the need for comments explaining the "what".
* **Maintainability:** Comments exist to improve the readability and maintainability of the code for other developers (including your future self).
* **Target Audience:** Write comments for the intended audience (fellow developers, potentially including those less familiar with the specific domain or code).

## When to Comment

* **Complex Logic:** Explain sections of code with complex algorithms, non-obvious logic, or intricate business rules.
* **Workarounds & Hacks:** Document reasons for deviations from standard practices, workarounds for bugs (in the code or external systems), or temporary hacks (use `TODO` or `FIXME` conventions).
* **Intent:** Explain the purpose or intent behind a piece of code if it's not immediately clear from the code itself.
* **Rationale for Decisions:** Briefly explain the reasoning behind significant design choices within the code, especially if alternatives were considered (though major decisions belong in ADRs).
* **Public APIs/Functions (Doc Comments):** Document all public functions, classes, methods, modules, and interfaces, explaining their purpose, parameters, return values, and any potential errors or side effects.

## What NOT to Comment

* **Obvious Code:** Do not comment on code that is simple and self-explanatory. Comments like `// increment i` for `i += 1` add noise.
* **Restating the Code:** Avoid comments that merely translate the code line-by-line into natural language.
* **Version Control Metadata:** Do not include commit history, author names, or change dates in comments. This information belongs in the version control system (e.g., Git history).
* **Commented-Out Code:** Remove commented-out code. Version control history tracks previous code versions. If code is temporarily disabled, use feature flags or conditional compilation, and add a comment explaining why it's disabled. If keeping it for reference is essential, add a clear explanation.
* **Closing Brace Comments:** Avoid comments marking the end of blocks (e.g., `// end if`); proper indentation should make block scope clear.

## Types of Comments

* **Documentation Comments (Doc Comments / Docstrings):**
    * Used for generating API documentation (e.g., Javadoc, Python Docstrings, Rust `///` comments).
    * Follow language-specific conventions and tools (e.g., Javadoc tags like `@param`, `@return`, `@throws`; Google or NumPy style for Python docstrings).
    * Describe the function/class/module's purpose, parameters, returns, and any exceptions/panics clearly.
* **Implementation Comments (Inline / Block):**
    * Used to clarify specific parts of the code logic *within* a function or module.
    * Use line comments (`//` or `#`) for short explanations related to a single line or small block.
    * Use block comments (`/* ... */`) for longer explanations or multi-line comments where appropriate (though sequences of line comments are often preferred).
    * Place comments on a separate line *before* the code they refer to, indented at the same level. Avoid trailing comments on the same line as code unless very short and directly relevant.
* **TODO / FIXME / HACK Comments:**
    * Use standardized tags like `TODO:` (minor tasks remaining), `FIXME:` (known issues needing fixing), `HACK:` (workarounds) to mark areas needing attention.
    * Include context, date, and ideally an owner or issue tracker reference (e.g., `// TODO(username/issue-123): Refactor this when API v2 is available`).
    * Regularly review and address these comments.

## Style & Formatting

* **Clarity & Conciseness:** Write clear, concise, and professional comments. Use correct grammar and spelling.
* **Consistency:** Maintain a consistent commenting style throughout the project.
* **Whitespace:** Leave a space between the comment delimiter (`//`, `#`) and the comment text.
* **Keep Updated:** Comments MUST be kept up-to-date with the code they describe. Inaccurate or outdated comments are worse than no comments. Update comments during code refactoring and modifications. Include comment review as part of the code review process.

## Language-Specific Guidelines

### Python
* Use docstrings (triple quotes) for modules, classes, and functions.
* Follow PEP 257 and a consistent documentation style like Google, NumPy, or reStructuredText.
* Example:
  ```python
  def calculate_discount(price, percentage):
      """
      Calculate the discounted price.
      
      Args:
          price (float): The original price
          percentage (float): Discount percentage (0-100)
          
      Returns:
          float: The price after discount
          
      Raises:
          ValueError: If percentage is outside the range 0-100
      """
  ```

### JavaScript/TypeScript
* Use JSDoc for documentation comments.
* Use `//` for single-line comments and `/* */` for multi-line comments.
* Example:
  ```javascript
  /**
   * Calculates the discounted price.
   * @param {number} price - The original price
   * @param {number} percentage - Discount percentage (0-100)
   * @returns {number} The price after discount
   * @throws {Error} If percentage is outside the range 0-100
   */
  function calculateDiscount(price, percentage) { ... }
  ```

### Java
* Use Javadoc for documentation comments.
* Use standardized tags like `@param`, `@return`, `@throws`.
* Example:
  ```java
  /**
   * Calculates the discounted price.
   * @param price The original price
   * @param percentage Discount percentage (0-100)
   * @return The price after discount
   * @throws IllegalArgumentException If percentage is outside the range 0-100
   */
  public double calculateDiscount(double price, double percentage) { ... }
  ```

### Go
* Follow Go's standard comment style.
* Document all exported (public) names.
* Place a comment directly above the identifier being commented.
* Example:
  ```go
  // CalculateDiscount returns the price after applying the given percentage discount.
  // It panics if percentage is outside the range 0-100.
  func CalculateDiscount(price, percentage float64) float64 { ... }
  ```

### C#
* Use XML documentation comments.
* Start with a summary and use tags like `<param>`, `<returns>`, `<exception>`.
* Example:
  ```csharp
  /// <summary>
  /// Calculates the discounted price.
  /// </summary>
  /// <param name="price">The original price</param>
  /// <param name="percentage">Discount percentage (0-100)</param>
  /// <returns>The price after discount</returns>
  /// <exception cref="ArgumentException">Thrown when percentage is outside the range 0-100</exception>
  public double CalculateDiscount(double price, double percentage) { ... }
  ```

## Common Anti-Patterns

* **Self-Deprecating Comments:** Avoid comments like "This is a hack" or "Sorry for the ugly code" without explaining why or providing a plan to fix it.
* **Excessive Commenting:** Over-commenting can clutter code. Don't comment every line or explain concepts familiar to the intended audience.
* **Non-Standard Formatting:** Avoid creating custom formatting styles for comments that deviate from project or language conventions.
* **Misleading Comments:** Never let comments contradict the code. When updating code, always update the corresponding comments.
* **Disabling Linter Rules:** Avoid routinely disabling linter or style checker rules for comments without good reason.

## Automation & Tools

* **Documentation Generators:** Utilize language-specific documentation generators (e.g., Sphinx for Python, JSDoc for JavaScript, Javadoc for Java) to create professional API documentation from code comments.
* **Linters & Style Checkers:** Configure code linters to check comment style and presence (e.g., missing docstrings on public functions).
* **TODO Trackers:** Consider using IDE plugins or tools that track and aggregate TODO-style comments.
* **Comment Reviews:** Include comment quality and accuracy in code review processes.
