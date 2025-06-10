# GENERAL INSTRUCTIONS
- Use Python 3.11 or above.
- When selecting modules to be used, please summarize the options and then ask me.
  - Prefer packages that are officially supported by that technology, or are well established with many contributors.
- Use dataclasses, and accurate typing.
- Simple solutions are better than more complex.
- Functionality should be divided up into classes.
- Classes and Functions should have limited scope, consider testing; if you need to mock multiple integrations that is a clear sign that the class or function is serving too many purposes.
- Build things as a reusable package, not just a script, or CLI tool.
- Always include a .gitignore file
- Always include a requirements.txt file
- Always ask me to run ruff format locally for code formatting/style issues or concerns
- Always use the logging module, and always leverage a helper function to make sure the entire project has consistent and centrally managed logging configuration.
- Prefer configuration-based approaches over hardcoded values whenever appropriate.
- Use type hints consistently throughout the codebase.
- Document public API functions and methods with docstrings using Google-style format.
- Never log or print anything that contains sensitive information, such as passwords or API keys.
- Use environment variables for sensitive information and configuration settings.
- Always use uv for dependency and environment management (instead of pip, python -m venv, or poetry).
- Always use ruff for code formatting, linting, and static analysis (instead of black, isort, flake8, or mypy).

## PROJECT STRUCTURE
- All names in angle brackets (e.g., <main_package_name>, <module_name>, <file_name>, <folder_name>) are placeholders.  
- Never use a placeholder name as the actual name in generated code or files.  
- Always prompt me for the actual name before generating code or files. There can be many different <file_name> or <module_name> files, so please ask me for the name of the file or module that you are working on.  
- This applies to all levels of the project structure, including submodules and nested folders / files.

Legend:
```
# <main_package_name>: The root Python package for your application
# <module_name>: A submodule or feature-specific package
# <file_name>: Any Python or config file
# <folder_name>: Any folder, e.g., for environment or entity-specific configs
```

```
<main_package_name>/
├── requirements.txt                # Python dependencies
├── README.md                       # Project documentation
├── .gitignore                      # Git ignore file
├── configs/                        # Configuration files
│   ├── <main_package_name>.yaml    # Default application configuration
│   └── <folder_name>/              # Environment/entity-specific configs
│       └── <file_name>.yaml
├── src/                            # Source code
│   ├── __init__.py
│   ├── core.py                 # Core functionality
│   ├── exceptions.py           # Custom exceptions
│   ├── config.py               # Configuration handling
│   ├── logging.py              # Logging configuration
│   └── <module_name>/          # Module for specific functionality (there could be multiple of these)
│   │   ├── __init__.py
│   │   └── <file_name>.py
├── tests/                          # Test suite
│   ├── __init__.py
│   ├── conftest.py                 # Test fixtures
│   ├── unit/                       # Unit tests
│   │   ├── __init__.py
│   │   └── test_<file_name>.py     # Test files related to our files/modules
│   └── resources/                  # Test resources
│       └── test_data.yaml          # Test data
```

## PERFORMANCE CONSIDERATIONS
- Assume the priority is clarity and readability, rather than complexity or searching for performance gains.
- Consider scalability when designing data structures and algorithms.
- Add performance optimizations only when necessary and with clear justification.

## ERROR HANDLING
- Use custom exception classes for domain-specific errors.
- Include appropriate error messages that are helpful for debugging.
- Validate input parameters in public methods and functions.
- Prefer explicit error handling over generic try/except blocks.
- Log errors with appropriate context and severity levels.

## CICD  
- Assume everything is github actions, unless instructed otherwise.
- Follow best practices for CI workflows: building, testing, linting, and packaging.

## TESTING  
- Always use pytest
- Always default to writing unit tests, do not create integration tests unless specifically asked.
- We need high test coverage on the most critical functions, classes, and processes.
- If you are working on tests, and you want to modify the source code to fit what is being tested, please summarize and ask if you should continue. There are very few scenarios where we should be modifying our code to fit the test.

## INTERACTION GUIDANCE
Ask for my input specifically in the following scenarios:
- When deciding between multiple design approaches (present options with pros/cons)
- Before adding new third-party dependencies not already in requirements.txt
- When creating a new module or significant class structure
- When suggesting architectural changes to existing code
- When naming key components (modules, classes, or important functions)
- When suggesting refactoring that spans multiple files
- If the requirements seem ambiguous or contradictory
- Before implementing a solution that deviates from the existing patterns in the codebase
- If you encouter something that might need backwards compatibility support, please ask for confirmation to make the breaking change without backwards compaitbility.

Do NOT ask for input for:
- Minor implementation details
- Variable or function names for non-critical components
- Adding standard testing libraries or fixtures
- Fixing obvious bugs or typos
- Implementing straightforward features with clear requirements

If there is any uncertainty about whether to prompt, always err on the side of asking for my input.

## DOCUMENTATION
- Include docstrings for all modules, classes, and functions using Google-style format.
- Maintain a clear and up-to-date README.md with installation and usage instructions.
- Add inline comments for complex logic, but prefer self-documenting code.
- Document configuration options and environment variables.
- Include examples for API usage when appropriate.

## PROJECT CONFIGURATION

## CONFIGURATION MANAGEMENT
- Use a pyproject.toml file for project metadata and dependencies.
- Use a .pre-commit-config.yaml file for pre-commit hooks.
- Manage dependencies with uv settings within the pyproject.toml file.
- Store configuration in YAML (.yml except for .pre-commmit-config.yaml) files under the configs/ directory.
- Support environment-specific configuration overrides.
- Use environment variables for secrets and deployment-specific values.
- Configuration should be validated at startup.
- Provide reasonable defaults for all configuration options.
- Include examples of configuration files in the repository.
- Prefer setting configuration options to revolve mypy issues rather than changing code.

## CODE QUALITY
- Follow PEP 8 style guidelines (with ruff formatter).
- Use ruff for formatting, linting, and static analysis.
- Keep functions and methods concise (generally under 50 lines).
- Use meaningful variable and function names.
- Avoid code duplication; extract reusable functions or classes.
- Implement separation of concerns through appropriate module organization.

## SECURITY
- Don't hardcode sensitive information (credentials, API keys, etc.).
- Sanitize user inputs and validate against expected formats.
- Use secure defaults for any security-related functionality.
- Implement proper access controls for sensitive operations.
- Be mindful of potential SQL injection vulnerabilities in database queries.
- Consider data privacy implications when logging and storing information.
