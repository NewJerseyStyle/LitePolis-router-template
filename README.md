# LitePolis-router Template

This repository serves as a template for creating router modules for LitePolis. It provides a basic structure and example code to guide you through the process.

> :warning: Keep the prefix "litepolis-router-" and "litepolis_router_" in the name of package and directories to ensure the LitePolis package manager will be able to recognize it during deployment.

## Getting Started

1.  **Clone the Repository:** Start by cloning this repository to your local machine.

2.  **Rename the Package:** Update the package name in the following files:
    * **`setup.py`**: Change `name='litepolis-router-template'` to your desired package name (e.g., `litepolis-router-dashboard`). Also, update the `version`, `description`, `author`, and `url` fields accordingly.
    * **`tests/test_core.py`**: Update the import statement to reflect your new package name. For example, change `from litepolis_router_template import router, prefix` to `from litepolis_router_dashboard import router, prefix`.
    * Rename the folder `litepolis_router_template` to your new package name (e.g., `litepolis_router_dashboard`).

3.  **Implement API Logic:** Modify the `litepolis_router_template/core.py` file (or the renamed equivalent) to implement your API routes and logic using the provided FastAPI `router` instance. This includes defining your endpoints and any necessary database interactions. The `DEFAULT_CONFIG` dictionary in `core.py` allows for default configuration settings. Ensure you update the docstrings for your endpoints to accurately reflect their functionality, as these will be used to generate API documentation.

4.  **Testing:** The `tests/test_core.py` file contains example tests using Pytest and FastAPI's `TestClient`. Update these tests to cover your module's functionality. Ensure your tests correctly use `DEFAULT_CONFIG` where necessary and properly set up the FastAPI test application instance with your `router` for testing. Ensure the tests run successfully after making changes.

## Key Files and Modifications

* **`setup.py`**: This file contains metadata about your package. **Crucially**, you need to change the `name` field to your package's unique name. Also, update the `version`, `description`, `author`, and `url` fields as needed.

* **`litepolis_router_template/core.py`**: This file contains the core logic for your module, including the FastAPI `router` instance and the `DEFAULT_CONFIG` dictionary. The `DEFAULT_CONFIG` dictionary provides default configuration settings that will be registered with LitePolis. Replace the example endpoints on the `router` with your own API operations. **Important:** Update the docstrings for API documentation generation. Pay attention to the `ResponseMessage` model and adapt it if necessary to fit your data structures.

* **`tests/test_core.py`**: This file contains tests for your module. Update the tests to reflect your changes in `core.py`. Thorough testing is essential for ensuring the correctness of your module. Ensure your tests correctly set up the FastAPI test application using your `router` and utilize `DEFAULT_CONFIG` as needed.

## Important Considerations

* **API Documentation:** Well-documented code is crucial for maintainability and collaboration. Ensure your endpoints in `core.py` have clear and comprehensive docstrings. These docstrings will be used to generate API documentation for LitePolis. For best practices and detailed examples, refer to this helpful resource: [How to Document an API for Python FastAPI](https://medium.com/codex/how-to-document-an-api-for-python-fastapi-best-practices-for-maintainable-and-readable-code-a183a3f7f036)

* **Testing:** Write comprehensive tests to cover all aspects of your router module. This will help catch errors early and ensure the stability of your code.

* **Dependencies:** If your module requires external libraries, add them to the `install_requires` list in `setup.py`. Also list FastAPI-specific dependencies in the `litepolis_router_template/dependencies.py` file.

* **`DEFAULT_CONFIG` and Router:** The `DEFAULT_CONFIG` dictionary defined in `core.py` is crucial. Its contents will be registered with the LitePolis configuration system upon deployment. Ensure it contains appropriate default values for your service. The FastAPI `router` object defined in `core.py` is also essential, as LitePolis will use it to discover and expose your API endpoints.

## About `dependencies.py`

Contains definitions for FastAPI dependencies (using `fastapi.Depends`) needed by your API endpoints. Defining them here helps LitePolis manage and potentially override or inject dependencies during deployment.

## About `DEFAULT_CONFIG`

This dictionary, defined in `core.py`, holds default configuration values for your router module. These values will be registered with the LitePolis configuration system when the module is deployed. If modified configurations are provided during deployment, they will override these defaults. Settings can be fetched within your code (or other services) using the `get_config(<package-name>, <configuration-key>)` function provided by LitePolis infrastructure, which will return the currently active value.

## Recommended Pattern for Accessing Configuration

To ensure automated tests (Pytest) do not rely on live configuration sources, use the following pattern to fetch configuration values. This pattern checks for environment variables set by Pytest (`PYTEST_CURRENT_TEST` or `PYTEST_VERSION`) to determine the execution context.

```python
import os
# Assuming get_config is available for fetching live config
# from litepolis import get_config 

# Define default values suitable for testing environment
DEFAULT_CONFIG = {
    "database_url": "sqlite:///./test_default.db", 
    "some_api_key": "test_key_123"
    # Add other necessary default config values here
}

# Configuration Fetching Logic
db_url = None
some_key = None

# Check if running under Pytest
if ("PYTEST_CURRENT_TEST" not in os.environ and
    "PYTEST_VERSION" not in os.environ):
    # NOT running under Pytest: Fetch from live source
    print("Fetching configuration from live source...") # Optional debug msg
    # Replace with actual service name and key
    # db_url = get_config("your_service_name", "database_url") 
    # some_key = get_config("your_service_name", "some_api_key")
    # Example placeholder if get_config isn't immediately available:
    db_url = "live_db_url_placeholder" 
    some_key = "live_api_key_placeholder"
else:
    # Running under Pytest: Use default values
    print("Running under Pytest. Using default configuration.") # Optional debug msg
    db_url = DEFAULT_CONFIG["database_url"]
    some_key = DEFAULT_CONFIG["some_api_key"]

# Use the determined config values (db_url, some_key)
print(f"Using Database URL: {db_url}")
print(f"Using API Key: {some_key}")

```

**Guidance:**

* Apply this pattern for any configuration that differs between test and live environments.
* Ensure `DEFAULT_CONFIG` is defined with appropriate test values.
* Remember to `import os`.
* Use the actual `get_config` function provided by the LitePolis environment when not running tests.
