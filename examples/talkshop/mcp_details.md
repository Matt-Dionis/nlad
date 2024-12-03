# Introducing the Model Context Protocol

Today, we're open-sourcing the Model Context Protocol (MCP), a new standard for connecting AI assistants to the systems where data lives, including content repositories, business tools, and development environments. Its aim is to help frontier models produce better, more relevant responses.

As AI assistants gain mainstream adoption, the industry has invested heavily in model capabilities, achieving rapid advances in reasoning and quality. Yet even the most sophisticated models are constrained by their isolation from data—trapped behind information silos and legacy systems. Every new data source requires its own custom implementation, making truly connected systems difficult to scale.

MCP addresses this challenge. It provides a universal, open standard for connecting AI systems with data sources, replacing fragmented integrations with a single protocol. The result is a simpler, more reliable way to give AI systems access to the data they need.

Model Context Protocol
The Model Context Protocol is an open standard that enables developers to build secure, two-way connections between their data sources and AI-powered tools. The architecture is straightforward: developers can either expose their data through MCP servers or build AI applications (MCP clients) that connect to these servers.

Today, we're introducing three major components of the Model Context Protocol for developers:

The Model Context Protocol specification and SDKs
Local MCP server support in the Claude Desktop apps
An open-source repository of MCP servers
Claude 3.5 Sonnet is adept at quickly building MCP server implementations, making it easy for organizations and individuals to rapidly connect their most important datasets with a range of AI-powered tools. To help developers start exploring, we’re sharing pre-built MCP servers for popular enterprise systems like Google Drive, Slack, GitHub, Git, Postgres, and Puppeteer.

Early adopters like Block and Apollo have integrated MCP into their systems, while development tools companies including Zed, Replit, Codeium, and Sourcegraph are working with MCP to enhance their platforms—enabling AI agents to better retrieve relevant information to further understand the context around a coding task and produce more nuanced and functional code with fewer attempts.

"At Block, open source is more than a development model—it’s the foundation of our work and a commitment to creating technology that drives meaningful change and serves as a public good for all,” said Dhanji R. Prasanna, Chief Technology Officer at Block. “Open technologies like the Model Context Protocol are the bridges that connect AI to real-world applications, ensuring innovation is accessible, transparent, and rooted in collaboration. We are excited to partner on a protocol and use it to build agentic systems, which remove the burden of the mechanical so people can focus on the creative.”

Instead of maintaining separate connectors for each data source, developers can now build against a standard protocol. As the ecosystem matures, AI systems will maintain context as they move between different tools and datasets, replacing today's fragmented integrations with a more sustainable architecture.

Getting started
Developers can start building and testing MCP connectors today. Existing Claude for Work customers can begin testing MCP servers locally, connecting Claude to internal systems and datasets. We'll soon provide developer toolkits for deploying remote production MCP servers that can serve your entire Claude for Work organization.

The Model Context Protocol (MCP) is an open protocol that enables seamless integration between LLM applications and external data sources and tools. Whether you’re building an AI-powered IDE, enhancing a chat interface, or creating custom AI workflows, MCP provides a standardized way to connect LLMs with the context they need.

# Quickstart

Get started with MCP in less than 5 minutes

MCP is a protocol that enables secure connections between host applications, such as Claude Desktop, and local services. In this quickstart guide, you’ll learn how to:

- Set up a local SQLite database
- Connect Claude Desktop to it through MCP
- Query and analyze your data securely

_While this guide focuses on using Claude Desktop as an example MCP host, the protocol is open and can be integrated by any application. IDEs, AI tools, and other software can all use MCP to connect to local integrations in a standardized way._

_Claude Desktop’s MCP support is currently in developer preview and only supports connecting to local MCP servers running on your machine. Remote MCP connections are not yet supported. This integration is only available in the Claude Desktop app, not the Claude web interface (claude.ai)._

## How MCP works

MCP (Model Context Protocol) is an open protocol that enables secure, controlled interactions between AI applications and local or remote resources. Let’s break down how it works, then look at how we’ll use it in this guide.

### General Architecture

At its core, MCP follows a client-server architecture where a host application can connect to multiple servers:

- **MCP Hosts:** Programs like Claude Desktop, IDEs, or AI tools that want to access resources through MCP
- **MCP Clients:** Protocol clients that maintain 1:1 connections with servers
- **MCP Servers:** Lightweight programs that each expose specific capabilities through the standardized Model Context Protocol
- **Local Resources:** Your computer’s resources (databases, files, services) that MCP servers can securely access
- **Remote Resources:** Resources available over the internet (e.g., through APIs) that MCP servers can connect to

### In This Guide

For this quickstart, we’ll implement a focused example using SQLite:

1. Claude Desktop acts as our MCP client
2. A SQLite MCP Server provides secure database access
3. Your local SQLite database stores the actual data

The communication between the SQLite MCP server and your local SQLite database happens entirely on your machine—your SQLite database is not exposed to the internet. The Model Context Protocol ensures that Claude Desktop can only perform approved database operations through well-defined interfaces. This gives you a secure way to let Claude analyze and interact with your local data while maintaining complete control over what it can access.

## Prerequisites

- macOS or Windows
- The latest version of Claude Desktop installed
- uv 0.4.18 or higher (uv --version to check)
- Git (git --version to check)
- SQLite (sqlite3 --version to check)

```bash
# Using Homebrew
brew install uv git sqlite3

# Or download directly:
# uv: https://docs.astral.sh/uv/
# Git: https://git-scm.com
# SQLite: https://www.sqlite.org/download.html
```

## Installation

1. Create a sample database

Let’s create a simple SQLite database for testing:

```bash
# Create a new SQLite database
sqlite3 ~/test.db <<EOF
CREATE TABLE products (
  id INTEGER PRIMARY KEY,
  name TEXT,
  price REAL
);

INSERT INTO products (name, price) VALUES
  ('Widget', 19.99),
  ('Gadget', 29.99),
  ('Gizmo', 39.99),
  ('Smart Watch', 199.99),
  ('Wireless Earbuds', 89.99),
  ('Portable Charger', 24.99),
  ('Bluetooth Speaker', 79.99),
  ('Phone Stand', 15.99),
  ('Laptop Sleeve', 34.99),
  ('Mini Drone', 299.99),
  ('LED Desk Lamp', 45.99),
  ('Keyboard', 129.99),
  ('Mouse Pad', 12.99),
  ('USB Hub', 49.99),
  ('Webcam', 69.99),
  ('Screen Protector', 9.99),
  ('Travel Adapter', 27.99),
  ('Gaming Headset', 159.99),
  ('Fitness Tracker', 119.99),
  ('Portable SSD', 179.99);
EOF

```

2. Configure Claude Desktop

Open your Claude Desktop App configuration at `~/Library/Application Support/Claude/claude_desktop_config.json` in a text editor.

For example, if you have VS Code installed:

```bash
code ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

Add this configuration (replace YOUR_USERNAME with your actual username):

```json
{
  "mcpServers": {
    "sqlite": {
      "command": "uvx",
      "args": ["mcp-server-sqlite", "--db-path", "/Users/YOUR_USERNAME/test.db"]
    }
  }
}
```

This tells Claude Desktop:

1. There’s an MCP server named “sqlite”
2. Launch it by running `uvx mcp-server-sqlite`
3. Connect it to your test database

Save the file, and restart Claude Desktop.

## Test it out

Let’s verify everything is working. Try sending this prompt to Claude Desktop:

"Can you connect to my SQLite database and tell me what products are available, and their prices?"

Claude Desktop will:

1. Connect to the SQLite MCP server
2. Query your local database
3. Format and present the results

## What’s happening under the hood?

When you interact with Claude Desktop using MCP:

1. **Server Discovery:** Claude Desktop connects to your configured MCP servers on startup
2. **Protocol Handshake:** When you ask about data, Claude Desktop:

   - Identifies which MCP server can help (sqlite in this case)
   - Negotiates capabilities through the protocol
   - Requests data or actions from the MCP server

3. **Interaction Flow:**

4. **Security:**

   - MCP servers only expose specific, controlled capabilities
   - MCP servers run locally on your machine, and the resources they access are not exposed to the internet
   - Claude Desktop requires user confirmation for sensitive operations

## Try these examples

Now that MCP is working, try these increasingly powerful examples:

**Basic Queries**

"What's the average price of all products in the database?"

**Data Analysis**

"Can you analyze the price distribution and suggest any pricing optimizations?"

**Complex Operations**

"Could you help me design and create a new table for storing customer orders?"

## Add more capabilities

Want to give Claude Desktop more local integration capabilities? Add these servers to your configuration:

_Note that these MCP servers will require Node.js to be installed on your machine._

**File System Access**

Add this to your config to let Claude Desktop read and analyze files:

```json
"filesystem": {
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/YOUR_USERNAME/Desktop"]
}
```

**PostgreSQL Connection**

Connect Claude Desktop to your PostgreSQL database:

```json
"postgres": {
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/mydb"]
}
```

## Troubleshooting

### Nothing showing up in Claude Desktop?

1. Check if MCP is enabled:

- Click the 🔌 icon in Claude Desktop, next to the chat box
- Expand “Installed MCP Servers”
- You should see your configured servers

2. Verify your config:

- From Claude Desktop, go to Claude > Settings…
- Open the “Developer” tab to see your configuration

3. Restart Claude Desktop completely:

- Quit the app (not just close the window)
- Start it again

### MCP or database errors?

1. Check Claude Desktop’s logs:

```bash
tail -n 20 -f ~/Library/Logs/Claude/mcp*.log
```

2. Verify database access:

```bash
# Test database connection
sqlite3 ~/test.db ".tables"
```

3. Common fixes:

- Check file paths in your config
- Verify database file permissions
- Ensure SQLite is installed properly

# Client details

## Claude Desktop App

The Claude desktop application provides comprehensive support for MCP, enabling deep integration with local tools and data sources.

**Key features:**

- Full support for resources, allowing attachment of local files and data
- Support for prompt templates
- Tool integration for executing commands and scripts
- Local server connections for enhanced privacy and security

# TypeScript MCP Server

Create a simple MCP server in TypeScript in 15 minutes

Let’s build your first MCP server in TypeScript! We’ll create a weather server that provides current weather data as a resource and lets Claude fetch forecasts using tools.

_This guide uses the OpenWeatherMap API. You’ll need a free API key from OpenWeatherMap to follow along._

## Prerequisites

1. Install Node.js

You’ll need Node.js 18 or higher:

```bash
node --version  # Should be v18 or higher
npm --version
```

2. Create a new project

You can use our create-typescript-server tool to bootstrap a new project:

```bash
npx @modelcontextprotocol/create-server weather-server
cd weather-server
```

3. Install dependencies

```bash
npm install --save axios dotenv
```

4. Set up environment

Create `.env`:

```bash
OPENWEATHER_API_KEY=your-api-key-here
```

Make sure to add your environment file to .gitignore

```bash
.env
```

## Create your server

1. Define types

Create a file src/types.ts, and add the following:

```typescript
export interface OpenWeatherResponse {
  main: {
    temp: number;
    humidity: number;
  };
  weather: Array<{
    description: string;
  }>;
  wind: {
    speed: number;
  };
  dt_txt?: string;
}

export interface WeatherData {
  temperature: number;
  conditions: string;
  humidity: number;
  wind_speed: number;
  timestamp: string;
}

export interface ForecastDay {
  date: string;
  temperature: number;
  conditions: string;
}

export interface GetForecastArgs {
  city: string;
  days?: number;
}

