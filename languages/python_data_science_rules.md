# Python (Data Science Focus) Rules

## General Python & Style

* **Follow Python Rules:** Adhere to all rules defined in `RuleFiles/languages/python_rules.md`, especially PEP 8 for general style, naming, and formatting.
* **Use Linters/Formatters:** Employ tools like `Black`, `flake8` (with relevant plugins like `flake8-bugbear`), or `Ruff` to enforce style and catch common errors automatically. Configure via `pyproject.toml`.
* **Type Hinting:** Use type hints (Python 3.5+) for function signatures and variables, especially in library code or complex functions, to improve readability and allow static analysis. Use tools like `mypy`.
* **Virtual Environments:** Always use virtual environments (`venv`, `conda`) to manage project dependencies and isolate them from system Python or other projects.
* **Dependency Management:** List dependencies explicitly with versions (or ranges) in `requirements.txt` or preferably `pyproject.toml` (using tools like Poetry or PDM). Regularly update dependencies and check for vulnerabilities.

## Project Structure

* **Standard Layout:** Follow a standardized project structure for data science projects. A common structure includes:
    * `data/`: Contains raw, intermediate, and processed data.
        * `data/raw/`: Original, immutable data.
        * `data/interim/`: Intermediate data transformations.
        * `data/processed/`: Final datasets ready for analysis/modeling.
    * `notebooks/`: Jupyter notebooks for exploration, experimentation, and visualization. Use clear naming conventions (e.g., numbered prefixes for order: `01-initial-exploration.ipynb`). Keep notebooks focused; refactor reusable code into scripts.
    * `src/` (or project name): Source code as Python modules/packages.
        * `src/data/`: Scripts for data loading, cleaning, processing.
        * `src/features/`: Scripts for feature engineering.
        * `src/models/`: Scripts for model training, prediction, evaluation.
        * `src/visualization/`: Scripts for generating visualizations (if complex).
    * `tests/`: Unit and integration tests for source code.
    * `config/`: Configuration files (e.g., YAML, TOML) for parameters, paths, etc.
    * `models/`: Serialized trained models (consider Git LFS or external storage for large models).
    * `references/` (Optional): Data dictionaries, manuals, external documentation.
    * `Makefile` (Optional): For automating common tasks (data processing, training).
    * `README.md`: Essential project documentation (see `readme_rules.md`).
    * `pyproject.toml` / `requirements.txt`: Dependency specification.
* **README:** Maintain a clear `README.md` explaining the project's purpose, setup, how to run analyses/models, and the directory structure.
* **Configuration:** Separate configuration (file paths, model parameters, thresholds) from code. Use configuration files (YAML, JSON, TOML) or environment variables.

## Data Handling (Pandas)

* **Vectorization:** Prioritize vectorized operations on DataFrames and Series over explicit iteration (e.g., `for` loops, `.apply()` with simple functions). Vectorized operations (using NumPy under the hood) are significantly faster.
* **Avoid Loops:** Avoid iterating over DataFrame rows (`.iterrows()`, `.itertuples()`). Look for vectorized alternatives or use `.apply()` with optimized functions if necessary, but be aware `.apply()` can still be slow.
* **Method Chaining:** Use method chaining (`.pipe()`) for sequences of operations on DataFrames to improve readability and avoid creating many intermediate variables.
* **Efficient Data Types:** Use appropriate data types to reduce memory usage and potentially speed up operations.
    * Convert object columns containing numerical data to numeric types (`pd.to_numeric`).
    * Use `category` type for low-cardinality string columns (`.astype('category')`).
    * Downcast numeric types (`pd.to_numeric` with `downcast`='integer'/'float') where appropriate (e.g., `int64` to `int32` or `uint16` if values fit).
* **Loading Data:**
    * When reading large CSVs (`pd.read_csv`), use the `usecols` parameter to load only necessary columns.
    * Specify `dtype` parameter during loading if known, to reduce memory usage and prevent incorrect type inference.
    * Consider using more efficient file formats like Parquet or Feather for intermediate storage.
* **Indexing:** Set meaningful indexes (`.set_index()`) if it benefits common lookup or joining operations. Understand the performance implications of different index types.
* **Missing Data:** Handle missing data (`NaN`) explicitly using methods like `.isna()`, `.fillna()`, `.dropna()`.
* **Memory Usage:** Monitor DataFrame memory usage with `.info(memory_usage='deep')`. Process large datasets in chunks (`chunksize` parameter in `read_csv`) if they don't fit in memory.

