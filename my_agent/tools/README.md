# Tools (`my_agent/tools`)

## Purpose
This directory contains the LangChain tools exposed to the coding agent. Tools are the actionable functions that give the LLM the ability to affect the outside world—in this case, executing Python code, running shell commands, and pausing to reflect.

## Key Files
- **`tools.py`**: Contains the tool definitions utilizing LangChain's `@tool` decorator.

## Included Tools
- **`python_repl_tool`**: Evaluates Python code. Crucially, this does *not* execute code locally. It takes the code string and sends an HTTP POST request to the Sandbox Server via `sandbox_client.py`. This ensures isolated, state-persistent, and safe execution.
- **`bash_tool`**: Executes shell commands within the Sandbox environment. Primarily intended for installing dynamically required Python packages (e.g., `pip install scikit-learn`).
- **`think_tool`**: A metacognition tool. Forces the AI to write down its reflection or analysis of an error before attempting to write code again. This reduces infinite loops and rapidly improves autonomous debugging capabilities.

## How to Extend
To add a new tool (e.g., `search_web_tool`):
1. Define a new function in `tools.py` using the `@tool` decorator.
2. Provide a rich, descriptive docstring. The LLM relies almost entirely on your docstring to understand *when* and *how* to use the tool.
3. Import and append the new tool to the `tools` list in `my_agent/nodes/coding_agent.py` so the LLM knows it exists (`llm.bind_tools(tools)`).
