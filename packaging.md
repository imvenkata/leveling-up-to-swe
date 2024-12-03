# Python Packaging Guide

## Why Package Your Python Code?

Packaging your Python code offers a multitude of benefits:

- **Distribution**: Simplifies sharing your code. Other developers can easily install and use your code by running `pip install <your_package_name>`. This leverages the Python Packaging Index (PyPI), a repository of Python packages that can be accessed via pip.

- **Solving Import Issues**: Packages eliminate the need for complex hacks to modify the Python path variable for importing functions and classes across multiple files. This allows for cleaner, more readable code.

- **Reproducibility**: Ensures consistent code execution across different machines, environments, and time. Carefully managing dependencies helps avoid version conflicts.

---

## Understanding Key Terminology

- **Module**: A single unit of Python code, often corresponding to a single file (e.g., `my_module.py`) or a folder containing an `__init__.py` file.

- **Package**: A folder containing an `__init__.py` file and optionally more Python files. Packages can contain sub-packages (nested packages).

- **Distribution Package**: A compressed file (often a `.tar.gz` or a `.whl` file) containing all the necessary Python files, metadata, and potentially non-Python files for distribution.

---

## Evolution of Python Packaging: From `setup.py` to `pyproject.toml`

### 1. The Setup.py Era

- **Role**: Used as a CLI tool and configuration file to specify package metadata like name, version, author, and dependencies via the `setup()` function from `setuptools`.
- **Building**: A source distribution (`sdist`) could be created by running `python setup.py sdist`, generating a `.tar.gz` file in a `dist` folder.
- **Limitations**:
  - Lack of clear documentation for `setup()` arguments.
  - Manual listing of sub-packages.
  - Potential for malicious code execution during installation.

### 2. Enter Setup.cfg

- **Purpose**: Introduced to reduce configuration file overload.
- **Features**:
  - Moved static metadata from `setup.py` to `setup.cfg` (INI-style configuration).
  - `setup()` could read values from `setup.cfg`.
- **Benefits**:
  - Simplified `setup.py`.
  - Cleaner separation of configuration and code.

### 3. The Modern Approach: Pyproject.toml

- **Overview**:
  - A TOML-formatted file serving as a central hub for project configuration.
  - Absorbs much of the metadata previously in `setup.py` and `setup.cfg`.
- **Key Improvements**:
  - **PEP 518**: Introduced `pyproject.toml` for managing build dependencies, addressing issues with importing external libraries in `setup.py`.
  - **PEP 621**: Standardized storing project metadata in `pyproject.toml`, further reducing reliance on `setup.py` and `setup.cfg`.
- **Build Backends**: Allows for flexibility in build tools beyond `setuptools`, e.g., `hatch` and `poetry`.

---

## Managing Dependencies

### Types of Dependencies

1. **Required Dependencies**: Essential libraries for your package to function. Listed in the `dependencies` array in `pyproject.toml`.
2. **Build Dependencies**: Libraries needed to build the package itself, like the `wheel` package. Specified in the `requires` array in the `build-system` section of `pyproject.toml`.
3. **Optional Dependencies**: Provide additional functionality. Defined in `pyproject.toml` and installed using `pip install <package_name>[<optional_set_name>]`.

### Best Practices for Reproducibility

- **Document Dependencies**: Ideally in `pyproject.toml`.
- **Freeze Dependencies**: Use `pip freeze > requirements.txt` to capture exact versions of installed dependencies.
- **Minimize Dependencies**: Avoid reliance on unnecessary libraries.
- **Resolve Conflicts**: Analyze dependency graphs and use tools like `pipdeptree` to visualize relationships.
- **Evaluate Package Health**: Use resources like Snyk's Python Package Index to assess quality and security.

---

## Distribution Package Formats

1. **Source Distribution (sdist)**:
   - A `.tar.gz` file containing source code and a `setup.py` file.
   - Requires execution of `setup.py` on the user’s machine, which may pose security and compatibility risks.
   
2. **Wheel (.whl)**:
   - A binary distribution format containing pre-built code.
   - Improves security and installation speed by eliminating the need to execute `setup.py` on the user’s machine.

---

## Including Non-Python Files

By default, only Python files are included in packages. To include additional files:

- **MANIFEST.in**: Specifies files to include using commands like `include`, `recursive-include`, and `global-exclude`.
- **Legacy Setup.cfg**: The `[options.package_data]` section maps package names to file patterns.
- **Modern Pyproject.toml**: The `[tool.setuptools.packages.find]` section provides similar functionality with glob patterns.

**Remember**:
- Folders containing data files must also have an `__init__.py` file for inclusion.
- Avoid unnecessary bloat to keep package size manageable.

---

## Practical Tips and Tools

