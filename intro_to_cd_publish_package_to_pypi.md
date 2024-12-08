# Intro to CD: Publishing Python Packages to PyPI

## Understanding the Context

### Continuous Delivery and DevOps

Continuous Delivery is a DevOps practice that focuses on making integrated code changes available to users on an ongoing basis. It works hand-in-hand with Continuous Integration, which emphasizes frequent merging of code changes. DevOps encompasses a set of tools and practices designed to help developers deliver software efficiently, including trunk-based development, continuous integration, and continuous delivery.

### Agile vs. Waterfall

Agile Project Management prioritizes incremental product development, rapid feedback, and frequent delivery of value to the customer. This approach contrasts with the Waterfall Methodology, which follows a linear process with distinct phases.

#### Benefits of Agile and Continuous Delivery

- Faster validation of ideas through quick deployment of prototypes or MVPs
- Early course correction based on user feedback
- Increased agility and competitiveness in responding to market changes

### Implementing Agile Principles

While Scrum is a popular Agile framework, it's important to remember that Agile is a philosophy, not a rigid set of rules. Teams can adapt Agile principles to their specific needs.

## Publishing to PyPI

### Understanding PyPI

PyPI (Python Package Index) is a repository for hosting Python packages, making them available for installation via `pip`. A key feature of PyPI is its immutability - once a specific version of a package is uploaded, it cannot be replaced.

### Essential Tools

1. **Twine**: The recommended CLI tool for uploading packages to PyPI.
   ```bash
   pip install twine
   ```

2. **Build CLI**: Used for generating source distributions and wheel files.
   ```bash
   pip install build
   ```

### Publishing Steps

#### Step 1: Create Accounts and API Tokens

- Set up accounts on Test PyPI (https://test.pypi.org/) and production PyPI (https://pypi.org/).
- Generate API tokens for each account in the account settings.
- Store these tokens securely (e.g., in a password manager or as environment variables).

#### Step 2: Build Your Package

```bash
python -m build --sdist --wheel .
```

#### Step 3: Upload to Test PyPI

```bash
twine upload --repository testpypi dist/*
```

When prompted, use `__token__` as the username and paste your Test PyPI API token as the password.

#### Step 4: Upload to Production PyPI

After successful testing, repeat the upload process with the production PyPI:

```bash
twine upload --repository pypi dist/*
```

## Automating the Publishing Process

### Task Runners

Task runners help streamline common development tasks. Popular options include:

- **Makefile**
- **Justfile**
- **PyInvoke**
- **Bash scripts**

## Pros and Cons of Different Task Runners

### Makefile

**Pros:**

- **Portability:** Widely available and pre-installed on many systems.
- **Industry Standard:** Familiar to many developers.
- **Dependency Graph Management:** Efficiently manages complex build processes.
- **Autocompletion:** Good support in many editors.

**Cons:**

- **Complexity and Quirks:** Syntax can be difficult to learn and error-prone.
- **Limited Scripting Capabilities:** Primarily uses `sh`, making complex logic challenging.
- **Debugging Challenges:** Combination of Bash and Make syntax can be difficult to debug.

### Justfile

**Pros:**

- **Simplified Syntax:** More user-friendly than Makefile.
- **Multiple Shells and Languages:** Supports different shells and even Python code.
- **Dependency Graph Management:** Handles task dependencies well.

**Cons:**

- **Portability:** May require separate installation, reducing portability compared to Makefile.

### PyInvoke

**Pros:**

- **Python-Based:** Easy to understand and extend for Python developers.
- **Flexibility:** Allows for powerful scripting and complex logic.
- **Arguments and Default Values:** Supports passing arguments to tasks with default values.

**Cons:**

- **Portability:** Needs separate installation, impacting portability.
- **Limited Autocompletion:** May not have robust autocompletion support.
- **Additional Tooling:** Adds another tool for developers to learn.

### Bash Scripts (e.g., run.sh)

**Pros:**

- **Portability:** Bash is widely available on most systems.
- **Familiarity:** Many developers are comfortable with Bash.
- **Flexibility:** Powerful scripting environment for complex processes.
- **Enhanced by Tools:** Tools like GitHub Copilot can assist in writing Bash scripts.

**Cons:**

- **Bash Quirks:** Has its own set of quirks and potential pitfalls.
- **Lack of Dependency Management:** No built-in mechanisms for managing task dependencies.

## Key Takeaways

- **Portability:** Consider using Makefile or Bash scripts for better portability across systems.
- **Complexity of the Build Process:** Simple projects may benefit from Bash scripts or Makefiles, while more complex processes might require Justfile or PyInvoke.
- **Team Familiarity:** Choose a task runner that aligns with your team's experience and preferences.
- **Security:** Take precautions when using task runners for deployments, avoiding storage of plain text secrets in scripts or Git repositories.

When selecting a task runner, carefully evaluate these pros and cons to make an informed decision that best suits your project needs and workflow.

## Creating a Task Runner Script

Here's an example of a Bash script (`run.sh`) that automates various tasks:

```bash
#!/bin/bash
set -e # Stop execution on error

DIR="$( cd "$( dirname "${BASH_SOURCE}" )" &> /dev/null && pwd )"

function upgrade_pip {
    python -m pip install --upgrade pip
}

function install {
    upgrade_pip
    python -m pip install -e .[test,release] 
}

function build {
    python -m build --sdist --wheel "$DIR/"
}

function lint {
    pre-commit run --all-files 
}

function publish:test {
    twine upload --repository testpypi dist/* 
}

function publish:prod {
    twine upload --repository pypi dist/*
}

function clean {
    # Add your clean logic here
}

function release:test {
    lint
    clean
    build 
    publish:test 
}

function release:prod {
    release:test  
    publish:prod 
}

# Run the specified task
time "$@"
```

To use this script:

1. Make it executable: `chmod +x run.sh`
2. Run a specific task: `./run.sh task_name` (e.g., `./run.sh install`)

## Best Practices and Additional Considerations

1. **Scripted Deployments**: Automate deployments to ensure consistency and reduce errors.
2. **Use CI/CD Tools**: Leverage tools like GitHub Actions for deployments instead of deploying from local machines.
3. **Implement Deployment Gates**: Consider adding approval processes or automated tests before production deployments.
4. **Comprehensive Testing**: Implement thorough tests to validate your code before publishing.
5. **Version Pinning**: Encourage users to pin versions only when necessary to ensure they receive critical updates.
6. **Unique Package Names**: Choose a globally unique name for your package, checking both Test PyPI and production PyPI for availability.