## Numerical Computing (NumPy)

* **Vectorization:** Leverage NumPy's vectorized operations for element-wise calculations on arrays. Avoid explicit Python loops over array elements.
* **Broadcasting:** Understand and utilize broadcasting to perform operations on arrays of different but compatible shapes without explicitly creating copies.
* **Built-in Functions:** Use NumPy's built-in universal functions (`ufuncs` like `np.sum`, `np.mean`, `np.exp`) and linear algebra functions, which are implemented in C/Fortran for high performance.
* **Memory Efficiency:**
    * Choose appropriate data types (`dtype`, e.g., `np.float32` vs. `np.float64`) to conserve memory.
    * Be mindful of creating intermediate arrays in complex calculations. Use in-place operations (`+=`, `*=`) where appropriate and safe, or structure calculations to minimize temporary arrays.
* **Indexing & Slicing:** Use efficient indexing and slicing techniques (including boolean indexing and integer array indexing) for data selection and manipulation. Understand the difference between views and copies.

## Machine Learning (Scikit-learn)

* **Pipelines:** Use Scikit-learn `Pipeline` objects to chain data preprocessing steps (e.g., imputation, scaling, encoding) and the final estimator (model). This prevents data leakage from the test set into the training set during steps like cross-validation and simplifies the workflow.
* **Data Splitting:** Always split data into training and testing sets (`train_test_split`) *before* applying transformations (like scaling or imputation based on training data) or training models. Use the `stratify` parameter for classification tasks with imbalanced classes.
* **Cross-Validation:** Use cross-validation techniques (`cross_val_score`, `GridSearchCV`, `RandomizedSearchCV`) for robust model evaluation and hyperparameter tuning, rather than relying on a single train-test split. Use stratified variants (e.g., `StratifiedKFold`) for classification.
* **Reproducibility:** Set the `random_state` parameter in algorithms involving randomness (e.g., `train_test_split`, tree-based models, clustering, dimensionality reduction, initializations) to ensure reproducible results.
* **Hyperparameter Tuning:** Use systematic hyperparameter tuning methods like `GridSearchCV` (exhaustive) or `RandomizedSearchCV` (random sampling, often more efficient) with cross-validation. Define appropriate scoring metrics relevant to the problem.
* **Feature Engineering:** Use Scikit-learn transformers (`StandardScaler`, `OneHotEncoder`, `SimpleImputer`, `PolynomialFeatures`, custom transformers) for feature engineering, ideally within a Pipeline.
* **Model Evaluation:** Choose appropriate evaluation metrics for the task (e.g., accuracy, precision, recall, F1-score, ROC AUC for classification; MAE, MSE, R² for regression). Evaluate models on the held-out test set only *after* final model selection and tuning.
* **Avoid Data Leakage:** Be extremely careful not to leak information from the test set (or future data) into the training process. This includes fitting transformers (`fit`/`fit_transform`) only on training data and then using `.transform()` on test data. Pipelines help manage this correctly.

## Performance

* **Profiling:** Profile code using tools like `cProfile`, `line_profiler`, or notebook magic commands (`%timeit`, `%prun`) to identify actual bottlenecks before optimizing.
* **Vectorization:** Prioritize Pandas/NumPy vectorized operations (see previous sections).
* **Avoid `.apply` for Math:** Pandas `.apply()` can be slow, especially for row-wise operations involving standard math. Try to find vectorized alternatives first.
* **Cython/Numba:** For critical performance bottlenecks in pure Python loops or calculations not easily vectorized, consider using Cython or Numba to compile sections of Python code for significant speedups.
* **Chunking:** Process large datasets that don't fit in memory by reading and processing them in chunks.
* **Parallel Processing:** Use libraries like `Dask` or `Joblib` (used internally by Scikit-learn with `n_jobs=-1`) for parallelizing computations across multiple CPU cores where appropriate.

## Testing & Validation

* **Follow Testing Rules:** Adhere to rules defined in `testing_rules.md`.
* **Test Data Transformations:** Write unit tests for data cleaning and feature engineering functions to ensure they behave as expected. Use sample DataFrames as input.
* **Test Model Logic (if custom):** Unit test any custom model logic or algorithms.
* **Validate Pipeline Outputs:** Check the schema, data types, and basic properties of data at different stages of processing pipelines.
* **Reproducibility:** Ensure analysis and model training scripts are reproducible (e.g., by fixing random seeds, versioning dependencies).
