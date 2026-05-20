# Model Context Protocol (MCP)

## What Is MCP?

**Model Context Protocol** is a **standardized way** for AI models and tools to communicate with each other.

### The Problem MCP Solves

Before MCP:

- Each AI tool/agent had its own custom way to call functions
- No standardization - messy, hard to integrate
- Lots of duplicate code for similar functionality

With MCP:

- Standard protocol everyone follows
- Tools can be easily swapped
- Once you learn it, works everywhere

### Real-World Analogy

Think of MCP like **HTTP for AI tools:**

- **Without HTTP:** Every website has its own communication protocol
- **With HTTP:** All websites use the same protocol
- **Same with MCP:** All AI tools use same protocol

### MCP Use Cases

- **AI Assistants** accessing company databases or APIs
- **Agents** using standard tools for web search, calculations, etc.
- **LLMs** connecting to external data sources
- **Multi-tool workflows** where tools need to communicate

---

## How MCP Works

### MCP Architecture

```
┌─────────────────┐
│  LLM / Agent    │
│   (Claude, etc) │
└────────┬────────┘
         │ (MCP protocol)
    ┌────▼─────────┐
    │  MCP Server  │  (Exposes tools/data)
    └─┬────┬───┬──┬─┘
      │    │   │  │
   ┌──▼─┐ │ ┌─▼──▼────┐  ┌──────────────────┐
   │Web │ │ │Database  │  │External API      │
   │API │ │ │Query     │  │(Stock prices,    │
   └────┘ │ └──────────┘  │search engines)   │
          │               └──────────────────┘
      ┌───▼──────┐
      │ Custom   │
      │ Code     │
      │ Tool     │
      └──────────┘
```

### MCP Components

**MCP Server:**

- Exposes tools and data sources
- Runs your custom code
- Provides standardized interface

**MCP Client:**

- LLM or agent using the MCP server
- Makes tool calls through MCP protocol
- Gets results back

**MCP Tools:**

- Functions/capabilities server exposes
- Examples: search, calculate, query database, send email

---

## Beginner Level: Understanding MCP

### Learning Outcomes

- Understand what MCP is and why it matters
- Know how MCP differs from alternatives
- Recognize MCP architecture

### Core Concepts

**Protocol:** Set of rules for how two systems communicate.

**Server:** System that provides tools/services (implements MCP).

**Client:** System that uses the tools (LLM, Agent).

**Tool:** Function/capability exposed by MCP server.

### Why Use MCP?

| Aspect | Without MCP | With MCP |
|--------|---|---|
| **Integration** | Custom code for each tool | Standard protocol |
| **Learning curve** | Different for each tool | Learn once, use everywhere |
| **Switching tools** | Rewrite code | Just swap the server |
| **Maintenance** | Lots of custom logic | Standardized maintenance |

### MCP vs. Other Approaches

| Approach | Use Case | Pros | Cons |
|----------|----------|------|------|
| **Direct API calls** | Quick prototype | Simple | No standard, hard to scale |
| **Webhooks** | Event-driven | Flexible | Complex setup |
| **OpenAI Function Calling** | OpenAI-specific | Easy | Locked to one provider |
| **MCP** | Multiple tools, standards | Interoperable, standard | Newer, less adoption |

---

## Intermediate Level: Building MCP Servers

### Learning Outcomes

- Build a working MCP server
- Expose tools through MCP
- Connect agent to MCP server

### Creating an MCP Server

An MCP server exposes tools that clients (LLMs, agents) can call.

**Basic Structure:**

```python
from fastmcp import FastMCP

# Create server
mcp = FastMCP("My Service")

# Define a tool
@mcp.tool()
async def my_tool(param: str) -> str:
    """Tool description."""
    return "Tool result"

# Run server
if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```

### Example: Web Search MCP Server

```python
from fastmcp import FastMCP
from tavily import TavilyClient
import os
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

mcp = FastMCP("Search Service")
tavily_client = TavilyClient(api_key=os.getenv("TAVILY_API_KEY"))

@mcp.tool()
async def search_web(query: str) -> str:
    """Search the web for information about the query.
    
    Args:
        query: The search query
        
    Returns:
        Search results with titles, URLs, and summaries
    """
    logger.info(f"Searching for: {query}")
    response = tavily_client.search(query, limit=5)
    results = response.get("results", [])
    
    formatted_results = []
    for item in results:
        formatted_results.append(
            f"Title: {item['title']}\n"
            f"URL: {item['url']}\n"
            f"Content: {item['content'][:500]}..."
        )
    
    return "\n\n".join(formatted_results)

@mcp.tool()
async def extract_facts(content: str) -> str:
    """Extract key facts from the given content.
    
    Args:
        content: Text to extract facts from
        
    Returns:
        Bullet-point list of key facts
    """
    # You could use an LLM here for more sophisticated extraction
    sentences = content.split(". ")
    facts = [f"• {s.strip()}" for s in sentences[:5] if s.strip()]
    return "\n".join(facts)

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```

### Using MCP Server from Agent

```python
from langchain_mcp_adapters.client import MultiServerMCPClient
from langchain.chat_models import init_chat_model

# Connect to MCP server
client = MultiServerMCPClient(
    {
        "search_service": {
            "url": "http://localhost:8000/mcp/",
            "transport": "streamable_http",
        }
    }
)

# Get available tools
mcp_tools = await client.get_tools()

# Create LLM with tools
llm = init_chat_model("gpt-4")
llm_with_tools = llm.bind_tools(mcp_tools)

# Use in agent
from langchain.agents import create_react_agent, AgentExecutor

agent = create_react_agent(llm_with_tools, mcp_tools)
executor = AgentExecutor.from_agent_and_tools(agent, mcp_tools)
result = executor.invoke({"input": "Search for latest AI news"})
```

