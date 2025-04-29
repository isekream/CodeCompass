# Debugging Rules

## Mindset & Approach

* **Systematic Process:** Approach debugging systematically, not randomly. Follow a logical process to identify and fix issues.
* **Reproducibility:** The first step is always to reliably reproduce the bug. Understand the exact steps, inputs, and environmental conditions that trigger the issue. If you can't reproduce it, you can't reliably fix it.
* **Understand the Problem:** Clearly define the *actual* problem versus the *expected* behavior. Don't jump to conclusions about the cause; first, understand the symptoms thoroughly.
* **Patience & Persistence:** Debugging can be challenging and time-consuming. Maintain patience, stay focused, and be persistent. Take breaks when stuck to approach the problem with a fresh perspective.
* **Question Assumptions:** Bugs often hide in incorrect assumptions about how code, libraries, or systems behave. Question your own assumptions and verify them.
* **Learn from Mistakes:** Treat every bug as a learning opportunity. Understand the root cause to prevent similar issues in the future. Consider adding a test case that covers the fixed bug.

## Process

1.  **Reproduce:** Reliably reproduce the bug in a controlled environment (preferably development or testing environment).
2.  **Isolate:** Narrow down the scope of the problem. Use techniques like binary search (commenting out code sections), simplifying inputs, or focusing on specific components to pinpoint the area where the bug originates.
3.  **Hypothesize:** Formulate a hypothesis about the potential root cause based on the symptoms and isolated code section.
4.  **Test Hypothesis:** Use debugging tools, logging, or targeted code changes to test your hypothesis. Gather evidence to confirm or refute it.
5.  **Fix:** Once the root cause is confirmed, implement the simplest, clearest fix that addresses the issue without introducing regressions.
6.  **Verify:** Verify the fix by running the reproduction steps again. Ensure the original bug is gone and no new issues were introduced. Run relevant unit, integration, or E2E tests.
7.  **Document (If Necessary):** Document the fix (e.g., in commit messages, issue trackers) explaining the bug, the root cause, and the solution, especially for non-trivial issues.

## Tools & Techniques

* **Debugger:**
    * **Learn Your IDE Debugger:** Master the features of your primary IDE's debugger (e.g., Visual Studio, VS Code, IntelliJ, Xcode, PyCharm).
    * **Breakpoints:** Set breakpoints strategically to pause execution and inspect the program's state (variable values, call stack).
    * **Conditional Breakpoints:** Use conditional breakpoints to pause only when specific conditions are met (e.g., `i > 100`, `user.name == null`).
    * **Stepping:** Use stepping controls (Step Over, Step Into, Step Out, Continue) to navigate code execution line-by-line or function-by-function.
    * **Watch Expressions:** Use watch expressions to monitor the values of specific variables or expressions as you step through the code.
    * **Call Stack:** Examine the call stack to understand the sequence of function calls leading to the current point.
    * **Variable Inspection:** Inspect local variables, parameters, and object properties to understand the program's state at breakpoints.
    * **Expression Evaluation:** Use the debugger's expression evaluator to test hypotheses or check values without modifying code (be cautious of side effects; use "no side effects" options if available).
* **Logging:**
    * **Strategic Logging:** Add temporary logging statements (`console.log`, `print`, `Log.d`, etc.) strategically to trace execution flow and variable values if a debugger isn't practical or available.
    * **Be Specific:** Log meaningful messages, including context and variable values (e.g., `Logging user ID: ${userId} before calling X`).
    * **Log Levels:** Use appropriate log levels (DEBUG, INFO, WARN, ERROR) if using a logging framework.
    * **Remove Temporary Logs:** Remove temporary debugging logs before committing code. Use conditional logging controlled by flags or environments for persistent debug logs.
    * **Centralized Logging:** Analyze logs from centralized logging systems (see `monitoring_logging_rules.md`) to understand issues in deployed environments.
* **Static Analysis & Linters:** Utilize linters and static analysis tools (see `linters_formatters_rules.md`). They often catch potential bugs and logical errors before runtime.
* **Version Control (`git bisect`):** Use `git bisect` to efficiently identify the specific commit that introduced a regression by performing a binary search on the commit history.
* **Rubber Duck Debugging:** Explain the problem and your code, step-by-step, out loud to someone else or even an inanimate object (like a rubber duck). The act of verbalizing often reveals the flaw in logic.
* **Divide and Conquer:** Comment out sections of code or simplify complex functions to isolate the problematic part.
* **Test-Driven Development (TDD):** Writing tests *before* code can help prevent bugs by forcing clear definition of expected behavior and providing immediate verification. Write failing tests that reproduce bugs before fixing them.

## Language/Platform Considerations

* **JavaScript (Browser):** Use Browser Developer Tools (Console, Sources/Debugger, Network tabs). Understand asynchronous debugging (Promises, async/await).
* **JavaScript (Node.js):** Use the Node.js inspector protocol with IDE debuggers or Chrome DevTools (`node --inspect`). Understand the event loop and asynchronous stack traces.
* **Python:** Use `pdb` (command-line), `breakpoint()` (Python 3.7+), or IDE debuggers (VS Code, PyCharm).
* **Java:** Use IDE debuggers (IntelliJ, Eclipse). Understand JVM concepts (heap, stack). Remote debugging is common.
* **C# / .NET:** Use the Visual Studio Debugger. Understand .NET runtime concepts.
* **Ruby:** Use `binding.irb` (built-in), `binding.pry` (Pry gem), `debug.gem` (Ruby 3+), or IDE debuggers.
* **Go:** Use `delve` (command-line debugger) or IDE debuggers. Understand goroutine debugging.
* **Rust:** Use `rust-gdb` or `rust-lldb` (command-line), or IDE debuggers (often via extensions using these).
* **PHP:** Use Xdebug with an IDE or editor extension. Configure `php.ini` settings for error display/logging appropriately per environment.

## Documentation & Learning

* **Document Findings:** For non-trivial bugs, document the root cause and solution in commit messages, pull requests, or issue trackers.
* **Share Knowledge:** Share debugging experiences and techniques within the team.
* **Error Message Literacy:** Learn to read and understand error messages and stack traces provided by the language runtime or frameworks. They often contain crucial clues.
