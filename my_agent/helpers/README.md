# Helpers (`my_agent/helpers`)

## Purpose
The `helpers` directory contains all the foundational logic connecting the main LangGraph application (the Brain) to the isolated code execution environment (the Sandbox / the Muscle), as well as general utilities.

## Core Problem Solved
Executing AI-generated Python code directly inside the main web server process is inherently dangerous (crashing the main process, infinite loops) and slow if doing process spawns (since large Pandas DataFrames would need to be reloaded on every run). The modules here facilitate a Microservice Sandbox Architecture, transmitting code to a persistent HTTP server running an interactive Python session.

## Key Files
- **`sandbox_server.py`**: An independent FastAPI application that runs on port `8765`. It maintains persistent Python REPL sessions (managing local variables and DataFrames in memory) and captures `stdout` and images (like Matplotlib charts).
- **`sandbox_client.py`**: The HTTP client used by `tools.py` in the main app. It sends code payloads to `sandbox_server.py` and parses the stdout, errors, and generated artifacts (images/tables) returned.
- **`sandbox.py`**: Utilities for creating and tearing down the physical virtual environment (the `.sandbox/venv`) where the isolated code runs.
- **`file_utils.py`**: Helpers for interacting with files safely, reading CSV/Excel headers, etc.
- **`utils.py`**: General purpose project utilities.

## Component Interaction
1. The Coding Agent (`nodes/coding_agent.py`) decides to write Python code.
2. It invokes `python_repl_tool` in `tools/tools.py`.
3. `tools.py` passes the code string to `sandbox_client.py`.
4. `sandbox_client.py` makes an HTTP request to `sandbox_server.py` (which must be running concurrently in a separate process).
5. `sandbox_server.py` runs the code in its persistent memory space, saves any generated plots to disk, and returns the console output back down the chain.
