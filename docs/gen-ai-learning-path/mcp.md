# Model Context Protocol (MCP)

## Overview

The Model Context Protocol (MCP) is a framework designed to enhance the interaction between large language models (LLMs) and external data sources. It provides a structured way to manage the context in which LLMs operate, allowing them to access and utilize external information effectively.

## Resources

- [MCP Crash Course](https://www.youtube.com/watch?v=5xqFjh56AwM&ab_channel=DaveEbbelaar)
- [Fast MCP](https://gofastmcp.com/getting-started/welcome)

## Projects

**MCP Implementation**

- Replace all tools used in the previous projects with MCP.

## Example MCP Server

```python
from fastmcp import FastMCP, Client
from tavily import TavilyClient
import logging
import os
import asyncio


logging.basicConfig(level=logging.INFO,
                    format='%(asctime)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)

TAVILY_API_KEY = "your_tavily_api_key_here"

mcp = FastMCP("My MCP Server")
tavily_client = TavilyClient(api_key=TAVILY_API_KEY)


@mcp.tool()
async def search_web(query: str) -> str:
    """Search the web for information."""
    logger.info(f"-----MCP: Searching for: {query}")
    response = tavily_client.search(query, limit=3)
    results = response.get("results", [])

    # Extract summaries from results
    summaries = [
        f"Title: {item['title']}\nURL: {item['url']}\nContent: {item['content'][:300]}..."
        for item in results
    ]

    return "\n\nGautam\n\n".join(summaries)


@mcp.tool()
async def write_summary(content: str) -> str:
    """Write a summary of the provided content."""
    summary = f"Summary of findings:\n\n{content[:500]}..."
    return summary


if __name__ == "__main__":
    mcp.run(transport="streamable-http") # Always use "streamable-http" when building apis with MCP

```

## Example MCP Client

```python
from langchain_mcp_adapters.client import MultiServerMCPClient
from langchain.chat_models import init_chat_model

client = MultiServerMCPClient(
    {
        "example": {
            # make sure you start your weather server on port 8000
            "url": "http://localhost:8000/mcp/",
            "transport": "streamable_http",
        }
    }
)
mcp_tools = await client.get_tools()



llm=init_chat_model("groq:llama-3.1-8b-instant")
llm_with_tools = llm.bind_tools(mcp_tools)
```
