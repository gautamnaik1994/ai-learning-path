# Agents

## What Are Agents?

Agents are **autonomous systems** that use LLMs to understand tasks, make decisions, and take actions. Unlike simple chatbots that respond to questions, agents can:

- **Break down complex problems** into steps
- **Call external tools** (search, calculate, send emails, query databases)
- **Reason about results** and decide next steps
- **Iterate** until the goal is achieved

### Real-World Examples

| Use Case | What the Agent Does |
|----------|---|
| **Research Assistant** | Researches topic, finds sources, synthesizes findings into report |
| **Customer Support** | Gathers customer info, checks knowledge base, accesses systems to resolve issue |
| **Code Assistant** | Understands requirements, searches documentation, writes and tests code |
| **Financial Advisor** | Gathers market data, calculates scenarios, recommends strategies |

### Agents vs. RAG vs. Fine-tuning

| Approach | When to Use | Limitation |
|----------|---|---|
| **Simple LLM** | General questions, simple tasks | No tool access; can hallucinate |
| **RAG** | Question-answering over documents | Passive retrieval; no multi-step reasoning |
| **Fine-tuning** | Specific domain knowledge | Expensive, requires retraining for new info |
| **Agents** | Complex multi-step tasks, tool use | More complex to implement |

**Decision: Use Agents when you need:**

- Multiple steps to solve a problem
- Access to external tools (APIs, databases, search)
- Reasoning and decision-making
- Adaptive behavior based on intermediate results

---

## How Agents Work

### The Agent Loop

1. **Observe:** Agent receives task and available tools
2. **Think:** LLM reasons about task and decides which tool to use
3. **Act:** Agent calls the tool
4. **Reflect:** LLM evaluates result
5. **Repeat:** If goal not reached, loop back to step 2
6. **Respond:** Return final answer to user

### Example: Research Agent

```
User: "Find info about latest AI trends and write a summary"

Step 1 (Think): "I need to search for latest AI trends"
Step 2 (Act): Call search_web("AI trends 2025")
Step 3 (Reflect): Got 5 articles. Need more specific info.
Step 4 (Act): Call search_web("large language models advances 2025")
Step 5 (Reflect): Have enough info. Now write summary.
Step 6 (Act): Write comprehensive summary from search results
Step 7 (Respond): Return summary to user
```

---

## Beginner Level: Understanding Agents

### Learning Outcomes

- Understand agent concepts and when to use them
- Know agent types and architectures
- Recognize tool use and decision-making

### Core Concepts

**Tools:** External functions agents can call (search, calculator, email, API, etc.)

**Tool Calling/Function Calling:** LLM can decide which tool to use and with what parameters.

**Tool Calling Flow:**

```
LLM: "I'll use the search_web tool with query='AI trends'"
System: Executes search_web('AI trends')
LLM: Sees results, decides next action
```

### Agent Architectures

#### 1. Single Agent

Simple agent with fixed set of tools.

```
User → LLM Agent → Tools (search, calculator, etc.) → Response
```

**Use:** Simple tasks with clear steps.

#### 2. Supervisor Agent (Multi-agent)

Central "supervisor" coordinates multiple specialist agents.

```
         ┌─ Analyst Agent (analyzes data)
User → Supervisor Agent ├─ Researcher Agent (finds info)
         └─ Writer Agent (writes report)
```

**Use:** Complex tasks requiring different specialists.

#### 3. Hierarchical Agents

Tree structure with management levels.

```
              CEO
          /    |    \
    Research   IT   Writing
      /  \    / \    / \
    Data Market Dev Infra Tech  Summary
```

**Use:** Very complex problems requiring delegation.

---

## Intermediate Level: Building Agents

### Learning Outcomes

- Build working agent with LangChain/LangGraph
- Create custom tools for agent to use
- Handle agent errors and edge cases

### Tools for Building Agents

**LangChain:**

- Higher-level, easier to start
- Predefined agents and tools
- Good for prototyping

**LangGraph:**

- Lower-level, more control
- Explicit state management
- Good for complex workflows

### Basic Agent Structure

```python
from langchain_openai import ChatOpenAI
from langchain.agents import create_react_agent, AgentExecutor
from langchain.tools import Tool

# Define tools
def search_web(query):
    # Your search implementation
    return "Search results..."

tools = [
    Tool(
        name="search_web",
        func=search_web,
        description="Search the web for information"
    )
]

# Create agent
llm = ChatOpenAI(model="gpt-4")
agent = create_react_agent(llm, tools)
executor = AgentExecutor.from_agent_and_tools(
    agent=agent, 
    tools=tools, 
    verbose=True
)

# Use agent
result = executor.invoke({"input": "Research latest AI trends"})
```

