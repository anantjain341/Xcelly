# `my_agent` Module

## Purpose
The `my_agent` directory contains the core LangGraph-based AI system (the "Brain"). It orchestrates multi-agent data analysis, manages state transition between specialized agents (nodes), and coordinates tool execution via subgraphs.

## Folder Structure
- **`/nodes`**: Contains the individual LangGraph nodes (Router, Data Inspector, Supervisor, Planner, Coding Agent, etc.) that represent distinct AI personas and tasks.
- **`/tools`**: Contains the LangChain-compatible tools (`python_repl_tool`, `bash_tool`, `think_tool`) used by the coding agent to interact with the environment.
- **`/helpers`**: Utilities for interacting with the isolated Sandbox server (the "Muscle"), managing files, and executing Python code securely.
- **`/models`**: Defines the shared state models (e.g., `ExcelAnalysisState`) passed between graph nodes.
- **`/graphs`**: Subgraphs that encapsulate complex sub-processes, like the iterative code-execution-and-reflection loop (`coding_subgraph.py`).
- **`/core`**: Core utilities such as logging configuration, execution variables, and secrets management.

## Key Files
- `agent.py`: The primary entry point that builds and compiles the main LangGraph state machine. It defines the edges (routing logic) connecting all the nodes in `/nodes`.

## Architecture & Flow
1. **User Input** flows into `agent.py`.
2. The state is updated and passed to the **Router**, which conditionally routes to chat, analysis, or followup paths.
3. If analysis is needed, the **Data Inspector** retrieves data context, the **Supervisor** decides on strategy, and the **Planning Agent** writes an execution plan.
4. The **Coding Subgraph** executes the plan iteratively using tools defined in `/tools` and executed remotely via `/helpers/sandbox_client.py`.
5. The state aggregates artifacts and final answers, which are returned to the user via FastAPI.

## Extending the Module
To add a new capability or AI persona:
1. Define a new node function in `my_agent/nodes/`.
2. (Optional) Define new state variables or transition logic in `my_agent/models/state.py`.
3. Add the node to the graph in `my_agent/agent.py` using `workflow.add_node()`.
4. Define the edges (how and when to route to your new node) using `workflow.add_edge()` or `workflow.add_conditional_edges()`.