// Type guard for forecast arguments
export function isValidForecastArgs(args: any): args is GetForecastArgs {
  return (
    typeof args === "object" &&
    args !== null &&
    "city" in args &&
    typeof args.city === "string" &&
    (args.days === undefined || typeof args.days === "number")
  );
}
```

2. Add the base code

Replace `src/index.ts` with the following:

```typescript
#!/usr/bin/env node
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  ListResourcesRequestSchema,
  ReadResourceRequestSchema,
  ListToolsRequestSchema,
  CallToolRequestSchema,
  ErrorCode,
  McpError,
} from "@modelcontextprotocol/sdk/types.js";
import axios from "axios";
import dotenv from "dotenv";
import {
  WeatherData,
  ForecastDay,
  OpenWeatherResponse,
  isValidForecastArgs,
} from "./types.js";

dotenv.config();

const API_KEY = process.env.OPENWEATHER_API_KEY;
if (!API_KEY) {
  throw new Error("OPENWEATHER_API_KEY environment variable is required");
}

const API_CONFIG = {
  BASE_URL: "http://api.openweathermap.org/data/2.5",
  DEFAULT_CITY: "San Francisco",
  ENDPOINTS: {
    CURRENT: "weather",
    FORECAST: "forecast",
  },
} as const;

class WeatherServer {
  private server: Server;
  private axiosInstance;

  constructor() {
    this.server = new Server(
      {
        name: "example-weather-server",
        version: "0.1.0",
      },
      {
        capabilities: {
          resources: {},
          tools: {},
        },
      }
    );

    // Configure axios with defaults
    this.axiosInstance = axios.create({
      baseURL: API_CONFIG.BASE_URL,
      params: {
        appid: API_KEY,
        units: "metric",
      },
    });

    this.setupHandlers();
    this.setupErrorHandling();
  }

  private setupErrorHandling(): void {
    this.server.onerror = (error) => {
      console.error("[MCP Error]", error);
    };

    process.on("SIGINT", async () => {
      await this.server.close();
      process.exit(0);
    });
  }

  private setupHandlers(): void {
    this.setupResourceHandlers();
    this.setupToolHandlers();
  }

  private setupResourceHandlers(): void {
    // Implementation continues in next section
  }

  private setupToolHandlers(): void {
    // Implementation continues in next section
  }

  async run(): Promise<void> {
    const transport = new StdioServerTransport();
    await this.server.connect(transport);

    // Although this is just an informative message, we must log to stderr,
    // to avoid interfering with MCP communication that happens on stdout
    console.error("Weather MCP server running on stdio");
  }
}

const server = new WeatherServer();
server.run().catch(console.error);
```

3. Add resource handlers

Add this to the setupResourceHandlers method:

```typescript
private setupResourceHandlers(): void {
  this.server.setRequestHandler(
    ListResourcesRequestSchema,
    async () => ({
      resources: [{
        uri: `weather://${API_CONFIG.DEFAULT_CITY}/current`,
        name: `Current weather in ${API_CONFIG.DEFAULT_CITY}`,
        mimeType: "application/json",
        description: "Real-time weather data including temperature, conditions, humidity, and wind speed"
      }]
    })
  );

  this.server.setRequestHandler(
    ReadResourceRequestSchema,
    async (request) => {
      const city = API_CONFIG.DEFAULT_CITY;
      if (request.params.uri !== `weather://${city}/current`) {
        throw new McpError(
          ErrorCode.InvalidRequest,
          `Unknown resource: ${request.params.uri}`
        );
      }

      try {
        const response = await this.axiosInstance.get<OpenWeatherResponse>(
          API_CONFIG.ENDPOINTS.CURRENT,
          {
            params: { q: city }
          }
        );

        const weatherData: WeatherData = {
          temperature: response.data.main.temp,
          conditions: response.data.weather[0].description,
          humidity: response.data.main.humidity,
          wind_speed: response.data.wind.speed,
          timestamp: new Date().toISOString()
        };

        return {
          contents: [{
            uri: request.params.uri,
            mimeType: "application/json",
            text: JSON.stringify(weatherData, null, 2)
          }]
        };
      } catch (error) {
        if (axios.isAxiosError(error)) {
          throw new McpError(
            ErrorCode.InternalError,
            `Weather API error: ${error.response?.data.message ?? error.message}`
          );
        }
        throw error;
      }
    }
  );
}
```

4. Add tool handlers

Add these handlers to the setupToolHandlers method:

```typescript
private setupToolHandlers(): void {
  this.server.setRequestHandler(
    ListToolsRequestSchema,
    async () => ({
      tools: [{
        name: "get_forecast",
        description: "Get weather forecast for a city",
        inputSchema: {
          type: "object",
          properties: {
            city: {
              type: "string",
              description: "City name"
            },
            days: {
              type: "number",
              description: "Number of days (1-5)",
              minimum: 1,
              maximum: 5
            }
          },
          required: ["city"]
        }
      }]
    })
  );

  this.server.setRequestHandler(
    CallToolRequestSchema,
    async (request) => {
      if (request.params.name !== "get_forecast") {
        throw new McpError(
          ErrorCode.MethodNotFound,
          `Unknown tool: ${request.params.name}`
        );
      }

      if (!isValidForecastArgs(request.params.arguments)) {
        throw new McpError(
          ErrorCode.InvalidParams,
          "Invalid forecast arguments"
        );
      }

      const city = request.params.arguments.city;
      const days = Math.min(request.params.arguments.days || 3, 5);

      try {
        const response = await this.axiosInstance.get<{
          list: OpenWeatherResponse[]
        }>(API_CONFIG.ENDPOINTS.FORECAST, {
          params: {
            q: city,
            cnt: days * 8 // API returns 3-hour intervals
          }
        });

        const forecasts: ForecastDay[] = [];
        for (let i = 0; i < response.data.list.length; i += 8) {
          const dayData = response.data.list[i];
          forecasts.push({
            date: dayData.dt_txt?.split(' ')[0] ?? new Date().toISOString().split('T')[0],
            temperature: dayData.main.temp,
            conditions: dayData.weather[0].description
          });
        }

        return {
          content: [{
            type: "text",
            text: JSON.stringify(forecasts, null, 2)
          }]
        };
      } catch (error) {
        if (axios.isAxiosError(error)) {
          return {
            content: [{
              type: "text",
              text: `Weather API error: ${error.response?.data.message ?? error.message}`
            }],
            isError: true,
          }
        }
        throw error;
      }
    }
  );
}
```

5. Build and test

```bash
npm run build
```

## Connect to Claude Desktop

1. Update Claude config

If you didn’t already connect to Claude Desktop during project setup, add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "weather": {
      "command": "node",
      "args": ["/path/to/weather-server/build/index.js"],
      "env": {
        "OPENWEATHER_API_KEY": "your-api-key"
      }
    }
  }
}
```

2. Restart Claude

1. Quit Claude completely
1. Start Claude again
1. Look for your weather server in the 🔌 menu

## Try it out!

### Check Current Weather

Ask Claude: "What's the current weather in San Francisco? Can you analyze the conditions?"

### Get a Forecast

Ask Claude: "Can you get me a 5-day forecast for Tokyo and tell me if I should pack an umbrella?"

### Compare Weather

Ask Claude: "Can you analyze the forecast for both Tokyo and San Francisco and tell me which city will be warmer this week?"

## Understanding the code

### Type Safety

```typescript
interface WeatherData {
  temperature: number;
  conditions: string;
  humidity: number;
  wind_speed: number;
  timestamp: string;
}
```

TypeScript adds type safety to our MCP server, making it more reliable and easier to maintain.

### Resources

```typescript
this.server.setRequestHandler(ListResourcesRequestSchema, async () => ({
  resources: [
    {
      uri: `weather://${DEFAULT_CITY}/current`,
      name: `Current weather in ${DEFAULT_CITY}`,
      mimeType: "application/json",
    },
  ],
}));
```

Resources provide data that Claude can access as context.

### Tools

```typescript
{
  name: "get_forecast",
  description: "Get weather forecast for a city",
  inputSchema: {
    type: "object",
    properties: {
      city: { type: "string" },
      days: { type: "number" }
    }
  }
}
```

Tools let Claude take actions through your server with type-safe inputs.

## Best practices

### Error Handling

When a tool encounters an error, return the error message with isError: true, so the model can self-correct:

```typescript
try {
  const response = await axiosInstance.get(...);
} catch (error) {
  if (axios.isAxiosError(error)) {
    return {
      content: {
        mimeType: "text/plain",
        text: `Weather API error: ${error.response?.data.message ?? error.message}`
      },
      isError: true,
    }
  }
  throw error;
}
```

For other handlers, throw an error, so the application can notify the user:

```typescript
try {
  const response = await this.axiosInstance.get(...);
} catch (error) {
  if (axios.isAxiosError(error)) {
    throw new McpError(
      ErrorCode.InternalError,
      `Weather API error: ${error.response?.data.message}`
    );
  }
  throw error;
}
```

### Type Validation

```typescript
function isValidForecastArgs(args: any): args is GetForecastArgs {
  return (
    typeof args === "object" &&
    args !== null &&
    "city" in args &&
    typeof args.city === "string"
  );
}
```

_You can also use libraries like Zod to perform this validation automatically._

## Available transports

While this guide uses stdio to run the MCP server as a local process, MCP supports other transports as well.

## Troubleshooting

_The following troubleshooting tips are for macOS. Guides for other platforms are coming soon._

### Build errors

```bash
# Check TypeScript version
npx tsc --version

# Clean and rebuild
rm -rf build/
npm run build
```

### Runtime errors

Look for detailed error messages in the Claude Desktop logs:

```bash
# Monitor logs
tail -n 20 -f ~/Library/Logs/Claude/mcp*.log
```

### Type errors

```bash
# Check types without building
npx tsc --noEmit
```

# Python MCP Server

Create a simple MCP server in Python in 15 minutes

Let’s build your first MCP server in Python! We’ll create a weather server that provides current weather data as a resource and lets Claude fetch forecasts using tools.

_This guide uses the OpenWeatherMap API. You’ll need a free API key from OpenWeatherMap to follow along._

## Prerequisites

_The following steps are for macOS. Guides for other platforms are coming soon._

1. Install Python

You’ll need Python 3.10 or higher:

```bash
python --version  # Should be 3.10 or higher
```

2. Install uv via homebrew

See https://docs.astral.sh/uv/ for more information.

```bash
brew install uv
uv --version # Should be 0.4.18 or higher
```

3. Create a new project using the MCP project creator

```bash
uvx create-mcp-server --path weather_service
cd weather_service
```

4. Install additional dependencies

```bash
uv add httpx python-dotenv
```

5. Set up environment

Create `.env`:

```bash
OPENWEATHER_API_KEY=your-api-key-here
```

## Create your server

1. Add the base imports and setup

In `weather_service/src/weather_service/server.py`

```python
import os
import json
import logging
from datetime import datetime, timedelta
from collections.abc import Sequence
from functools import lru_cache
from typing import Any

import httpx
import asyncio
from dotenv import load_dotenv
from mcp.server import Server
from mcp.types import (
    Resource,
    Tool,
    TextContent,
    ImageContent,
    EmbeddedResource,
    LoggingLevel
)
from pydantic import AnyUrl

# Load environment variables
load_dotenv()

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("weather-server")

# API configuration
API_KEY = os.getenv("OPENWEATHER_API_KEY")
if not API_KEY:
    raise ValueError("OPENWEATHER_API_KEY environment variable required")

API_BASE_URL = "http://api.openweathermap.org/data/2.5"
DEFAULT_CITY = "London"
CURRENT_WEATHER_ENDPOINT = "weather"
FORECAST_ENDPOINT = "forecast"

# The rest of our server implementation will go here
```

2. Add weather fetching functionality

Add this functionality:

```python
# Create reusable params
http_params = {
    "appid": API_KEY,
    "units": "metric"
}

