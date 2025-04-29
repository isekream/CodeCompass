# Linters & Formatters Rules

## Purpose & Benefits

* **Consistency:** Ensure code style and formatting are uniform across the entire codebase and team, regardless of individual developer preferences. This significantly improves readability and maintainability.
* **Readability:** Automatically formatted code is easier to read and understand, reducing cognitive load for developers.
* **Error Prevention:** Linters analyze code statically to catch potential bugs, logical errors, anti-patterns, and deviations from best practices *before* runtime. They can identify issues like unused variables, potential null pointer exceptions, or suspicious constructs.
* **Enforce Standards:** Codify and automatically enforce agreed-upon coding standards, style guides (like PEP 8 for Python or language-specific guidelines), and security best practices.
* **Improved Code Reviews:** Automating style and basic error checks allows code reviewers to focus on more critical aspects like logic, architecture, and functionality, rather than nitpicking formatting.
* **Developer Productivity:** Automating formatting saves developers time and mental energy spent on manual formatting. Early error detection by linters reduces debugging time later.
* **Learning Tool:** Linters provide immediate feedback, helping developers learn language idioms and best practices.

## General Principles

* **Use Both:** Employ both linters (for code quality, errors, style rules) and formatters (primarily for aesthetic code layout like spacing, line breaks). They serve complementary purposes.
* **Automate Execution:** Linters and formatters provide maximum benefit when run automatically.
* **Consistency is Key:** The primary goal is consistency. Agree on a toolset and configuration for the project and stick to it.

## Configuration

* **Project-Specific Configuration:** Store linter and formatter configurations within the project repository (e.g., `.eslintrc.js`, `pyproject.toml`, `.prettierrc`, `.editorconfig`). This ensures all developers and CI environments use the same rules.
* **Adopt Standard Configurations:** Start with widely accepted standard configurations for your language/framework (e.g., `eslint:recommended`, `black`, `gofmt` defaults) and customize minimally only where necessary and agreed upon by the team.
* **Shared Configuration:** For larger organizations or related projects, consider creating shareable configuration packages to ensure consistency across multiple repositories.
* **Rule Customization:** Customize linter rules deliberately. Disable rules that genuinely conflict with project needs or established patterns, but avoid disabling rules simply to bypass fixing problematic code. Document reasons for disabling specific rules.
* **EditorConfig:** Use an `.editorconfig` file to define basic editor settings like indent style, indent size, line endings, and charset, ensuring consistency across different IDEs and editors.

## Integration

* **Editor Integration:** Integrate linters and formatters directly into developer IDEs/editors for real-time feedback and format-on-save capabilities. This catches issues at the earliest possible stage.
* **Pre-Commit Hooks:** Use pre-commit hooks (managed via tools like `pre-commit` framework, `husky`, `lefthook`) to run formatters and linters *before* code is committed. This prevents poorly formatted or failing code from entering the version control history. Hooks should be fast.
* **CI Pipeline Integration:** Integrate linters and formatters as mandatory checks in the Continuous Integration (CI) pipeline. Fail the build if linting errors occur or if formatting checks fail (`--check` mode). This serves as a safety net for code merged into main branches.

## Choosing Tools

* **Language Ecosystem:** Choose tools that are standard or widely adopted within the specific language ecosystem (e.g., ESLint/Prettier for JS/TS, Black/Flake8/Ruff for Python, `gofmt`/`goimports` for Go, Checkstyle/PMD for Java, `rustfmt`/`clippy` for Rust).
* **Formatter vs. Linter Rules:** When using both, configure them to avoid conflicts. Often, stylistic rules in the linter can be disabled in favor of letting the formatter handle them (e.g., using `eslint-config-prettier` to disable ESLint rules that conflict with Prettier).
* **Extensibility:** Consider tools that allow for plugins or custom rule definitions if project-specific checks are needed.

## Best Practices

* **Fix Issues Promptly:** Address linter warnings and errors as they appear. Don't let them accumulate.
* **Format Before Committing:** Ensure code is formatted correctly before committing (ideally automated via format-on-save or pre-commit hooks).
* **Whole-File Formatting:** Format entire files at once, rather than just changed lines, to maintain consistency. Avoid mixing formatting styles within a single file.
* **Introduce Gradually:** When introducing linters/formatters to an existing project, start with a minimal ruleset or format only new/changed files initially to avoid overwhelming diffs. Gradually increase rule strictness or apply formatting to the entire codebase over time.

## Language-Specific Recommendations

### JavaScript/TypeScript
* **Linters:** ESLint with appropriate plugins (typescript-eslint, react, etc.)
* **Formatters:** Prettier
* **Configuration:** 
  * Use `.eslintrc.js` for ESLint and `.prettierrc` for Prettier
  * Consider `eslint-config-prettier` to avoid conflicts
  * For TypeScript, use `@typescript-eslint/recommended` and optionally `@typescript-eslint/recommended-requiring-type-checking`

### Python
* **Linters:** Flake8 (with plugins), Pylint, or Ruff (newer, faster alternative)
* **Formatters:** Black (for code), isort (for imports)
* **Configuration:** 
  * Use `pyproject.toml` for Black, isort, and Ruff
  * Use `.flake8` or `setup.cfg` for Flake8
  * Consider using both flake8 and black, with flake8 configured to ignore formatting issues handled by black

### Go
* **Linters:** golangci-lint (meta-linter that includes multiple linters)
* **Formatters:** gofmt/goimports (built into the language)
* **Configuration:**
  * `.golangci.yml` for golangci-lint
  * Go's standard tools require minimal configuration

### Java
* **Linters:** CheckStyle, PMD, SpotBugs (formerly FindBugs)
* **Formatters:** Google Java Format, Eclipse formatter
* **Configuration:**
  * Use XML configuration files for CheckStyle and PMD
  * Consider IDE-specific settings files for formatting

### Rust
* **Linters:** Clippy
* **Formatters:** rustfmt
* **Configuration:**
  * Use `rustfmt.toml` for rustfmt
  * Use `clippy.toml` for Clippy
  * Consider enabling pedantic lints for stricter checking

## Handling Special Cases

* **Inline Rule Disabling:** Allow inline disabling of specific linter rules (e.g., `// eslint-disable-next-line`) only with clear comments explaining why the rule is being disabled. This should be an exception, not the norm.
* **Generated Code:** Configure linters to ignore generated files (through `.gitignore`-style patterns or specific directories) to avoid flagging issues in code that's not directly maintained by developers.
* **Legacy Code:** When adding linting to existing projects, consider a phased approach. Start with critical rules (security, correctness) and gradually add style rules as code is touched. Consider using tools like `prettier-eslint` or tools with `--fix` options to automate some changes.
* **Third-Party Code:** Configure linters to ignore third-party code or vendor directories that shouldn't be modified.
* **IDE-Specific Settings:** Provide IDE-specific settings files (e.g., `.vscode/settings.json`) in the repository to help team members configure their editors correctly, but ensure these are optional and don't replace proper tool configuration files.

## Team Adoption

* **Document Choices:** Document which linters and formatters are used, along with their configurations and any custom rules. Explain the rationale behind key decisions.
* **Setup Guide:** Provide a clear setup guide for developers to configure their local environment (IDE plugins, pre-commit hooks).
* **Training:** Consider brief training or documentation on common linting issues and how to resolve them.
* **Evolution:** Periodically review and update linting rules based on team feedback and evolving best practices.
