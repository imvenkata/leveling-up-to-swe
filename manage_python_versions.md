# Semantic Versioning and Managing Python Versions with Pyenv

Semantic versioning and managing multiple Python versions using Pyenv.

---

## Understanding Semantic Versioning

**Semantic Versioning (SemVer)** uses a three-part version number: `MAJOR.MINOR.PATCH`. Each part follows specific rules:

1. **MAJOR version**: Incremented for incompatible API changes.
2. **MINOR version**: Incremented for backward-compatible new functionality.
3. **PATCH version**: Incremented for backward-compatible bug fixes.

### Examples of Semantic Versioning

Consider these examples:

1. Initial release: `1.0.0`
2. Patch update (bug fix): `1.0.1`
3. Minor update (new feature): `1.1.0`
4. Major update (breaking change): `2.0.0`

#### Example: Python Library "DataProcessor"

```python
# DataProcessor version 1.0.0
def process_data(data):
    return data.upper()

# DataProcessor version 1.1.0 (Minor update: new feature)
def process_data(data, lowercase=False):
    return data.lower() if lowercase else data.upper()

# DataProcessor version 2.0.0 (Major update: breaking change)
def process_data(data, case='upper'):
    if case not in ['upper', 'lower']:
        raise ValueError("Case must be 'upper' or 'lower'")
    return data.upper() if case == 'upper' else data.lower()
```

- **Version 1.1.0** introduces a new feature without breaking existing functionality.
- **Version 2.0.0** makes a breaking change to the function signature.

---

## Managing Multiple Python Versions with Pyenv

Pyenv is a powerful tool for managing multiple Python versions. Here’s how to use it effectively.

### Installing Pyenv

Install Pyenv with the following command:

```bash
curl https://pyenv.run | bash
```

Add these lines to your shell configuration file (e.g., `.bashrc` or `.zshrc`):

```bash
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

### Installing Python Versions

Install a specific Python version:

```bash
pyenv install 3.9.6
```

You can install multiple versions:

```bash
pyenv install 3.8.10
pyenv install 3.10.5
```

### Listing Installed Versions

List all installed Python versions:

```bash
pyenv versions
```

### Setting Global and Local Python Versions

Set a global Python version:

```bash
pyenv global 3.9.6
```

Set a local Python version for a specific project:

```bash
cd my_project
pyenv local 3.8.10
```

### Creating Virtual Environments

Create a virtual environment using a specific Python version:

```bash
pyenv virtualenv 3.9.6 myproject-env
```

Activate the virtual environment:

```bash
pyenv activate myproject-env
```

Deactivate it when done:

```bash
pyenv deactivate
```

---

## Practical Example: Managing a Project with Multiple Python Versions

Let’s demonstrate managing a project requiring different Python versions.

### Steps:

1. **Create a new project directory**:

    ```bash
    mkdir semantic_version_demo
    cd semantic_version_demo
    ```

2. **Set up two Python versions for the project**:

    ```bash
    pyenv local 3.8.10 3.9.6
    ```

    This creates a `.python-version` file in your project directory.

3. **Create virtual environments for each version**:

    ```bash
    pyenv virtualenv 3.8.10 semver-demo-3.8
    pyenv virtualenv 3.9.6 semver-demo-3.9
    ```

4. **Create two Python files for demonstration**:

    ```python
    # file: demo_38.py
    print(f"Python version: {__import__('sys').version}")
    # Use a feature available in Python 3.8
    print(f"{(x := 10)} is X")
    ```

    ```python
    # file: demo_39.py
    print(f"Python version: {__import__('sys').version}")
    # Use a feature available in Python 3.9
    print({1, 2, 3}.union({3, 4, 5}))
    ```

5. **Run each file with its corresponding Python version**:

    ```bash
    pyenv activate semver-demo-3.8
    python demo_38.py

    pyenv activate semver-demo-3.9
    python demo_39.py
    ```

---