async def fetch_weather(city: str) -> dict[str, Any]:
    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"{API_BASE_URL}/weather",
            params={"q": city, **http_params}
        )
        response.raise_for_status()
        data = response.json()

    return {
        "temperature": data["main"]["temp"],
        "conditions": data["weather"][0]["description"],
        "humidity": data["main"]["humidity"],
        "wind_speed": data["wind"]["speed"],
        "timestamp": datetime.now().isoformat()
    }


app = Server("weather-server")
```

3. Implement resource handlers

Add these resource-related handlers to our main function:

```python
app = Server("weather-server")

@app.list_resources()
async def list_resources() -> list[Resource]:
    """List available weather resources."""
    uri = AnyUrl(f"weather://{DEFAULT_CITY}/current")
    return [
        Resource(
            uri=uri,
            name=f"Current weather in {DEFAULT_CITY}",
            mimeType="application/json",
            description="Real-time weather data"
        )
    ]

@app.read_resource()
async def read_resource(uri: AnyUrl) -> str:
    """Read current weather data for a city."""
    city = DEFAULT_CITY
    if str(uri).startswith("weather://") and str(uri).endswith("/current"):
        city = str(uri).split("/")[-2]
    else:
        raise ValueError(f"Unknown resource: {uri}")

    try:
        weather_data = await fetch_weather(city)
        return json.dumps(weather_data, indent=2)
    except httpx.HTTPError as e:
        raise RuntimeError(f"Weather API error: {str(e)}")
```

4. Implement tool handlers

Add these tool-related handlers:

```python
app = Server("weather-server")

# Resource implementation ...

@app.list_tools()
async def list_tools() -> list[Tool]:
    """List available weather tools."""
    return [
        Tool(
            name="get_forecast",
            description="Get weather forecast for a city",
            inputSchema={
                "type": "object",
                "properties": {
                    "city": {
                        "type": "string",
                        "description": "City name"
                    },
                    "days": {
                        "type": "number",
                        "description": "Number of days (1-5)",
                        "minimum": 1,
                        "maximum": 5
                    }
                },
                "required": ["city"]
            }
        )
    ]

@app.call_tool()
async def call_tool(name: str, arguments: Any) -> Sequence[TextContent | ImageContent | EmbeddedResource]:
    """Handle tool calls for weather forecasts."""
    if name != "get_forecast":
        raise ValueError(f"Unknown tool: {name}")

    if not isinstance(arguments, dict) or "city" not in arguments:
        raise ValueError("Invalid forecast arguments")

    city = arguments["city"]
    days = min(int(arguments.get("days", 3)), 5)

    try:
        async with httpx.AsyncClient() as client:
            response = await client.get(
                f"{API_BASE_URL}/{FORECAST_ENDPOINT}",
                params={
                    "q": city,
                    "cnt": days * 8,  # API returns 3-hour intervals
                    **http_params,
                }
            )
            response.raise_for_status()
            data = response.json()

        forecasts = []
        for i in range(0, len(data["list"]), 8):
            day_data = data["list"][i]
            forecasts.append({
                "date": day_data["dt_txt"].split()[0],
                "temperature": day_data["main"]["temp"],
                "conditions": day_data["weather"][0]["description"]
            })

        return [
            TextContent(
                type="text",
                text=json.dumps(forecasts, indent=2)
            )
        ]
    except httpx.HTTPError as e:
        logger.error(f"Weather API error: {str(e)}")
        raise RuntimeError(f"Weather API error: {str(e)}")
```

5. Add the main function

Add this to the end of `weather_service/src/weather_service/server.py`:

```python
async def main():
    # Import here to avoid issues with event loops
    from mcp.server.stdio import stdio_server

    async with stdio_server() as (read_stream, write_stream):
        await app.run(
            read_stream,
            write_stream,
            app.create_initialization_options()
        )
```

6. Check your entry point in **init**.py

Add this to the end of `weather_service/src/weather_service/__init__.py`:

```python
from . import server
import asyncio

def main():
   """Main entry point for the package."""
   asyncio.run(server.main())

# Optionally expose other important items at package level
__all__ = ['main', 'server']
```

## Connect to Claude Desktop

1. Update Claude config

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "weather": {
      "command": "uv",
      "args": ["--directory", "path/to/your/project", "run", "weather-service"],
      "env": {
        "OPENWEATHER_API_KEY": "your-api-key"
      }
    }
  }
}
```

2. Restart Claude

   1. Quit Claude completely
   2. Start Claude again
   3. Look for your weather server in the 🔌 menu

## Try it out!

### Check Current Weather

"What's the current weather in San Francisco? Can you analyze the conditions and tell me if it's a good day for outdoor activities?"

### Get a Forecast

"Can you get me a 5-day forecast for Tokyo and help me plan what clothes to pack for my trip?"

### Compare Weather

"Can you analyze the forecast for both Tokyo and San Francisco and tell me which city would be better for outdoor photography this week?"

## Understanding the code

### Type Hints

```python
async def read_resource(self, uri: str) -> ReadResourceResult:
    # ...
```

### Resources

```python
@app.list_resources()
async def list_resources(self) -> ListResourcesResult:
    return ListResourcesResult(
        resources=[
            Resource(
                uri=f"weather://{DEFAULT_CITY}/current",
                name=f"Current weather in {DEFAULT_CITY}",
                mimeType="application/json",
                description="Real-time weather data"
            )
        ]
    )
```

### Tools

```python
Tool(
    name="get_forecast",
    description="Get weather forecast for a city",
    inputSchema={
        "type": "object",
        "properties": {
            "city": {
                "type": "string",
                "description": "City name"
            },
            "days": {
                "type": "number",
                "description": "Number of days (1-5)",
                "minimum": 1,
                "maximum": 5
            }
        },
        "required": ["city"]
    }
)
```

### Server Structure

```python
# Create server instance with name
app = Server("weather-server")

# Register resource handler
@app.list_resources()
async def list_resources() -> list[Resource]:
    """List available resources"""
    return [...]

# Register tool handler
@app.call_tool()
async def call_tool(name: str, arguments: Any) -> Sequence[TextContent]:
    """Handle tool execution"""
    return [...]

# Register additional handlers
@app.read_resource()
...
@app.list_tools()
...
```

Python type hints help catch errors early and improve code maintainability.

## Best practices

### Error Handling

```python
try:
    async with httpx.AsyncClient() as client:
        response = await client.get(..., params={..., **http_params})
        response.raise_for_status()
except httpx.HTTPError as e:
    raise McpError(
        ErrorCode.INTERNAL_ERROR,
        f"API error: {str(e)}"
    )
```

### Type Validation

```python
if not isinstance(args, dict) or "city" not in args:
    raise McpError(
        ErrorCode.INVALID_PARAMS,
        "Invalid forecast arguments"
    )
```

### Environment Variables

```python
if not API_KEY:
    raise ValueError("OPENWEATHER_API_KEY is required")
```

## Available transports

While this guide uses stdio transport, MCP supports additional transport options:

### SSE (Server-Sent Events)

````python
from mcp.server.sse import SseServerTransport
from starlette.applications import Starlette
from starlette.routing import Route

# Create SSE transport with endpoint
sse = SseServerTransport("/messages")

# Handler for SSE connections
async def handle_sse(scope, receive, send):
    async with sse.connect_sse(scope, receive, send) as streams:
        await app.run(
            streams[0], streams[1], app.create_initialization_options()
        )

# Handler for client messages
async def handle_messages(scope, receive, send):
    await sse.handle_post_message(scope, receive, send)

# Create Starlette app with routes
app = Starlette(
    debug=True,
    routes=[
        Route("/sse", endpoint=handle_sse),
        Route("/messages", endpoint=handle_messages, methods=["POST"]),
    ],
)

# Run with any ASGI server
import uvicorn
uvicorn.run(app, host="0.0.0.0", port=8000)
​```

## Advanced features

1. Understanding Request Context

The request context provides access to the current request’s metadata and the active client session. Access it through server.request_context:

```python
@app.call_tool()
async def call_tool(name: str, arguments: Any) -> Sequence[TextContent]:
    # Access the current request context
    ctx = self.request_context

    # Get request metadata like progress tokens
    if progress_token := ctx.meta.progressToken:
        # Send progress notifications via the session
        await ctx.session.send_progress_notification(
            progress_token=progress_token,
            progress=0.5,
            total=1.0
        )

    # Sample from the LLM client
    result = await ctx.session.create_message(
        messages=[
            SamplingMessage(
                role="user",
                content=TextContent(
                    type="text",
                    text="Analyze this weather data: " + json.dumps(arguments)
                )
            )
        ],
        max_tokens=100
    )

    return [TextContent(type="text", text=result.content.text)]
````

2. Add caching

```python
# Cache settings
cache_timeout = timedelta(minutes=15)
last_cache_time = None
cached_weather = None

async def fetch_weather(city: str) -> dict[str, Any]:
    global cached_weather, last_cache_time

    now = datetime.now()
    if (cached_weather is None or
        last_cache_time is None or
        now - last_cache_time > cache_timeout):

        async with httpx.AsyncClient() as client:
            response = await client.get(
                f"{API_BASE_URL}/{CURRENT_WEATHER_ENDPOINT}",
                params={"q": city, **http_params}
            )
            response.raise_for_status()
            data = response.json()

        cached_weather = {
            "temperature": data["main"]["temp"],
            "conditions": data["weather"][0]["description"],
            "humidity": data["main"]["humidity"],
            "wind_speed": data["wind"]["speed"],
            "timestamp": datetime.now().isoformat()
        }
        last_cache_time = now

    return cached_weather
```

3. Add progress notifications

```python
@self.call_tool()
async def call_tool(self, name: str, arguments: Any) -> CallToolResult:
    if progress_token := self.request_context.meta.progressToken:
        # Send progress notifications
        await self.request_context.session.send_progress_notification(
            progress_token=progress_token,
            progress=1,
            total=2
        )

        # Fetch data...

        await self.request_context.session.send_progress_notification(
            progress_token=progress_token,
            progress=2,
            total=2
        )

    # Rest of the method implementation...
```

4. Add logging support

```python
# Set up logging
logger = logging.getLogger("weather-server")
logger.setLevel(logging.INFO)

@app.set_logging_level()
async def set_logging_level(level: LoggingLevel) -> EmptyResult:
    logger.setLevel(level.upper())
    await app.request_context.session.send_log_message(
        level="info",
        data=f"Log level set to {level}",
        logger="weather-server"
    )
    return EmptyResult()

# Use logger throughout the code
# For example:
# logger.info("Weather data fetched successfully")
# logger.error(f"Error fetching weather data: {str(e)}")
```

5. Add resource templates

```python
@app.list_resource_templates()
async def list_resource_templates() -> list[ResourceTemplate]:
    return [
        ResourceTemplate(
            uriTemplate="weather://{city}/current",
            name="Current weather for any city",
            mimeType="application/json"
        )
    ]
```

## Testing

1. Create test file

Create `tests/weather_test.py`:

```python
import pytest
import os
from unittest.mock import patch, Mock
from datetime import datetime
import json
from pydantic import AnyUrl
os.environ["OPENWEATHER_API_KEY"] = "TEST"

from weather_service.server import (
    fetch_weather,
    read_resource,
    call_tool,
    list_resources,
    list_tools,
    DEFAULT_CITY
)

@pytest.fixture
def anyio_backend():
    return "asyncio"

@pytest.fixture
def mock_weather_response():
    return {
        "main": {
            "temp": 20.5,
            "humidity": 65
        },
        "weather": [
            {"description": "scattered clouds"}
        ],
        "wind": {
            "speed": 3.6
        }
    }

@pytest.fixture
def mock_forecast_response():
    return {
        "list": [
            {
                "dt_txt": "2024-01-01 12:00:00",
                "main": {"temp": 18.5},
                "weather": [{"description": "sunny"}]
            },
            {
                "dt_txt": "2024-01-02 12:00:00",
                "main": {"temp": 17.2},
                "weather": [{"description": "cloudy"}]
            }
        ]
    }

@pytest.mark.anyio
async def test_fetch_weather(mock_weather_response):
    with patch('requests.Session.get') as mock_get:
        mock_get.return_value.json.return_value = mock_weather_response
        mock_get.return_value.raise_for_status = Mock()

        weather = await fetch_weather("London")

        assert weather["temperature"] == 20.5
        assert weather["conditions"] == "scattered clouds"
        assert weather["humidity"] == 65
        assert weather["wind_speed"] == 3.6
        assert "timestamp" in weather

@pytest.mark.anyio
async def test_read_resource():
    with patch('weather_service.server.fetch_weather') as mock_fetch:
        mock_fetch.return_value = {
            "temperature": 20.5,
            "conditions": "clear sky",
            "timestamp": datetime.now().isoformat()
        }

        uri = AnyUrl("weather://London/current")
        result = await read_resource(uri)

        assert isinstance(result, str)
        assert "temperature" in result
        assert "clear sky" in result

@pytest.mark.anyio
async def test_call_tool(mock_forecast_response):
    class Response():
        def raise_for_status(self):
            pass

        def json(self):
            return mock_forecast_response

    class AsyncClient():
        async def __aenter__(self):
            return self

        async def __aexit__(self, *exc_info):
            pass

        async def get(self, *args, **kwargs):
            return Response()

    with patch('httpx.AsyncClient', new=AsyncClient) as mock_client:
        result = await call_tool("get_forecast", {"city": "London", "days": 2})

        assert len(result) == 1
        assert result[0].type == "text"
        forecast_data = json.loads(result[0].text)
        assert len(forecast_data) == 1
        assert forecast_data[0]["temperature"] == 18.5
        assert forecast_data[0]["conditions"] == "sunny"

@pytest.mark.anyio
async def test_list_resources():
    resources = await list_resources()
    assert len(resources) == 1
    assert resources[0].name == f"Current weather in {DEFAULT_CITY}"
    assert resources[0].mimeType == "application/json"

@pytest.mark.anyio
async def test_list_tools():
    tools = await list_tools()
    assert len(tools) == 1
    assert tools[0].name == "get_forecast"
    assert "city" in tools[0].inputSchema["properties"]
```

2. Run tests

```bash
uv add --dev pytest
uv run pytest
```

### Troubleshooting

#### Installation issues

```python
# Check Python version
python --version

# Reinstall dependencies
uv sync --reinstall
```

#### Type checking

```python
# Install mypy
uv add --dev pyright

# Run type checker
uv run pyright src
```

# Concepts

## Core architecture

Understand how MCP connects clients, servers, and LLMs

The Model Context Protocol (MCP) is built on a flexible, extensible architecture that enables seamless communication between LLM applications and integrations. This document covers the core architectural components and concepts.

### Overview

MCP follows a client-server architecture where:

- **Hosts** are LLM applications (like Claude Desktop or IDEs) that initiate connections
- **Clients** maintain 1:1 connections with servers, inside the host application
- **Servers** provide context, tools, and prompts to clients

## Core components

### Protocol layer

The protocol layer handles message framing, request/response linking, and high-level communication patterns.

**TypeScript:**

```typescript
class Protocol<Request, Notification, Result> {
  // Handle incoming requests
  setRequestHandler<T>(
    schema: T,
    handler: (request: T, extra: RequestHandlerExtra) => Promise<Result>
  ): void;

  // Handle incoming notifications
  setNotificationHandler<T>(
    schema: T,
    handler: (notification: T) => Promise<void>
  ): void;

  // Send requests and await responses
  request<T>(request: Request, schema: T, options?: RequestOptions): Promise<T>;

  // Send one-way notifications
  notification(notification: Notification): Promise<void>;
}
```

**Python:**

```python
class Session(BaseSession[RequestT, NotificationT, ResultT]):
    async def send_request(
        self,
        request: RequestT,
        result_type: type[Result]
    ) -> Result:
        """
        Send request and wait for response. Raises McpError if response contains error.
        """
        # Request handling implementation

    async def send_notification(
        self,
        notification: NotificationT
    ) -> None:
        """Send one-way notification that doesn't expect response."""
        # Notification handling implementation

    async def _received_request(
        self,
        responder: RequestResponder[ReceiveRequestT, ResultT]
    ) -> None:
        """Handle incoming request from other side."""
        # Request handling implementation

    async def _received_notification(
        self,
        notification: ReceiveNotificationT
    ) -> None:
        """Handle incoming notification from other side."""
        # Notification handling implementation
```

Key classes include:

- **Protocol**
- **Client**
- **Server**

### Transport layer

The transport layer handles the actual communication between clients and servers. MCP supports multiple transport mechanisms:

1. Stdio transport

   - Uses standard input/output for communication
   - Ideal for local processes

2. HTTP with SSE transport

   - Uses Server-Sent Events for server-to-client messages
   - HTTP POST for client-to-server messages

All transports use JSON-RPC 2.0 to exchange messages. See the specification for detailed information about the Model Context Protocol message format.

### Message types

MCP has these main types of messages:

1. **Requests** expect a response from the other side:

```typescript
interface Request {
  method: string;
  params?: { ... };
}
```

2. **Notifications** are one-way messages that don’t expect a response:

```typescript
interface Notification {
  method: string;
  params?: { ... };
}
```

3. **Results** are successful responses to requests:

```typescript
interface Result {
  [key: string]: unknown;
}
```

4. **Errors** indicate that a request failed:

```typescript
interface Error {
  code: number;
  message: string;
  data?: unknown;
}
```

## Connection lifecycle

1. Initialization

   1. Client sends `initialize` request with protocol version and capabilities
   2. Server responds with its protocol version and capabilities
   3. Client sends `initialized` notification as acknowledgment
   4. Normal message exchange begins

2. Message exchange

After initialization, the following patterns are supported:

    - **Request-Response:** Client or server sends requests, the other responds
    - **Notifications:** Either party sends one-way messages

3. Termination

Either party can terminate the connection:

- Clean shutdown via close()
- Transport disconnection
- Error conditions

## Error handling

MCP defines these standard error codes:

```typescript
enum ErrorCode {
  // Standard JSON-RPC error codes
  ParseError = -32700,
  InvalidRequest = -32600,
  MethodNotFound = -32601,
  InvalidParams = -32602,
  InternalError = -32603,
}
```

SDKs and applications can define their own error codes above -32000.

Errors are propagated through:

- Error responses to requests
- Error events on transports
- Protocol-level error handlers

## Implementation example

Here’s a basic example of implementing an MCP server:

**TypeScript:**

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  {
    name: "example-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      resources: {},
    },
  }
);

// Handle requests
server.setRequestHandler(ListResourcesRequestSchema, async () => {
  return {
    resources: [
      {
        uri: "example://resource",
        name: "Example Resource",
      },
    ],
  };
});

// Connect transport
const transport = new StdioServerTransport();
await server.connect(transport);
```

**Python:**

```python
import asyncio
import mcp.types as types
from mcp.server import Server
from mcp.server.stdio import stdio_server

app = Server("example-server")

@app.list_resources()
async def list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri="example://resource",
            name="Example Resource"
        )
    ]

async def main():
    async with stdio_server() as streams:
        await app.run(
            streams[0],
            streams[1],
            app.create_initialization_options()
        )

if __name__ == "__main__":
    asyncio.run(main)
```

## Best practices

### Transport selection

1. Local communication

   - Use stdio transport for local processes
   - Efficient for same-machine communication
   - Simple process management

2. Remote communication

   - Use SSE for scenarios requiring HTTP compatibility
   - Consider security implications including authentication and authorization

### Message handling

1. Request processing

   - Validate inputs thoroughly
   - Use type-safe schemas
   - Handle errors gracefully
   - Implement timeouts

2. Progress reporting

   - Use progress tokens for long operations
   - Report progress incrementally
   - Include total progress when known

3. Error management

   - Use appropriate error codes
   - Include helpful error messages
   - Clean up resources on errors

## Security considerations

1. Transport security

   - Use TLS for remote connections
   - Validate connection origins
   - Implement authentication when needed

2. Message validation

   - Validate all incoming messages
   - Sanitize inputs
   - Check message size limits
   - Verify JSON-RPC format

3. Resource protection

   - Implement access controls
   - Validate resource paths
   - Monitor resource usage
   - Rate limit requests

4. Error handling

   - Don’t leak sensitive information
   - Log security-relevant errors
   - Implement proper cleanup
   - Handle DoS scenarios

## Debugging and monitoring

1. Logging

   - Log protocol events
   - Track message flow
   - Monitor performance
   - Record errors

2. Diagnostics

   - Implement health checks
   - Monitor connection state
   - Track resource usage
   - Profile performance

3. Testing

   - Test different transports
   - Verify error handling
   - Check edge cases
   - Load test servers

# Resources

Expose data and content from your servers to LLMs

Resources are a core primitive in the Model Context Protocol (MCP) that allow servers to expose data and content that can be read by clients and used as context for LLM interactions.

Resources are designed to be **application-controlled**, meaning that the client application can decide how and when they should be used. Different MCP clients may handle resources differently. For example:

- Claude Desktop currently requires users to explicitly select resources before they can be used
- Other clients might automatically select resources based on heuristics
- Some implementations may even allow the AI model itself to determine which resources to use

Server authors should be prepared to handle any of these interaction patterns when implementing resource support. In order to expose data to models automatically, server authors should use a **model-controlled** primitive such as **Tools**.

## Overview

Resources represent any kind of data that an MCP server wants to make available to clients. This can include:

- File contents
- Database records
- API responses
- Live system data
- Screenshots and images
- Log files
- And more

Each resource is identified by a unique URI and can contain either text or binary data.

## Resource URIs

Resources are identified using URIs that follow this format:

```bash
[protocol]://[host]/[path]
```

For example:

- `file:///home/user/documents/report.pdf`
- `postgres://database/customers/schema`
- `screen://localhost/display1`

The protocol and path structure is defined by the MCP server implementation. Servers can define their own custom URI schemes.

## Resource types

Resources can contain two types of content:

### Text resources

Text resources contain UTF-8 encoded text data. These are suitable for:

- Source code
- Configuration files
- Log files
- JSON/XML data
- Plain text

### Binary resources

Binary resources contain raw binary data encoded in base64. These are suitable for:

- Images
- PDFs
- Audio files
- Video files
- Other non-text formats

## Resource discovery

Clients can discover available resources through two main methods:

### Direct resources

Servers expose a list of concrete resources via the `resources/list` endpoint. Each resource includes:

```typescript
{
  uri: string;           // Unique identifier for the resource
  name: string;          // Human-readable name
  description?: string;  // Optional description
  mimeType?: string;     // Optional MIME type
}
```

### Resource templates

For dynamic resources, servers can expose **URI templates** that clients can use to construct valid resource URIs:

```typescript
{
  uriTemplate: string;   // URI template following RFC 6570
  name: string;          // Human-readable name for this type
  description?: string;  // Optional description
  mimeType?: string;     // Optional MIME type for all matching resources
}
```

## Reading resources

To read a resource, clients make a resources/read request with the resource URI.

The server responds with a list of resource contents:

```typescript
{
  contents: [
    {
      uri: string;        // The URI of the resource
      mimeType?: string;  // Optional MIME type

      // One of:
      text?: string;      // For text resources
      blob?: string;      // For binary resources (base64 encoded)
    }
  ]
}
```

_Servers may return multiple resources in response to one resources/read request. This could be used, for example, to return a list of files inside a directory when the directory is read._

## Resource updates

MCP supports real-time updates for resources through two mechanisms:

### List changes

Servers can notify clients when their list of available resources changes via the `notifications/resources/list_changed` notification.

### Content changes

Clients can subscribe to updates for specific resources:

1. Client sends resources/subscribe with resource URI
2. Server sends notifications/resources/updated when the resource changes
3. Client can fetch latest content with resources/read
4. Client can unsubscribe with resources/unsubscribe

## Example implementation

Here’s a simple example of implementing resource support in an MCP server:

**TypeScript:**

```typescript
const server = new Server(
  {
    name: "example-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      resources: {},
    },
  }
);

// List available resources
server.setRequestHandler(ListResourcesRequestSchema, async () => {
  return {
    resources: [
      {
        uri: "file:///logs/app.log",
        name: "Application Logs",
        mimeType: "text/plain",
      },
    ],
  };
});

// Read resource contents
server.setRequestHandler(ReadResourceRequestSchema, async (request) => {
  const uri = request.params.uri;

  if (uri === "file:///logs/app.log") {
    const logContents = await readLogFile();
    return {
      contents: [
        {
          uri,
          mimeType: "text/plain",
          text: logContents,
        },
      ],
    };
  }

  throw new Error("Resource not found");
});
```

**Python:**

```python
app = Server("example-server")

@app.list_resources()
async def list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri="file:///logs/app.log",
            name="Application Logs",
            mimeType="text/plain"
        )
    ]

@app.read_resource()
async def read_resource(uri: AnyUrl) -> str:
    if str(uri) == "file:///logs/app.log":
        log_contents = await read_log_file()
        return log_contents

    raise ValueError("Resource not found")

# Start server
async with stdio_server() as streams:
    await app.run(
        streams[0],
        streams[1],
        app.create_initialization_options()
    )
```

## Best practices

When implementing resource support:

1. Use clear, descriptive resource names and URIs
2. Include helpful descriptions to guide LLM understanding
3. Set appropriate MIME types when known
4. Implement resource templates for dynamic content
5. Use subscriptions for frequently changing resources
6. Handle errors gracefully with clear error messages
7. Consider pagination for large resource lists
8. Cache resource contents when appropriate
9. Validate URIs before processing
10. Document your custom URI schemes

## Security considerations

When exposing resources:

- Validate all resource URIs
- Implement appropriate access controls
- Sanitize file paths to prevent directory traversal
- Be cautious with binary data handling
- Consider rate limiting for resource reads
- Audit resource access
- Encrypt sensitive data in transit
- Validate MIME types
- Implement timeouts for long-running reads
- Handle resource cleanup appropriately

# Prompts

Create reusable prompt templates and workflows

Prompts enable servers to define reusable prompt templates and workflows that clients can easily surface to users and LLMs. They provide a powerful way to standardize and share common LLM interactions.

_Prompts are designed to be user-controlled, meaning they are exposed from servers to clients with the intention of the user being able to explicitly select them for use._

## Overview

Prompts in MCP are predefined templates that can:

- Accept dynamic arguments
- Include context from resources
- Chain multiple interactions
- Guide specific workflows
- Surface as UI elements (like slash commands)

## Prompt structure

Each prompt is defined with:

```typescript
{
  name: string;              // Unique identifier for the prompt
  description?: string;      // Human-readable description
  arguments?: [              // Optional list of arguments
    {
      name: string;          // Argument identifier
      description?: string;  // Argument description
      required?: boolean;    // Whether argument is required
    }
  ]
}
```

## Discovering prompts

Clients can discover available prompts through the prompts/list endpoint:

```typescript
// Request
{
  method: "prompts/list";
}

// Response
{
  prompts: [
    {
      name: "analyze-code",
      description: "Analyze code for potential improvements",
      arguments: [
        {
          name: "language",
          description: "Programming language",
          required: true,
        },
      ],
    },
  ];
}
```

## Using prompts

To use a prompt, clients make a prompts/get request:

````typescript
​// Request
{
  method: "prompts/get",
  params: {
    name: "analyze-code",
    arguments: {
      language: "python"
    }
  }
}

// Response
{
  description: "Analyze Python code for potential improvements",
  messages: [
    {
      role: "user",
      content: {
        type: "text",
        text: "Please analyze the following Python code for potential improvements:\n\n```python\ndef calculate_sum(numbers):\n    total = 0\n    for num in numbers:\n        total = total + num\n    return total\n\nresult = calculate_sum([1, 2, 3, 4, 5])\nprint(result)\n```"
      }
    }
  ]
}
````

## Dynamic prompts

Prompts can be dynamic and include:

### Embedded resource context

```typescript
{
  "name": "analyze-project",
  "description": "Analyze project logs and code",
  "arguments": [
    {
      "name": "timeframe",
      "description": "Time period to analyze logs",
      "required": true
    },
    {
      "name": "fileUri",
      "description": "URI of code file to review",
      "required": true
    }
  ]
}
```

When handling the `prompts/get` request:

```typescript
{
  "messages": [
    {
      "role": "user",
      "content": {
        "type": "text",
        "text": "Analyze these system logs and the code file for any issues:"
      }
    },
    {
      "role": "user",
      "content": {
        "type": "resource",
        "resource": {
          "uri": "logs://recent?timeframe=1h",
          "text": "[2024-03-14 15:32:11] ERROR: Connection timeout in network.py:127\n[2024-03-14 15:32:15] WARN: Retrying connection (attempt 2/3)\n[2024-03-14 15:32:20] ERROR: Max retries exceeded",
          "mimeType": "text/plain"
        }
      }
    },
    {
      "role": "user",
      "content": {
        "type": "resource",
        "resource": {
          "uri": "file:///path/to/code.py",
          "text": "def connect_to_service(timeout=30):\n    retries = 3\n    for attempt in range(retries):\n        try:\n            return establish_connection(timeout)\n        except TimeoutError:\n            if attempt == retries - 1:\n                raise\n            time.sleep(5)\n\ndef establish_connection(timeout):\n    # Connection implementation\n    pass",
          "mimeType": "text/x-python"
        }
      }
    }
  ]
}
```

### Multi-step workflows

```typescript
const debugWorkflow = {
  name: "debug-error",
  async getMessages(error: string) {
    return [
      {
        role: "user",
        content: {
          type: "text",
          text: `Here's an error I'm seeing: ${error}`,
        },
      },
      {
        role: "assistant",
        content: {
          type: "text",
          text: "I'll help analyze this error. What have you tried so far?",
        },
      },
      {
        role: "user",
        content: {
          type: "text",
          text: "I've tried restarting the service, but the error persists.",
        },
      },
    ];
  },
};
```

## Example implementation

Here’s a complete example of implementing prompts in an MCP server:

**TypeScript:**

```typescript
import { Server } from "@modelcontextprotocol/sdk/server";
import {
  ListPromptsRequestSchema,
  GetPromptRequestSchema,
} from "@modelcontextprotocol/sdk/types";

const PROMPTS = {
  "git-commit": {
    name: "git-commit",
    description: "Generate a Git commit message",
    arguments: [
      {
        name: "changes",
        description: "Git diff or description of changes",
        required: true,
      },
    ],
  },
  "explain-code": {
    name: "explain-code",
    description: "Explain how code works",
    arguments: [
      {
        name: "code",
        description: "Code to explain",
        required: true,
      },
      {
        name: "language",
        description: "Programming language",
        required: false,
      },
    ],
  },
};

const server = new Server(
  {
    name: "example-prompts-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      prompts: {},
    },
  }
);

// List available prompts
server.setRequestHandler(ListPromptsRequestSchema, async () => {
  return {
    prompts: Object.values(PROMPTS),
  };
});

// Get specific prompt
server.setRequestHandler(GetPromptRequestSchema, async (request) => {
  const prompt = PROMPTS[request.params.name];
  if (!prompt) {
    throw new Error(`Prompt not found: ${request.params.name}`);
  }

  if (request.params.name === "git-commit") {
    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `Generate a concise but descriptive commit message for these changes:\n\n${request.params.arguments?.changes}`,
          },
        },
      ],
    };
  }

  if (request.params.name === "explain-code") {
    const language = request.params.arguments?.language || "Unknown";
    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `Explain how this ${language} code works:\n\n${request.params.arguments?.code}`,
          },
        },
      ],
    };
  }

  throw new Error("Prompt implementation not found");
});
```

**Python:**

```python
from mcp.server import Server
import mcp.types as types

# Define available prompts
PROMPTS = {
    "git-commit": types.Prompt(
        name="git-commit",
        description="Generate a Git commit message",
        arguments=[
            types.PromptArgument(
                name="changes",
                description="Git diff or description of changes",
                required=True
            )
        ],
    ),
    "explain-code": types.Prompt(
        name="explain-code",
        description="Explain how code works",
        arguments=[
            types.PromptArgument(
                name="code",
                description="Code to explain",
                required=True
            ),
            types.PromptArgument(
                name="language",
                description="Programming language",
                required=False
            )
        ],
    )
}

# Initialize server
app = Server("example-prompts-server")

@app.list_prompts()
async def list_prompts() -> list[types.Prompt]:
    return list(PROMPTS.values())

@app.get_prompt()
async def get_prompt(
    name: str, arguments: dict[str, str] | None = None
) -> types.GetPromptResult:
    if name not in PROMPTS:
        raise ValueError(f"Prompt not found: {name}")

    if name == "git-commit":
        changes = arguments.get("changes") if arguments else ""
        return types.GetPromptResult(
            messages=[
                types.PromptMessage(
                    role="user",
                    content=types.TextContent(
                        type="text",
                        text=f"Generate a concise but descriptive commit message "
                        f"for these changes:\n\n{changes}"
                    )
                )
            ]
        )

    if name == "explain-code":
        code = arguments.get("code") if arguments else ""
        language = arguments.get("language", "Unknown") if arguments else "Unknown"
        return types.GetPromptResult(
            messages=[
                types.PromptMessage(
                    role="user",
                    content=types.TextContent(
                        type="text",
                        text=f"Explain how this {language} code works:\n\n{code}"
                    )
                )
            ]
        )

    raise ValueError("Prompt implementation not found")
```

## Best practices

When implementing prompts:

1. Use clear, descriptive prompt names
2. Provide detailed descriptions for prompts and arguments
3. Validate all required arguments
4. Handle missing arguments gracefully
5. Consider versioning for prompt templates
6. Cache dynamic content when appropriate
7. Implement error handling
8. Document expected argument formats
9. Consider prompt composability
10. Test prompts with various inputs

## UI integration

Prompts can be surfaced in client UIs as:

- Slash commands
- Quick actions
- Context menu items
- Command palette entries
- Guided workflows
- Interactive forms

## Updates and changes

Servers can notify clients about prompt changes:

1. Server capability: `prompts.listChanged`
2. Notification: `notifications/prompts/list_changed`
3. Client re-fetches prompt list

## Security considerations

When implementing prompts:

- Validate all arguments
- Sanitize user input
- Consider rate limiting
- Implement access controls
- Audit prompt usage
- Handle sensitive data appropriately
- Validate generated content
- Implement timeouts
- Consider prompt injection risks
- Document security requirements

# Tools

Enable LLMs to perform actions through your server

Tools are a powerful primitive in the Model Context Protocol (MCP) that enable servers to expose executable functionality to clients. Through tools, LLMs can interact with external systems, perform computations, and take actions in the real world.

_Tools are designed to be model-controlled, meaning that tools are exposed from servers to clients with the intention of the AI model being able to automatically invoke them (with a human in the loop to grant approval)._

## Overview

Tools in MCP allow servers to expose executable functions that can be invoked by clients and used by LLMs to perform actions. Key aspects of tools include:

- **Discovery:** Clients can list available tools through the tools/list endpoint
- **Invocation:** Tools are called using the tools/call endpoint, where servers perform the requested operation and return results
- **Flexibility:** Tools can range from simple calculations to complex API interactions

Like resources, tools are identified by unique names and can include descriptions to guide their usage. However, unlike resources, tools represent dynamic operations that can modify state or interact with external systems.