### Supervisor Agent Example

Task: "Conduct market research and write a report"

The supervisor:

1. Routes task to Researcher (finds information)
2. Routes results to Analyst (analyzes findings)
3. Routes insights to Writer (writes report)
4. Compiles final response

### Projects

**Project 1: Research Assistant**

- Tools: web search, summarization
- Task: "Research [topic] and give me a 3-paragraph summary"
- Evaluation: Accuracy of summary, quality of sources

**Project 2: Code Helper Agent**

- Tools: search documentation, write code, run tests
- Task: "Help me write a Python function that [requirement]"
- Evaluation: Code correctness, documentation quality

**Project 3: Multi-agent System**

- Agents: Researcher, Analyst, Writer
- Task: "Analyze a market and write a detailed report"
- Evaluation: Report comprehensiveness and insights

---

## Advanced Level: Production Agents

### Learning Outcomes

- Handle errors and edge cases
- Optimize for cost and latency
- Monitor and improve agent performance
- Deploy agents in production

### Advanced Concepts

**Agent Memory:**

- Short-term: Current conversation context
- Long-term: Previous interactions and learnings
- Shared: Between multiple agents

**Tool Validation:**

- Validate tool inputs before calling
- Handle tool errors gracefully
- Fallback strategies when tools fail

**Cost Optimization:**

- Use cheaper models for simple reasoning
- Cache common queries
- Batch tool calls when possible

**Monitoring & Logging:**

- Log all agent decisions and tool calls
- Track success/failure rates
- Identify bottlenecks

### Advanced Projects

**Project: Customer Support Agent**

- Tools: customer database, ticket system, knowledge base, email
- Features: Multi-turn conversation, ticket creation, solution recommendations
- Monitoring: Response time, resolution rate, customer satisfaction

**Project: Data Analysis Agent**

- Tools: SQL queries, statistical analysis, charting
- Features: Multi-step analysis, hypothesis testing
- Optimization: Query optimization, caching results

---

## Common Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Agent gets stuck in loop | Add max iterations, better error handling |
| Tool gives irrelevant results | Improve tool descriptions, add input validation |
| Hallucination (making up tool results) | Use real tools only, validate outputs |
| Slow execution | Parallel tool calls, caching, cheaper models |
| High costs | Smaller models, fewer tool calls, batching |

---

## When NOT to Use Agents

- **Simple Q&A** - Use simple LLM or RAG
- **Static content** - Use RAG with documents
- **Real-time requirements** - Agents add latency
- **Guaranteed accuracy needed** - Agents can make mistakes
- **No external data** - Use fine-tuning instead

---

## Learning Resources

- [LangChain Agents Documentation](https://python.langchain.com/docs/concepts/agents/)
- [LangGraph Introduction](https://python.langchain.com/docs/langgraph/)
- **YouTube:**
    - [Agentic AI With LangGraph](https://www.youtube.com/playlist?list=PLZoTAELRMXVPFd7JdvB-rnTb_5V26NYNO)
    - [Building Agents with LangChain](https://www.youtube.com/watch?v=VisS2yPzIWc&ab_channel=DataIndependent)

- **Courses:**
    - [Introduction to LangGraph](https://academy.langchain.com/collections) (Free)
    - [Build Deep Research Agent Using LangGraph](https://academy.langchain.com/courses/deep-research-with-langgraph) (Free)
    - [Build Smart Email Agent using LangGraph](https://academy.langchain.com/courses/ambient-agents) (Free)

---

## Decision Framework: Agents vs. Alternatives

```
Task requires multiple steps?
  ├─ NO → Use simple LLM or RAG
  └─ YES → Need tool access?
       ├─ NO → Use fine-tuning or RAG
       └─ YES → Need reasoning?
            ├─ NO → Use simple tools
            └─ YES → Use Agents ✓
```

---

## Next Steps

1. **Start with LangChain agents** for simpler implementation
2. **Build Research Assistant** as first project
3. **Graduate to LangGraph** for complex workflows
4. **Explore Supervisor/Hierarchical agents** for multi-agent systems
5. **Deploy to production** with proper monitoring

See [Production Deployment](./production.md) for deployment strategies.