### Best Practices

1. **Clear tool descriptions:** Helps LLM understand when to use each tool
2. **Type hints:** Specify input/output types precisely
3. **Error handling:** Catch and return meaningful error messages
4. **Logging:** Log tool calls for debugging
5. **Performance:** Keep tool responses quick

---

## Advanced Level: Production MCP

### Learning Outcomes

- Deploy MCP servers
- Handle errors and edge cases
- Monitor MCP performance
- Integrate with multiple tools

### Advanced Concepts

**Multi-tool Server:**

```python
mcp = FastMCP("Multi-Service Hub")

@mcp.tool()
async def search_web(query: str) -> str:
    # Web search implementation
    pass

@mcp.tool()
async def query_database(sql: str) -> str:
    # Database query implementation
    pass

@mcp.tool()
async def send_email(to: str, subject: str, body: str) -> str:
    # Email sending implementation
    pass
```

**Error Handling:**

```python
@mcp.tool()
async def risky_tool(param: str) -> str:
    try:
        result = perform_risky_operation(param)
        return result
    except ValueError as e:
        return f"Invalid input: {str(e)}"
    except Exception as e:
        logger.error(f"Tool error: {e}")
        return f"Tool failed: {str(e)}"
```

**Async Operations:**

```python
import asyncio

@mcp.tool()
async def parallel_search(queries: list[str]) -> str:
    """Search multiple queries in parallel."""
    tasks = [
        search_web(query)
        for query in queries
    ]
    results = await asyncio.gather(*tasks)
    return "\n\n".join(results)
```

### Deployment Options

**Local Development:**

```bash
python mcp_server.py
# Access at http://localhost:8000/mcp/
```

**Docker Container:**

```dockerfile
FROM python:3.11
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY mcp_server.py .
CMD ["python", "mcp_server.py"]
```

**Cloud Deployment:**

- AWS Lambda for serverless functions
- Google Cloud Run for containerized services
- Azure Functions for managed hosting

---

## Projects

### Project 1: Simple MCP Server

- **Goal:** Create MCP server with 1-2 tools
- **Tools:** Calculator, text summarizer
- **Evaluation:** Tools are accessible and work correctly

### Project 2: Multi-tool MCP Hub

- **Goal:** Combine multiple services into one MCP server
- **Tools:** Web search, database query, file operations
- **Integration:** Connect to agent that uses all tools

### Project 3: Production MCP Service

- **Goal:** Deploy MCP server with proper error handling
- **Features:** Authentication, logging, monitoring
- **Tools:** Connect to real APIs (weather, finance, etc.)

### Project 4: Custom Workflow

- **Goal:** Build MCP server for your specific domain
- **Example:** Finance (stock prices, portfolio tracking), Healthcare (patient records), etc.
- **Tools:** Domain-specific tools

---

## Common Patterns

### Pattern 1: Wrapper for Existing APIs

```python
@mcp.tool()
async def get_weather(city: str) -> str:
    """Get current weather for a city."""
    response = requests.get(f"https://api.weather.com/{city}")
    return response.json()
```

### Pattern 2: Database Queries

```python
@mcp.tool()
async def query_products(category: str, min_price: int) -> str:
    """Query products by category and price."""
    query = """
        SELECT name, price FROM products
        WHERE category = ? AND price >= ?
    """
    results = db.execute(query, (category, min_price))
    return json.dumps(results)
```

### Pattern 3: Data Transformation

```python
@mcp.tool()
async def convert_document(format_from: str, format_to: str, content: str) -> str:
    """Convert document between formats."""
    # Markdown to HTML, PDF to text, etc.
    result = transformation_library.convert(content, format_from, format_to)
    return result
```

---

## When to Use MCP

**Use MCP when:**

- Multiple tools need standard interface
- Building enterprise system with many integrations
- Want interoperability between different LLMs
- Need standardized tool discovery and management

**Don't use MCP when:**

- One-off prototype
- Simple API wrapper
- Performance is critical (adds slight overhead)
- Requires real-time communication

---

## Learning Resources

- **Official:**
    - [MCP Specification](https://modelcontextprotocol.io/)
    - [FastMCP Getting Started](https://gofastmcp.com/getting-started/welcome)

- **Tutorials:**
    - [MCP Crash Course](https://www.youtube.com/watch?v=5xqFjh56AwM&ab_channel=DaveEbbelaar)
    - [Building MCP Servers](https://www.youtube.com/results?search_query=MCP+server+tutorial)

- **Documentation:**
    - [FastMCP Python Library](https://github.com/jlopp/FastMCP)
    - [LangChain MCP Adapters](https://python.langchain.com/docs/langgraph/cloud/deployment/mcp_servers)

---

## Decision Framework: When to Use MCP

```
Need multiple tools?
  ├─ NO → Call APIs directly
  └─ YES → Need standardization?
       ├─ NO → Use custom wrapper
       └─ YES → Need interoperability?
            ├─ NO → Custom solution is fine
            └─ YES → Use MCP ✓
```

---

## Next Steps

1. **Start simple:** Create MCP server with one tool
2. **Test locally:** Verify tool works with agent
3. **Expand:** Add more tools to the server
4. **Deploy:** Move to cloud or production environment
5. **Monitor:** Track tool usage and performance

See [Production Deployment](./production.md) for deployment details.