## Tool definition structure

Each tool is defined with the following structure:

```typescript
{
  name: string;          // Unique identifier for the tool
  description?: string;  // Human-readable description
  inputSchema: {         // JSON Schema for the tool's parameters
    type: "object",
    properties: { ... }  // Tool-specific parameters
  }
}
```

### Implementing tools

Here’s an example of implementing a basic tool in an MCP server:

**TypeScript:**

```typescript
const server = new Server(
  {
    name: "example-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

// Define available tools
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "calculate_sum",
        description: "Add two numbers together",
        inputSchema: {
          type: "object",
          properties: {
            a: { type: "number" },
            b: { type: "number" },
          },
          required: ["a", "b"],
        },
      },
    ],
  };
});

// Handle tool execution
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "calculate_sum") {
    const { a, b } = request.params.arguments;
    return {
      toolResult: a + b,
    };
  }
  throw new Error("Tool not found");
});
```

**Python:**

```python
app = Server("example-server")

@app.list_tools()
async def list_tools() -> list[types.Tool]:
    return [
        types.Tool(
            name="calculate_sum",
            description="Add two numbers together",
            inputSchema={
                "type": "object",
                "properties": {
                    "a": {"type": "number"},
                    "b": {"type": "number"}
                },
                "required": ["a", "b"]
            }
        )
    ]

@app.call_tool()
async def call_tool(
    name: str,
    arguments: dict
) -> list[types.TextContent | types.ImageContent | types.EmbeddedResource]:
    if name == "calculate_sum":
        a = arguments["a"]
        b = arguments["b"]
        result = a + b
        return [types.TextContent(type="text", text=str(result))]
    raise ValueError(f"Tool not found: {name}")
```

## Example tool patterns

Here are some examples of types of tools that a server could provide:

### System operations

Tools that interact with the local system:

```typescript
{
  name: "execute_command",
  description: "Run a shell command",
  inputSchema: {
    type: "object",
    properties: {
      command: { type: "string" },
      args: { type: "array", items: { type: "string" } }
    }
  }
}
```

### API integrations

Tools that wrap external APIs:

```typescript
{
  name: "github_create_issue",
  description: "Create a GitHub issue",
  inputSchema: {
    type: "object",
    properties: {
      title: { type: "string" },
      body: { type: "string" },
      labels: { type: "array", items: { type: "string" } }
    }
  }
}
```

### Data processing

Tools that transform or analyze data:

```typescript
{
  name: "analyze_csv",
  description: "Analyze a CSV file",
  inputSchema: {
    type: "object",
    properties: {
      filepath: { type: "string" },
      operations: {
        type: "array",
        items: {
          enum: ["sum", "average", "count"]
        }
      }
    }
  }
}
```

## Best practices

When implementing tools:

1. Provide clear, descriptive names and descriptions
2. Use detailed JSON Schema definitions for parameters
3. Include examples in tool descriptions to demonstrate how the model should use them
4. Implement proper error handling and validation
5. Use progress reporting for long operations
6. Keep tool operations focused and atomic
7. Document expected return value structures
8. Implement proper timeouts
9. Consider rate limiting for resource-intensive operations
10. Log tool usage for debugging and monitoring

## Security considerations

When exposing tools:

### Input validation

- Validate all parameters against the schema
- Sanitize file paths and system commands
- Validate URLs and external identifiers
- Check parameter sizes and ranges
- Prevent command injection

### Access control

- Implement authentication where needed
- Use appropriate authorization checks
- Audit tool usage
- Rate limit requests
- Monitor for abuse

### Error handling

- Don’t expose internal errors to clients
- Log security-relevant errors
- Handle timeouts appropriately
- Clean up resources after errors
- Validate return values

## Tool discovery and updates

MCP supports dynamic tool discovery:

1. Clients can list available tools at any time
2. Servers can notify clients when tools change using `notifications/tools/list_changed`
3. Tools can be added or removed during runtime
4. Tool definitions can be updated (though this should be done carefully)

## Error handling

Tool errors should be reported within the result object, not as MCP protocol-level errors. This allows the LLM to see and potentially handle the error. When a tool encounters an error:

1. Set `isError` to `true` in the result
2. Include error details in the `content` array

Here’s an example of proper error handling for tools:

```typescript
try {
  // Tool operation
  const result = performOperation();
  return {
    content: [
      {
        type: "text",
        text: `Operation successful: ${result}`,
      },
    ],
  };
} catch (error) {
  return {
    isError: true,
    content: [
      {
        type: "text",
        text: `Error: ${error.message}`,
      },
    ],
  };
}
```

This approach allows the LLM to see that an error occurred and potentially take corrective action or request human intervention.

## Testing tools

A comprehensive testing strategy for MCP tools should cover:

- **Functional testing:** Verify tools execute correctly with valid inputs and handle invalid inputs appropriately
- **Integration testing:** Test tool interaction with external systems using both real and mocked dependencies
- **Security testing:** Validate authentication, authorization, input sanitization, and rate limiting
- **Performance testing:** Check behavior under load, timeout handling, and resource cleanup
- **Error handling:** Ensure tools properly report errors through the MCP protocol and clean up resources

# Sampling

Let your servers request completions from LLMs

Sampling is a powerful MCP feature that allows servers to request LLM completions through the client, enabling sophisticated agentic behaviors while maintaining security and privacy.

_This feature of MCP is not yet supported in the Claude Desktop client._

## How sampling works

The sampling flow follows these steps:

1. Server sends a `sampling/createMessage` request to the client
2. Client reviews the request and can modify it
3. Client samples from an LLM
4. Client reviews the completion
5. Client returns the result to the server

This human-in-the-loop design ensures users maintain control over what the LLM sees and generates.

## Message format

Sampling requests use a standardized message format:

```typescript
{
  messages: [
    {
      role: "user" | "assistant",
      content: {
        type: "text" | "image",

        // For text:
        text?: string,

        // For images:
        data?: string,             // base64 encoded
        mimeType?: string
      }
    }
  ],
  modelPreferences?: {
    hints?: [{
      name?: string                // Suggested model name/family
    }],
    costPriority?: number,         // 0-1, importance of minimizing cost
    speedPriority?: number,        // 0-1, importance of low latency
    intelligencePriority?: number  // 0-1, importance of capabilities
  },
  systemPrompt?: string,
  includeContext?: "none" | "thisServer" | "allServers",
  temperature?: number,
  maxTokens: number,
  stopSequences?: string[],
  metadata?: Record<string, unknown>
}
```

## Request parameters

### Messages

The `messages` array contains the conversation history to send to the LLM. Each message has:

- `role`: Either “user” or “assistant”
- `content`: The message content, which can be:
  - Text content with a `text` field
  - Image content with `data` (base64) and `mimeType` fields

### Model preferences

The `modelPreferences` object allows servers to specify their model selection preferences:

- `hints`: Array of model name suggestions that clients can use to select an appropriate model:

  - `name`: String that can match full or partial model names (e.g. “claude-3”, “sonnet”)
  - Clients may map hints to equivalent models from different providers
  - Multiple hints are evaluated in preference order

- Priority values (0-1 normalized):

  - `costPriority`: Importance of minimizing costs
  - `speedPriority`: Importance of low latency response
  - `intelligencePriority`: Importance of advanced model capabilities

Clients make the final model selection based on these preferences and their available models.

### System prompt

An optional `systemPrompt` field allows servers to request a specific system prompt. The client may modify or ignore this.

### Context inclusion

The `includeContext` parameter specifies what MCP context to include:

- "none": No additional context
- "thisServer": Include context from the requesting server
- "allServers": Include context from all connected MCP servers

The client controls what context is actually included.

### Sampling parameters

Fine-tune the LLM sampling with:

- `temperature`: Controls randomness (0.0 to 1.0)
- `maxTokens`: Maximum tokens to generate
- `stopSequences`: Array of sequences that stop generation
- `metadata`: Additional provider-specific parameters

## Response format

The client returns a completion result:

```typescript
{
  model: string,  // Name of the model used
  stopReason?: "endTurn" | "stopSequence" | "maxTokens" | string,
  role: "user" | "assistant",
  content: {
    type: "text" | "image",
    text?: string,
    data?: string,
    mimeType?: string
  }
}
```

## Example request

Here’s an example of requesting sampling from a client:

```typescript
{
  "method": "sampling/createMessage",
  "params": {
    "messages": [
      {
        "role": "user",
        "content": {
          "type": "text",
          "text": "What files are in the current directory?"
        }
      }
    ],
    "systemPrompt": "You are a helpful file system assistant.",
    "includeContext": "thisServer",
    "maxTokens": 100
  }
}
```

## Best practices

When implementing sampling:

1. Always provide clear, well-structured prompts
2. Handle both text and image content appropriately
3. Set reasonable token limits
4. Include relevant context through includeContext
5. Validate responses before using them
6. Handle errors gracefully
7. Consider rate limiting sampling requests
8. Document expected sampling behavior
9. Test with various model parameters
10. Monitor sampling costs

## Human in the loop controls

Sampling is designed with human oversight in mind:

### For prompts

- Clients should show users the proposed prompt
- Users should be able to modify or reject prompts
- System prompts can be filtered or modified
- Context inclusion is controlled by the client

### For completions

- Clients should show users the completion
- Users should be able to modify or reject completions
- Clients can filter or modify completions
- Users control which model is used

## Security considerations

When implementing sampling:

- Validate all message content
- Sanitize sensitive information
- Implement appropriate rate limits
- Monitor sampling usage
- Encrypt data in transit
- Handle user data privacy
- Audit sampling requests
- Control cost exposure
- Implement timeouts
- Handle model errors gracefully

## Common patterns

### Agentic workflows

Sampling enables agentic patterns like:

- Reading and analyzing resources
- Making decisions based on context
- Generating structured data
- Handling multi-step tasks
- Providing interactive assistance

### Context management

Best practices for context:

- Request minimal necessary context
- Structure context clearly
- Handle context size limits
- Update context as needed
- Clean up stale context

### Error handling

Robust error handling should:

- Catch sampling failures
- Handle timeout errors
- Manage rate limits
- Validate responses
- Provide fallback behaviors
- Log errors appropriately

## Limitations

Be aware of these limitations:

- Sampling depends on client capabilities
- Users control sampling behavior
- Context size has limits
- Rate limits may apply
- Costs should be considered
- Model availability varies
- Response times vary
- Not all content types supported

# Transports

Learn about MCP’s communication mechanisms

Transports in the Model Context Protocol (MCP) provide the foundation for communication between clients and servers. A transport handles the underlying mechanics of how messages are sent and received.

## Message Format

MCP uses JSON-RPC 2.0 as its wire format. The transport layer is responsible for converting MCP protocol messages into JSON-RPC format for transmission and converting received JSON-RPC messages back into MCP protocol messages.

There are three types of JSON-RPC messages used:

### Requests

```typescript
{
  jsonrpc: "2.0",
  id: number | string,
  method: string,
  params?: object
}
```

### Responses

```typescript
{
  jsonrpc: "2.0",
  id: number | string,
  result?: object,
  error?: {
    code: number,
    message: string,
    data?: unknown
  }
}
```

### Notifications

```typescript
{
  jsonrpc: "2.0",
  method: string,
  params?: object
}
```

## Built-in Transport Types

MCP includes two standard transport implementations:

### Standard Input/Output (stdio)

The stdio transport enables communication through standard input and output streams. This is particularly useful for local integrations and command-line tools.

Use stdio when:

- Building command-line tools
- Implementing local integrations
- Needing simple process communication
- Working with shell scripts

**TypeScript (Server):**

```typescript
const server = new Server(
  {
    name: "example-server",
    version: "1.0.0",
  },
  {
    capabilities: {},
  }
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

**TypeScript (Client):**

```typescript
const client = new Client(
  {
    name: "example-client",
    version: "1.0.0",
  },
  {
    capabilities: {},
  }
);

