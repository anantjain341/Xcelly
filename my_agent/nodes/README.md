# Agent Nodes (`my_agent/nodes`)

## Purpose
This directory houses the individual LangGraph node definitions. In a LangGraph workflow, each node represents a specific processing step or AI agent that takes the current global state, performs an action (often calling an LLM), and mutates the state for the next step.

## Key Components
- **`router.py`**: The traffic controller. Uses a fast LLM call to classify the user's intent as general chat, a request for new analysis, or a follow-up to previous analysis.
- **`data_inspector.py`**: The data archaeologist. Analyzes the uploaded `.csv` or `.xlsx` file using pandas to extract schemas, column types, missing values, and high-level statistics without loading the entire dataset into context.
- **`supervisor.py`**: Evaluates the user query against the existing conversation and data context to decide whether a new plan/code execution is required or if the question can be answered from existing context.
- **`planning.py`**: The strategist. Given the data context and the user query, it outputs a step-by-step markdown plan on how the coding agent should write Python code to analyze the data.
- **`coding_agent.py`**: The executor. An agent equipped with tools that iteratively writes Pandas/Python code. Runs within a subgraph (`my_agent/graphs/coding_subgraph.py`) to loop through code generation, execution, and reflection.
- **`followup_answer.py`**: Handles queries that can be answered strictly from the accumulated context (avoiding expensive new code executions).
- **`chat.py` / `chatbot.py`**: Handle generic conversational interactions unrelated to data files.

## Component Interaction
Nodes never call each other directly. They are registered in `my_agent/agent.py`. Each node function accepts the `ExcelAnalysisState` dictionary, performs its logic, and returns a dictionary with the specific keys it wishes to update (e.g., `{"messages": [new_message]}`).

## How to Extend
To add a new node (e.g., a specific "Charting Agent"):
1. Create a new file (e.g., `charting.py`).
2. Write an asynchronous function matching the signature: `async def charting_node(state: ExcelAnalysisState) -> dict:`.
3. Implement the LLM call or procedural logic.
4. Return the state dictionary updates.
5. Register it in `my_agent/agent.py`.
