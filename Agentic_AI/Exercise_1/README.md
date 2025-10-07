# Agentic AI & the LangChain + LangGraph Research Agent

## What is Agentic AI?

**Agentic AI** refers to a class of intelligent systems (often built with large language models, or LLMs) that go beyond simple prompt-response behavior and instead act more like autonomous agents. These systems can:

- **Take initiative / Autonomy** — plan multi-step tasks without needing explicit instruction at every step  
- **Use tools / APIs** — call external functions, fetch data, run searches, interact with other systems  
- **Maintain memory / Context** — store short-term or long-term state to carry information across steps  
- **Reason and loop** — break down tasks, evaluate intermediate results, choose what to do next, and possibly backtrack or branch  

Thus, rather than being reactive (answering a single prompt), agentic AI tries to continuously plan, act, and adapt until achieving a goal.

Some real-world examples include:

- Research assistants that explore sources, summarize, and cite  
- Coding copilots that plan, write, and debug code  
- Automated email agents that read, respond, and trigger further actions  

## Capabilities of Agentic AI

Because of their architecture, agentic AI systems can:

1. **Decompose Complex Tasks**  
   Break a large goal into sub-tasks, execute them sequentially or conditionally.

2. **Dynamic Decision Making**  
   Based on intermediate outputs or external feedback, decide which tool or action to take next.

3. **Memory / State Tracking**  
   Remember past interactions, results, or user preferences to maintain continuity across steps.

4. **Tool Integration & Action Execution**  
   Interact with APIs, perform searches, run computations, call functions, or manipulate files.

5. **Workflow Orchestration / Branching**  
   Handle workflows that aren’t strictly linear — support loops, conditional branching, parallel agents, etc.

6. **Coordination & Multiagent Interaction**  
   In more advanced setups, multiple agents or modules can cooperate, passing tasks or delegating.

7. **Autonomous Adaptation**  
   If one approach fails, adapt strategy (e.g., try alternate tool or re-plan) until success or graceful exit.

In short, agentic AI is a leap from generating single responses to orchestrating sequences of thought + action toward a goal.

---

## The Project: Multistep Research Agent (LangChain + LangGraph)

The article demonstrates how to build a simple **research assistant agent** using **LangChain** and **LangGraph**. Below is a summary of its components and flow.

### Key Technologies

- **LangChain** — A framework to build LLM-powered applications, defining chains, agents, memory, and tool interfaces.  
- **LangGraph** — A graph (state + workflow) engine layered on LangChain. It allows you to define nodes, edges, branching logic, and persistent state across steps.  
- **SerpAPI** — A web search API to fetch search results (used as the agent’s external tool).  
- **Ollama / LLaMA 2 (via langchain_ollama)** — A local model backend that enables you to run the language model without depending on an external cloud API.

### Agent Design & Workflow

The research agent is built as follows:

1. **State Schema Definition**  
   A `TypedDict` (named `ResearchState`) defines the keys that will be tracked across the workflow (e.g. `query`, `search_results`, `summary`, `response`, etc.).

2. **Tool Functions**  
   - `web_search_tool(query)`: uses SerpAPI to fetch search results  
   - `summarize_tool(text)`: uses an LLM chain + prompt template to summarize text  
   - `store_tool(data)`: stores the summary in a local in-memory store (a Python dict)  
   - `respond_tool(query)`: retrieves the stored summary and formats a final answer  

3. **Handler Functions (Nodes)**  
   Each node (step) is a handler that takes the current state, updates it, and returns it:
   - `ask_handler` — prompts user to input their research query  
   - `search_handler` — runs web search and stores results  
   - `summarize_handler` — summarizes the search results  
   - `store_handler` — stores the summary in memory  
   - `respond_handler` — prepares the final answer from memory  

4. **Constructing the Graph Workflow**  
   Using `StateGraph` from LangGraph:
   - Add nodes for each handler  
   - Define entry point (start at `ask`)  
   - Add directed edges to specify flow:  
     `ask → search → summarize → store → respond`  
   - Compile the graph into an executable agent  

5. **Running the Agent**  
   - Start from an empty initial state (`{}`)  
   - Invoke the graph to execute handlers in sequence  
   - At the end, print `state["response"]` as the final summarized output  

### How It Works (Example Flow)

- The agent asks the user: *“What do you want to research?”*  
- The user inputs a query (e.g. “climate change effects”)  
- The agent executes a web search using SerpAPI  
- It summarizes the returned results using the LLM  
- It stores the summary in memory (keyed by query)  
- Finally, it retrieves from memory and prints the final summary  

### Strengths, Limitations & Applications

**Strengths:**

- Modular: Each step is cleanly separated  
- Stateful: It carries information across steps  
- Extendable: You can add branching logic, loops, error handling, or more agents  
- Declarative workflow: LangGraph lets you visualize flows rather than hardcode monolithic logic  

**Limitations (in this simple demo):**

- Memory is volatile (in this demo it’s just an in-memory dict)  
- Error/exception handling is minimal  
- No branching / conditional logic (always linear flow)  
- Input/output is synchronous and interactive; not yet production-grade  

**Use Cases / Real-World Applications (as per article):**

- Automating in-depth research assistants  
- Enterprise AI copilots (fetching internal docs, summarizing reports)  
- Autonomous agents for portfolio monitoring or trading  
- Customer support agents that perform multi-step resolution  

---



1. Install dependencies:  
   ```bash
   pip install langchain langgraph langchain_ollama langchain-community