const transport = new StdioClientTransport({
  command: "./server",
  args: ["--option", "value"],
});
await client.connect(transport);
```

**Python (Server):**

```python
app = Server("example-server")

async with stdio_server() as streams:
    await app.run(
        streams[0],
        streams[1],
        app.create_initialization_options()
    )
```

**Python (Client):**

```python
params = StdioServerParameters(
    command="./server",
    args=["--option", "value"]
)

async with stdio_client(params) as streams:
    async with ClientSession(streams[0], streams[1]) as session:
        await session.initialize()
```

### Server-Sent Events (SSE)

SSE transport enables server-to-client streaming with HTTP POST requests for client-to-server communication.

Use SSE when:

- Only server-to-client streaming is needed
- Working with restricted networks
- Implementing simple updates

**TypeScript (Server):**

```typescript
const server = new Server(
  {
    name: "example-server",
    version: "1.0.0",
  },
  {
    capabilities: {},
  }
);

const transport = new SSEServerTransport("/message", response);
await server.connect(transport);
```

**TypeScript (Client):**

```typescript
const client = new Client(
  {
    name: "example-client",
    version: "1.0.0",
  },
  {
    capabilities: {},
  }
);

const transport = new SSEClientTransport(new URL("http://localhost:3000/sse"));
await client.connect(transport);
```

**Python (Server):**

```python
from mcp.server.sse import SseServerTransport
from starlette.applications import Starlette
from starlette.routing import Route

app = Server("example-server")
sse = SseServerTransport("/messages")

async def handle_sse(scope, receive, send):
    async with sse.connect_sse(scope, receive, send) as streams:
        await app.run(streams[0], streams[1], app.create_initialization_options())

async def handle_messages(scope, receive, send):
    await sse.handle_post_message(scope, receive, send)

starlette_app = Starlette(
    routes=[
        Route("/sse", endpoint=handle_sse),
        Route("/messages", endpoint=handle_messages, methods=["POST"]),
    ]
)
```

**Python (Client):**

```python
async with sse_client("http://localhost:8000/sse") as streams:
    async with ClientSession(streams[0], streams[1]) as session:
        await session.initialize()
```

## Custom Transports

MCP makes it easy to implement custom transports for specific needs. Any transport implementation just needs to conform to the Transport interface:

You can implement custom transports for:

- Custom network protocols
- Specialized communication channels
- Integration with existing systems
- Performance optimization

**TypeScript:**

```typescript
interface Transport {
  // Start processing messages
  start(): Promise<void>;

  // Send a JSON-RPC message
  send(message: JSONRPCMessage): Promise<void>;

  // Close the connection
  close(): Promise<void>;

  // Callbacks
  onclose?: () => void;
  onerror?: (error: Error) => void;
  onmessage?: (message: JSONRPCMessage) => void;
}
```

**Python:**

Note that while MCP Servers are often implemented with asyncio, we recommend implementing low-level interfaces like transports with `anyio` for wider compatibility.

```python
@contextmanager
async def create_transport(
    read_stream: MemoryObjectReceiveStream[JSONRPCMessage | Exception],
    write_stream: MemoryObjectSendStream[JSONRPCMessage]
):
    """
    Transport interface for MCP.

    Args:
        read_stream: Stream to read incoming messages from
        write_stream: Stream to write outgoing messages to
    """
    async with anyio.create_task_group() as tg:
        try:
            # Start processing messages
            tg.start_soon(lambda: process_messages(read_stream))

            # Send messages
            async with write_stream:
                yield write_stream

        except Exception as exc:
            # Handle errors
            raise exc
        finally:
            # Clean up
            tg.cancel_scope.cancel()
            await write_stream.aclose()
            await read_stream.aclose()
```

## Error Handling

Transport implementations should handle various error scenarios:

1. Connection errors
2. Message parsing errors
3. Protocol errors
4. Network timeouts
5. Resource cleanup

Example error handling:

**TypeScript:**

```typescript
class ExampleTransport implements Transport {
  async start() {
    try {
      // Connection logic
    } catch (error) {
      this.onerror?.(new Error(`Failed to connect: ${error}`));
      throw error;
    }
  }

  async send(message: JSONRPCMessage) {
    try {
      // Sending logic
    } catch (error) {
      this.onerror?.(new Error(`Failed to send message: ${error}`));
      throw error;
    }
  }
}
```

**Python:**

Note that while MCP Servers are often implemented with asyncio, we recommend implementing low-level interfaces like transports with `anyio` for wider compatibility.

```python
@contextmanager
async def example_transport(scope: Scope, receive: Receive, send: Send):
    try:
        # Create streams for bidirectional communication
        read_stream_writer, read_stream = anyio.create_memory_object_stream(0)
        write_stream, write_stream_reader = anyio.create_memory_object_stream(0)

        async def message_handler():
            try:
                async with read_stream_writer:
                    # Message handling logic
                    pass
            except Exception as exc:
                logger.error(f"Failed to handle message: {exc}")
                raise exc

        async with anyio.create_task_group() as tg:
            tg.start_soon(message_handler)
            try:
                # Yield streams for communication
                yield read_stream, write_stream
            except Exception as exc:
                logger.error(f"Transport error: {exc}")
                raise exc
            finally:
                tg.cancel_scope.cancel()
                await write_stream.aclose()
                await read_stream.aclose()
    except Exception as exc:
        logger.error(f"Failed to initialize transport: {exc}")
        raise exc
```

## Best Practices

When implementing or using MCP transport:

1. Handle connection lifecycle properly
2. Implement proper error handling
3. Clean up resources on connection close
4. Use appropriate timeouts
5. Validate messages before sending
6. Log transport events for debugging
7. Implement reconnection logic when appropriate
8. Handle backpressure in message queues
9. Monitor connection health
10. Implement proper security measures

## Security Considerations

When implementing transport:

### Authentication and Authorization

- Implement proper authentication mechanisms
- Validate client credentials
- Use secure token handling
- Implement authorization checks

### Data Security

- Use TLS for network transport
- Encrypt sensitive data
- Validate message integrity
- Implement message size limits
- Sanitize input data

### Network Security

- Implement rate limiting
- Use appropriate timeouts
- Handle denial of service scenarios
- Monitor for unusual patterns
- Implement proper firewall rules

## Debugging Transport

Tips for debugging transport issues:

1. Enable debug logging
2. Monitor message flow
3. Check connection states
4. Validate message formats
5. Test error scenarios
6. Use network analysis tools
7. Implement health checks
8. Monitor resource usage
9. Test edge cases
10. Use proper error tracking

# Developer Tools

## Debugging

A comprehensive guide to debugging Model Context Protocol (MCP) integrations

Effective debugging is essential when developing MCP servers or integrating them with applications. This guide covers the debugging tools and approaches available in the MCP ecosystem.

_This guide is for macOS. Guides for other platforms are coming soon._

### Debugging tools overview

MCP provides several tools for debugging at different levels:

1. MCP Inspector

   - Interactive debugging interface
   - Direct server testing
   - See the Inspector guide for details

2. Claude Desktop Developer Tools

   - Integration testing
   - Log collection
   - Chrome DevTools integration

3. Server Logging

   - Custom logging implementations
   - Error tracking
   - Performance monitoring

### Debugging in Claude Desktop

#### Checking server status

The Claude.app interface provides basic server status information:

1. Click the 🔌 icon to view:

   - Connected servers
   - Available prompts and resources

2. Click the 🔨 icon to view:

   - Tools made available to the model

#### Viewing logs

Review detailed MCP logs from Claude Desktop:

```bash
# Follow logs in real-time
tail -n 20 -f ~/Library/Logs/Claude/mcp\*.log
```

The logs capture:

- Server connection events
- Configuration issues
- Runtime errors
- Message exchanges

#### Using Chrome DevTools

Access Chrome’s developer tools inside Claude Desktop to investigate client-side errors:

1. Enable DevTools:

```bash
jq '.allowDevTools = true' ~/Library/Application\ Support/Claude/developer_settings.json > tmp.json \
 && mv tmp.json ~/Library/Application\ Support/Claude/developer_settings.json
```

2. Open DevTools: `Command-Option-Shift-i`

Note: You’ll see two DevTools windows:

- Main content window
- App title bar window

Use the Console panel to inspect client-side errors.

Use the Network panel to inspect:

- Message payloads
- Connection timing

#### Common issues

**Environment variables**

MCP servers inherit only a subset of environment variables automatically, like `USER`, `HOME`, and `PATH`.

To override the default variables or provide your own, you can specify an `env` key in `claude_desktop_config.json`:

```typescript
{
  "myserver": {
    "command": "mcp-server-myapp",
    "env": {
      "MYAPP_API_KEY": "some_key",
    }
  }
}
```

**Server initialization**

Common initialization problems:

1. Path Issues

   - Incorrect server executable path
   - Missing required files
   - Permission problems

2. Configuration Errors

   - Invalid JSON syntax
   - Missing required fields
   - Type mismatches

3. Environment Problems

   - Missing environment variables
   - Incorrect variable values
   - Permission restrictions

##### Connection problems

When servers fail to connect:

1. Check Claude Desktop logs
2. Verify server process is running
3. Test standalone with Inspector
4. Verify protocol compatibility

#### Implementing logging

**Server-side logging**

When building a server that uses the local stdio transport, all messages logged to stderr (standard error) will be captured by the host application (e.g., Claude Desktop) automatically.

_Local MCP servers should not log messages to stdout (standard out), as this will interfere with protocol operation._

For all transports, you can also provide logging to the client by sending a log message notification:

**TypeScript:**

```typescript
server.sendLoggingMessage({
  level: "info",
  data: "Server started successfully",
});
```

**Python:**

```python
server.request_context.session.send_log_message(
  level="info",
  data="Server started successfully",
)
```

Important events to log:

- Initialization steps
- Resource access
- Tool execution
- Error conditions
- Performance metrics

##### Client-side logging

In client applications:

- Enable debug logging
- Monitor network traffic
- Track message exchanges
- Record error states

#### Debugging workflow

**Development cycle**

1. Initial Development

   - Use Inspector for basic testing
   - Implement core functionality
   - Add logging points

2. Integration Testing

   - Test in Claude Desktop
   - Monitor logs
   - Check error handling

##### Testing changes

To test changes efficiently:

**Configuration changes:** Restart Claude Desktop
**Server code changes:** Use Command-R to reload
**Quick iteration:** Use Inspector during development

#### Best practices

**Logging strategy**

1. Structured Logging

   - Use consistent formats
   - Include context
   - Add timestamps
   - Track request IDs

2. Error Handling

   - Log stack traces
   - Include error context
   - Track error patterns
   - Monitor recovery

3. Performance Tracking

   - Log operation timing
   - Monitor resource usage
   - Track message sizes
   - Measure latency

##### Security considerations

When debugging:

1. Sensitive Data

   - Sanitize logs
   - Protect credentials
   - Mask personal information

2. Access Control

   - Verify permissions
   - Check authentication
   - Monitor access patterns

#### Getting help

When encountering issues:

1. First Steps

   - Check server logs
   - Test with Inspector
   - Review configuration
   - Verify environment

2. Support Channels

   - GitHub issues
   - GitHub discussions

3. Providing Information

   - Log excerpts
   - Configuration files
   - Steps to reproduce
   - Environment details

## Inspector

In-depth guide to using the MCP Inspector for testing and debugging Model Context Protocol servers

The MCP Inspector is an interactive developer tool for testing and debugging MCP servers. While the Debugging Guide covers the Inspector as part of the overall debugging toolkit, this document provides a detailed exploration of the Inspector’s features and capabilities.

### Getting started

#### Installation and basic usage

The Inspector runs directly through npx without requiring installation:

```bash
npx @modelcontextprotocol/inspector <command>
```

```bash
npx @modelcontextprotocol/inspector <command> <arg1> <arg2>
```

#### Inspecting servers from NPM or PyPi

A common way to start server packages from NPM or PyPi.

**NPM package:**

```bash
npx -y @modelcontextprotocol/inspector npx <package-name> <args>
# For example
npx -y @modelcontextprotocol/inspector npx server-postgres postgres://127.0.0.1/testdb
```

**PyPi package:**

```bash
npx @modelcontextprotocol/inspector uvx <package-name> <args>
# For example
npx @modelcontextprotocol/inspector uvx mcp-server-git --repository ~/code/mcp/servers.git
```

#### Inspecting locally developed servers

To inspect servers locally developed or downloaded as a repository, the most common way is:

**TypeScript:**

```bash
npx @modelcontextprotocol/inspector node path/to/server/index.js args...
```

**Python:**

```bash
npx @modelcontextprotocol/inspector \
  uv \
  --directory path/to/server \
  run \
  package-name \
  args...
