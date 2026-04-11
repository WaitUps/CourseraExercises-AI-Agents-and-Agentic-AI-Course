This notebook details the construction and operation of a simple AI agent, focusing on its core **agent loop**. This loop enables the agent to iteratively interact with its environment, process information, make decisions, and execute actions.

---

# Part 2: The Agent Loop Architecture Overview
## Execution Environment & Model Details
- **Model:** `gemini-flash-lite-latest`
- **Configuration:** `{"max_output_tokens": 3072}`
- **Core Library:** `google-genai` (Gemini API)
- **Architecture:** Iterative agent loop with structured memory, tool definition, and execution.

---

## The Agent Loop
At the heart of our AI agent is a six-step iterative loop:

1.  **Construct Prompt**: The agent's rules (system message) and its `memory` (conversation history) are combined to form the prompt for the Large Language Model (LLM). This provides the LLM with all necessary context.

2.  **Generate Response**: The combined prompt is sent to the `gemini-flash-lite-latest` model via the `generate_response` function. The LLM generates a response, typically containing a structured action.

3.  **Parse Response**: The LLM's response is parsed to extract the intended action and its arguments. The agent expects a JSON format within a markdown code block (e.g., ````action { "tool_name": "...", "args": {...} }````). If parsing fails, an `error` action is triggered.

4.  **Execute Action**: Based on the parsed `tool_name` and `args`, the corresponding tool function (e.g., `list_files()`, `read_file(file_name)`) is executed. This is where the agent interacts with its environment.

5.  **Update Memory**: The agent's `memory` is updated with both the LLM's raw response (as an 'assistant' message) and the result of the executed action (as a 'user' message). This ensures continuous context for future iterations.

6.  **Decide Whether to Continue**: The loop evaluates whether to continue based on the executed action. If the action is `terminate`, or if `max_iterations` are reached, the loop ends; otherwise, it proceeds to the next iteration.

---

### Agent Rules (Code Snippet)
```python
agent_rules = [{
    "role": "system",
    "content": """
You are an AI agent that can perform tasks by using available tools.
When you perform an action, its result will be provided to you in a subsequent 'user' turn, formatted as a JSON string. You should interpret these 'user' turns as tool outputs, not new user requests, unless explicitly stated by the user.

Available tools:
- list_files() -> List[str]: List all files in the current directory.
- read_file(file_name: str) -> str: Read the content of a file.
- terminate(message: str): End the agent loop and print a summary to the user.

If a user asks about files, list them using `list_files()` before attempting to read. After listing files, if the user has not specified a file to read, you should inform the user of the available files and ask them which one they'd like to read. If you have already listed the files and there are no further explicit instructions from the user on what to do next, you should use the `terminate` tool with an appropriate message summarizing the available files.

Every response MUST have an action.
Respond in this format:

```action
{
    "tool_name": "insert tool_name",
    "args": {...fill in any required arguments here...}
}
"""
}]
```

---

## Key Components and Concepts

*   **Agent Rules**: A system message defining the agent's persona, available tools, and expected response format. This guides the LLM's behavior.
*   **Memory**: A chronological record of all interactions (user input, agent responses, tool outputs). It's crucial for maintaining context and enabling adaptive decision-making.
*   **Tools**: Predefined functions (`list_files`, `read_file`, `terminate`) that the agent can invoke to perform specific tasks. The LLM orchestrates their use.
*   **`extract_markdown_block`**: A dynamically generated helper function used to reliably parse structured action commands from the LLM's text output.

This architecture allows the agent to dynamically adapt its behavior, reason over past events, and execute tasks by orchestrating a set of predefined tools, ensuring a robust and interactive AI experience.


---

## Agent Capabilities: Observations & Next Steps

This section marks the conclusion of the provided starter code for Module 1 of the course. Here are some key observations and areas for future development:

***

### Current Status and Missing Components:
*   **`extract_markdown_block` Function:** This essential helper function, which is critical for parsing the agent's responses, was not part of the original starter code. It was dynamically generated and added to the notebook by Colab Gemini during our interaction.
*   **Core Tool Functions (`list_files`, `read_file`):** The actual implementations for the `list_files` and `read_file` tool functions are currently missing. These are fundamental for the agent to perform real file operations and interact with its environment as intended.
*   **User Communication Tool:** There is currently no dedicated tool for the agent to communicate directly with the user for input or to provide detailed status updates beyond the simple `terminate` message. Implementing such a tool will be crucial for a more interactive and user-friendly agent.

***

### Future Improvements and Tasks:
To enhance the agent's functionality and robustness, the following improvements are planned. These missing parts and improvements will likely be provided in upcoming modules of the course:
*   **Implement Tool Functions:** Develop and integrate the implementations for `list_files` and `read_file` to enable the agent's file system interaction capabilities.
*   **`generate_response` Performance Enhancement:**
    *   Introduce a `log` parameter (defaulting to `False`) to the `generate_response` function, allowing explicit control over logging verbosity.
    *   Explore and apply a decorator-based approach for logging within `generate_response` to improve code modularity, readability, maintainability, and performance.
