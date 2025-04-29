# IDE Configuration Rules

## Purpose & Goals

* **Consistency:** Ensure a consistent development environment and coding style across all team members, regardless of individual IDE preferences where possible.
* **Productivity:** Leverage IDE features and extensions to improve developer productivity, reduce errors, and streamline common tasks.
* **Onboarding:** Simplify the onboarding process for new team members by providing standard configurations and recommended tools.
* **Code Quality:** Integrate tools like linters, formatters, and static analyzers directly into the IDE for real-time feedback and automated checks.

## Standardization & Consistency

* **`.editorconfig`:** Utilize an `.editorconfig` file in the root of the project repository to define and enforce basic coding style settings across different editors and IDEs. This file MUST be committed to version control.
    * **Settings:** Define core settings like `indent_style` (space), `indent_size` (e.g., 2 or 4 based on language conventions), `end_of_line` (lf), `charset` (utf-8), `trim_trailing_whitespace` (true), `insert_final_newline` (true).
    * **Scope:** Use sections like `[*]` for global settings and `[*.py]`, `[*.js]` for language-specific overrides.
* **Language/Framework Formatting:** Beyond `.editorconfig`, rely on language-specific formatters (like Black, Prettier, `gofmt`, `rustfmt`, `google-java-format`, C# formatting) for detailed rules. Configure the IDE to use these formatters, ideally automatically on save or via shortcuts.
* **Team Agreement:** Agree as a team on the primary IDEs used (if possible) and the core set of configurations and extensions to ensure a baseline consistency. While individual customization is allowed, project-level standards (formatting, linting) MUST be enforced.

## Configuration Files & Settings

* **Project-Specific Settings:** Store project-specific IDE settings within the project repository whenever possible (e.g., VS Code's `.vscode/settings.json`, IntelliJ's `.idea/` directory - carefully manage `.gitignore` for `.idea/` to share essential settings like code style and run configurations, but exclude user-specific files like `workspace.xml`).
* **Key Settings to Standardize (where applicable):**
    * **Formatting:** Ensure the IDE is configured to use the project's agreed-upon formatter (e.g., via `.editorconfig` and language-specific tools). Enable "Format on Save" where feasible.
    * **Linting:** Integrate the project's linters (e.g., ESLint, Flake8, RuboCop, Checkstyle, Clippy) into the IDE for real-time feedback and problem highlighting.
    * **Imports:** Configure "Optimize Imports on Save" or use shortcuts to remove unused imports and sort existing ones according to conventions.
    * **File Encodings:** Default to UTF-8.
    * **Line Endings:** Default to LF (Unix-style).
* **User Settings:** Encourage developers to maintain their personal IDE settings separately from project-committed settings, allowing for individual preferences (themes, keybindings) that don't affect project consistency.

## Extensions & Plugins

* **Recommend Core Extensions:** Maintain a documented list of recommended IDE extensions/plugins for the project's primary languages and frameworks. This helps ensure developers have access to essential tooling for linting, formatting, debugging, testing, language support, and framework integration.
    * **Examples (VS Code):** Prettier, ESLint, Python (Microsoft), Docker, GitLens, Live Server, language-specific packs (Java, C#), framework-specific packs (Vue Volar, Svelte for VS Code).
    * **Examples (IntelliJ/JetBrains):** Language plugins (Python, Go, Rust, etc. if not bundled), Checkstyle-IDEA, Prettier plugin, database tools, GitToolBox, Maven/Gradle helpers.
* **Avoid Conflicting Extensions:** Be mindful of extensions that might conflict with each other or with project-defined formatting/linting rules.
* **Security:** Only install extensions from trusted sources (official marketplaces, reputable publishers). Be cautious with extensions requiring broad permissions.
* **Keep Updated:** Keep IDEs and extensions updated, but coordinate major IDE version updates within the team if they introduce significant changes or compatibility issues.

## Collaboration & Sharing

* **Commit Settings:** Commit shared project settings (`.editorconfig`, `.vscode/settings.json` snippets, relevant `.idea/` files like code style definitions) to the repository.
* **Documentation:** Document the project's IDE setup guidelines, including essential settings and recommended extensions, in the project's `README.md` or a dedicated `CONTRIBUTING.md` or `DEVELOPMENT.md` file.
* **Settings Sync (Optional):** For teams using the same IDE extensively, consider using IDE features or plugins for synchronizing settings across team members (e.g., JetBrains Settings Sync, VS Code Settings Sync), but ensure project-specific settings committed to the repo take precedence where needed.