```

Please carefully read any attached README for the most accurate instructions.

### Feature overview

The Inspector provides several features for interacting with your MCP server:

#### Server connection pane

- Allows selecting the transport for connecting to the server
- For local servers, supports customizing the command-line arguments and environment

#### Resources tab

- Lists all available resources
- Shows resource metadata (MIME types, descriptions)
- Allows resource content inspection
- Supports subscription testing

#### Prompts tab

- Displays available prompt templates
- Shows prompt arguments and descriptions
- Enables prompt testing with custom arguments
- Previews generated messages

#### Tools tab

- Lists available tools
- Shows tool schemas and descriptions
- Enables tool testing with custom inputs
- Displays tool execution results

#### Notifications pane

- Presents all logs recorded from the server
- Shows notifications received from the server

### Best practices

#### Development workflow

1. Start Development

   - Launch Inspector with your server
   - Verify basic connectivity
   - Check capability negotiation

2. Iterative testing

   - Make server changes
   - Rebuild the server
   - Reconnect the Inspector
   - Test affected features
   - Monitor messages

3. Test edge cases

   - Invalid inputs
   - Missing prompt arguments
   - Concurrent operations
   - Verify error handling and error responses

# MCP TypeScript SDK

TypeScript implementation of the Model Context Protocol (MCP), providing both client and server capabilities for integrating with LLM surfaces.

## Overview

The Model Context Protocol allows applications to provide context for LLMs in a standardized way, separating the concerns of providing context from the actual LLM interaction. This TypeScript SDK implements the full MCP specification, making it easy to:

- Build MCP clients that can connect to any MCP server
- Create MCP servers that expose resources, prompts and tools
- Use standard transports like stdio and SSE
- Handle all MCP protocol messages and lifecycle events

## Installation

```bash
npm install @modelcontextprotocol/sdk
```

## Quick Start

### Creating a Client

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

const transport = new StdioClientTransport({
  command: "path/to/server",
});

const client = new Client(
  {
    name: "example-client",
    version: "1.0.0",
  },
  {
    capabilities: {},
  }
);

await client.connect(transport);

// List available resources
const resources = await client.request(
  { method: "resources/list" },
  ListResourcesResultSchema
);

// Read a specific resource
const resourceContent = await client.request(
  {
    method: "resources/read",
    params: {
      uri: "file:///example.txt",
    },
  },
  ReadResourceResultSchema
);
```

### Creating a Server

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  {
    name: "example-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      resources: {},
    },
  }
);

server.setRequestHandler(ListResourcesRequestSchema, async () => {
  return {
    resources: [
      {
        uri: "file:///example.txt",
        name: "Example Resource",
      },
    ],
  };
});

server.setRequestHandler(ReadResourceRequestSchema, async (request) => {
  if (request.params.uri === "file:///example.txt") {
    return {
      contents: [
        {
          uri: "file:///example.txt",
          mimeType: "text/plain",
          text: "This is the content of the example resource.",
        },
      ],
    };
  } else {
    throw new Error("Resource not found");
  }
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

# MCP Python SDK

Python implementation of the Model Context Protocol (MCP), providing both client and server capabilities for integrating with LLM surfaces.

## Overview

The Model Context Protocol allows applications to provide context for LLMs in a standardized way, separating the concerns of providing context from the actual LLM interaction. This Python SDK implements the full MCP specification, making it easy to:

- Build MCP clients that can connect to any MCP server
- Create MCP servers that expose resources, prompts and tools
- Use standard transports like stdio and SSE
- Handle all MCP protocol messages and lifecycle events

## Installation

We recommend the use of uv to manage your Python projects:

```bash
uv add mcp
```

Alternatively, add mcp to your `requirements.txt`:

```bash
pip install mcp
# or add to requirements.txt
pip install -r requirements.txt
```

## Overview

MCP servers provide focused functionality like resources, tools, prompts, and other capabilities that can be reused across many client applications. These servers are designed to be easy to build, highly composable, and modular.

### Key design principles

Servers are extremely easy to build with clear, simple interfaces
Multiple servers can be composed seamlessly through a shared protocol
Each server operates in isolation and cannot access conversation context
Features can be added progressively through capability negotiation

#### Server provided primitives

Prompts: Templatable text
Resources: File-like attachments
Tools: Functions that models can call
Utilities:
Completion: Auto-completion provider for prompt arguments or resource URI templates
Logging: Logging to the client
Pagination\*: Pagination for long results

#### Client provided primitives

Sampling: Allow servers to sample using client models
Roots: Information about locations to operate on (e.g., directories)

Connections between clients and servers are established through transports like stdio or SSE (Note that most clients support stdio, but not SSE at the moment). The transport layer handles message framing, delivery, and error handling.

### Quick Start

#### Creating a Server

MCP servers follow a decorator approach to register handlers for MCP primitives like resources, prompts, and tools. The goal is to provide a simple interface for exposing capabilities to LLM clients.

`example_server.py`

```python
# /// script
# dependencies = [
#   "mcp"
# ]
# ///
from mcp.server import Server, NotificationOptions
from mcp.server.models import InitializationOptions
import mcp.server.stdio
import mcp.types as types

# Create a server instance
server = Server("example-server")

# Add prompt capabilities
@server.list_prompts()
async def handle_list_prompts() -> list[types.Prompt]:
    return [
        types.Prompt(
            name="example-prompt",
            description="An example prompt template",
            arguments=[
                types.PromptArgument(
                    name="arg1",
                    description="Example argument",
                    required=True
                )
            ]
        )
    ]

@server.get_prompt()
async def handle_get_prompt(
    name: str,
    arguments: dict[str, str] | None
) -> types.GetPromptResult:
    if name != "example-prompt":
        raise ValueError(f"Unknown prompt: {name}")

    return types.GetPromptResult(
        description="Example prompt",
        messages=[
            types.PromptMessage(
                role="user",
                content=types.TextContent(
                    type="text",
                    text="Example prompt text"
                )
            )
        ]
    )

async def run():
    # Run the server as STDIO
    async with mcp.server.stdio.stdio_server() as (read_stream, write_stream):
        await server.run(
            read_stream,
            write_stream,
            InitializationOptions(
                server_name="example",
                server_version="0.1.0",
                capabilities=server.get_capabilities(
                    notification_options=NotificationOptions(),
                    experimental_capabilities={},
                )
            )
        )

if __name__ == "__main__":
    import asyncio
    asyncio.run(run())
```

#### Creating a Client

`example_client.py`

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

# Create server parameters for stdio connection
server_params = StdioServerParameters(
    command="python", # Executable
    args=["example_server.py"], # Optional command line arguments
    env=None # Optional environment variables
)

async def run():
    async with stdio_client(server_params) as (read, write):
        async with ClientSession(read, write) as session:
            # Initialize the connection
            await session.initialize()

            # The example server only supports prompt primitives:

            # List available prompts
            prompts = await session.list_prompts()

            # Get a prompt
            prompt = await session.get_prompt("example-prompt", arguments={"arg1": "value"})

            """
            Other example calls include:

            # List available resources
            resources = await session.list_resources()

            # List available tools
            tools = await session.list_tools()

            # Read a resource
            resource = await session.read_resource("file://some/path")

            # Call a tool
            result = await session.call_tool("tool-name", arguments={"arg1": "value"})
            """

if __name__ == "__main__":
    import asyncio
    asyncio.run(run())
```

## Primitives

The MCP Python SDK provides decorators that map to the core protocol primitives. Each primitive follows a different interaction pattern based on how it is controlled and used:

- Prompts: User-controlled; Interactive templates invoked by user choice; Slash commands, menu options
- Resources: Application-controlled; Contextual data managed by the client application; File contents, API responses
- Tools: Model-controlled; Functions exposed to the LLM to take actions; API calls, data updates

### User-Controlled Primitives

Prompts are designed to be explicitly selected by users for their interactions with LLMs.

Decorators:

- `@server.list_prompts()`: List available prompt templates
- `@server.get_prompt()`: Get a specific prompt with arguments

### Application-Controlled Primitives

Resources are controlled by the client application, which decides how and when they should be used based on its own logic.

Decorators:

- `@server.list_resources()`: List available resources
- `@server.read_resource()`: Read a specific resource's content
- `@server.subscribe_resource()`: Subscribe to resource updates

### Model-Controlled Primitives

Tools are exposed to LLMs to enable automated actions, with user approval.

Decorators:

- `@server.list_tools()`: List available tools
- `@server.call_tool()`: Execute a tool with arguments

### Server Management

Additional decorators for server functionality:

Decorators:

- `@server.set_logging_level()`: Update server logging level

### Capabilities

MCP servers declare capabilities during initialization. These map to specific decorators:

- `prompts`: `listChanged`; `@list_prompts`, `@get_prompt`; Prompt template management
- `resources`: `subscribe`, `listChanged`; `@list_resources`, `@read_resource`, `@subscribe_resource`; Resource exposure and updates
- `tools`: `listChanged`; `@list_tools`, `@call_tool`; Tool discovery and execution
- `logging`:m`@set_logging_level`; Server logging configuration
- `completion`: `@complete_argument`; Argument completion suggestions

Capabilities are negotiated during connection initialization. Servers only need to implement the decorators for capabilities they support.

## Client Interaction

The MCP Python SDK enables servers to interact with clients through request context and session management. This allows servers to perform operations like LLM sampling and progress tracking.

### Request Context

The Request Context provides access to the current request and client session. It can be accessed through server.request_context and enables:

- Sampling from the client's LLM
- Sending progress updates
- Logging messages
- Accessing request metadata

Example using request context for LLM sampling:

```python
@server.call_tool()
async def handle_call_tool(name: str, arguments: dict) -> list[types.TextContent]:
    # Access the current request context
    context = server.request_context

    # Use the session to sample from the client's LLM
    result = await context.session.create_message(
        messages=[
            types.SamplingMessage(
                role="user",
                content=types.TextContent(
                    type="text",
                    text="Analyze this data: " + json.dumps(arguments)
                )
            )
        ],
        max_tokens=100
    )

    return [types.TextContent(type="text", text=result.content.text)]
```

Using request context for progress updates:

```python
@server.call_tool()
async def handle_call_tool(name: str, arguments: dict) -> list[types.TextContent]:
    context = server.request_context

    if progress_token := context.meta.progressToken:
        # Send progress notifications
        await context.session.send_progress_notification(
            progress_token=progress_token,
            progress=0.5,
            total=1.0
        )

    # Perform operation...

    if progress_token:
        await context.session.send_progress_notification(
            progress_token=progress_token,
            progress=1.0,
            total=1.0
        )

    return [types.TextContent(type="text", text="Operation complete")]
```

The request context is automatically set for each request and provides a safe way to access the current client session and request metadata.