- **Editable Installs**: Use `pip install -e .` to install your package in "editable mode," allowing immediate reflection of code changes.
- **Templating Tools**: Tools like `cookiecutter` or `pyscaffold` can generate boilerplate code for new packages.
- **VS Code Extensions**: Extensions like "Better TOML" enhance syntax highlighting and improve `pyproject.toml` file support.




# Building a Sample Python Package

#### Project Structure

First, create the following directory structure for the package:

```
sample_package/
├── sample_package/
│   └── __init__.py
└── pyproject.toml
```

#### `pyproject.toml`

Next, populate the `pyproject.toml` file with the following content:

```toml
[project]
name = "sample-package"
version = "0.1.0"
description = "A sample Python package."
readme = "README.md"
requires-python = ">=3.7"
license = {text = "MIT"}
authors = [
    { name = "Your Name", email = "your.email@example.com" },
]
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]
dependencies = [
    "requests"
]

[project.optional-dependencies]
dev = [
    "pytest"
]

[build-system]
requires = ["setuptools>=61"]
build-backend = "setuptools.build_meta"
```

- The `project` section, as defined in **PEP 621**, contains metadata about the package.
- The `name` field specifies the name of the package, which users will use to install the package with the command `pip install sample-package`. Note, however, that this name does not have to be the same as the name of the directory containing your package.
- The `version` field specifies the version of the package. The version name can be any string, but it is best practice to use [semantic versioning](https://semver.org/). The version that you specify in the `pyproject.toml` file will determine what version of the package `pip` downloads if a user runs a command like `pip install sample-package==0.1.0`.
- The `description` field provides a short description of the package, which will appear on PyPI. PyPI (the Python Packaging Index) is "a glorified file server" that stores Python packages, which users can then install by running a command like `pip install sample-package`.
- The `readme` field specifies the path to the README file for the package. The README file contains a long description of the package and often provides instructions about how to install and use the package.
- The `requires-python` field specifies the minimum version of Python required to run the package.
- The `license` field specifies the license of the package. It is important to specify the license for your package, as it will inform users about how they can use the package. The sources provide an example of how to specify the MIT license. 
- The `authors` field specifies the authors of the package.
- The `classifiers` field provides a list of classifiers that describe the package, such as the programming language, license, operating system, and intended audience. Classifiers help users to find packages that meet their needs.
- The `dependencies` field specifies the runtime dependencies of the package. These are the Python packages that must be installed in order to use the package. When a user runs a command like `pip install sample-package`, `pip` will install the package's dependencies. Note that the sources do not recommend pinning dependencies to a specific version, as doing so increases the likelihood of dependency conflicts. A dependency conflict occurs when two packages require different versions of the same package.
- The `optional-dependencies` field allows you to define sets of optional dependencies, which users can install by running commands like `pip install sample-package[dev]`. Optional dependencies can be used for development tools, or they can be used for optional features of the package.
- The `build-system` section specifies the build system to use for building the package.
- The `requires` field in the `build-system` section specifies the build dependencies of the package, which are the Python packages that are required to build the package. When a user runs a command like `pip install sample-package`, these dependencies are installed, but they are then immediately discarded.
- The `build-backend` field specifies the build backend to use for building the package. Setuptools is the default build backend.

#### Package Code

Finally, add the following code to the `sample_package/__init__.py` file:

```python
import requests


def get_weather(city):
    """Gets the current weather for a city using the OpenWeatherMap API.

    Args:
        city: The name of the city.

    Returns:
        A dictionary containing the weather data, or None if an error occurs.
    """
    api_key = "YOUR_API_KEY"
    base_url = "http://api.openweathermap.org/data/2.5/weather?"
    url = f"{base_url}appid={api_key}&q={city}"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()
    else:
        return None
```

#### Building the Package

To build the package, run the following command in the root directory of the project:

```bash
python -m build
```

This will create a source distribution and a wheel in the `dist` directory.

#### Installing the Package

To install the package, run the following command:

```bash
pip install dist/sample_package-0.1.0-py3-none-any.whl
```

(Replace `sample_package-0.1.0-py3-none-any.whl` with the actual name of the wheel file.)

#### Testing the Package

To test the package, run the following code in a Python interpreter:

```python
>>> from sample_package import get_weather
>>> weather = get_weather("London")
>>> print(weather['main']['temp'])
284.93
```

You'll first need to obtain an API key from OpenWeatherMap. Instructions for doing so can be found [here](https://openweathermap.org/appid).

This is a basic example of how to create a Python package using a modern approach. There are many other things that you can do with Python packages, such as adding tests, documentation, and command-line interfaces. For more information, refer to the Python Packaging User Guide.

#### Additional Notes

- This response does not cover all the nuances of building a package with compiled code.
- While optional dependencies can be used to create a plugin-based architecture for a package, the sources only provide a simple example of how to use optional dependencies to enable colorized output.

Please let me know if you have any other questions


