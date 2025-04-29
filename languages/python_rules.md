# Python Rules

## Style and Formatting

- Follow PEP 8 guidelines for all Python code.
- Use snake_case for variable and function names.
- Use PascalCase for class names.
- Use 4 spaces for indentation.
- Limit lines to 88 characters (Black formatter default).
- Group imports: standard library, then third-party, then local application imports, separated by blank lines.
- Use docstrings for all public modules, functions, classes, and methods following Google or NumPy style.

## Idiomatic Python

- Use list comprehensions for creating lists based on existing iterables where appropriate, instead of explicit for loops with .append().
- Use generator expressions (expr for item in iterable) when the full list is not needed immediately, especially for large sequences, to save memory.
- Always use the with statement when opening files to ensure they are properly closed.
- Use enumerate() when iterating if both index and value are needed.
- Use dict.get(key, default) or collections.defaultdict instead of checking if key in dictionary before access.
- Iterate over dictionary keys and values using .items().
- Avoid using mutable default arguments (e.g., def func(a=[])) Use None as default and initialize inside the function if needed.

## Performance

- Use built-in functions and libraries (e.g., map, filter, itertools) where applicable as they are often implemented in C.
- Prefer local variables over global variables within loops or frequently called functions.
- Choose the appropriate data structure (list, tuple, set, dict) based on the use case (e.g., use sets for fast membership testing).
- For numerical operations on large datasets, prefer NumPy arrays and vectorized operations over standard Python lists and loops.

## Security

- Never use eval() or exec() on unsanitized user input.
- Sanitize all external input (user input, API responses) before using it in database queries, file paths, or HTML output.
- Use the secrets module for generating cryptographically secure tokens or keys, not the random module.
- Use parameterized queries or an ORM (like Django ORM, SQLAlchemy) to prevent SQL injection vulnerabilities.
- Store sensitive configuration like API keys and passwords in environment variables or a secrets management system, not hardcoded in source code.
