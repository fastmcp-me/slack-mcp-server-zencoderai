# slack-mcp-server
## Disclaimer
This project includes [code](https://github.com/modelcontextprotocol/servers-archived/tree/main/src/slack) originally developed by Anthropic and released under the MIT License. Substantial modifications and new functionality have been added by For Good AI Inc. (dba Zencoder Inc.), and are licensed under the Apache License, Version 2.0.

## Overview
A Model Context Protocol (MCP) server for interacting with Slack workspaces. This server provides tools to list channels, post messages, reply to threads, add reactions, get channel history, and manage users.

## Features

- **Multiple Transport Support**: Supports both stdio and Streamable HTTP transports
- **Modern MCP SDK**: Updated to use the latest MCP SDK (v1.13.2) with modern APIs
- **Comprehensive Slack Integration**: Full set of Slack operations including:
  - List channels (with predefined channel support)
  - Post messages
  - Reply to threads
  - Add reactions
  - Get channel history
  - Get thread replies
  - List users
  - Get user profiles

## Installation

### Local Development
```bash
npm install
npm run build
```

### Global Installation (NPM)
```bash
npm install -g @zencoderai/slack-mcp-server
```

### Docker Installation
```bash
# Build the Docker image
docker build -t slack-mcp-server .

# Or pull from registry
docker pull zencoderai/slack-mcp
```

## Configuration

Set the following environment variables:

```bash
export SLACK_BOT_TOKEN="xoxb-your-bot-token"
export SLACK_TEAM_ID="your-team-id"
export SLACK_CHANNEL_IDS="channel1,channel2,channel3"  # Optional: predefined channels
export AUTH_TOKEN="your-auth-token"  # Optional: Bearer token for HTTP authorization (Streamable HTTP transport only)
```

## Usage

### Command Line Options

```bash
slack-mcp [options]

Options:
  --transport <type>     Transport type: 'stdio' or 'http' (default: stdio)
  --port <number>        Port for HTTP server when using Streamable HTTP transport (default: 3000)
  --token <token>        Bearer token for HTTP authorization (optional, can also use AUTH_TOKEN env var)
  --help, -h             Show this help message
```

### Local Usage Examples

#### Using the slack-mcp command (after global installation)
```bash
# Use stdio transport (default)
slack-mcp

# Use stdio transport explicitly
slack-mcp --transport stdio

# Use Streamable HTTP transport on default port 3000
slack-mcp --transport http

# Use Streamable HTTP transport on custom port
slack-mcp --transport http --port 8080

# Use Streamable HTTP transport with custom auth token
slack-mcp --transport http --token mytoken

# Use Streamable HTTP transport with auth token from environment variable
AUTH_TOKEN=mytoken slack-mcp --transport http
```

#### Using node directly (for development)
```bash
# Use stdio transport (default)
node dist/index.js

# Use stdio transport explicitly
node dist/index.js --transport stdio

# Use Streamable HTTP transport on default port 3000
node dist/index.js --transport http

# Use Streamable HTTP transport on custom port
node dist/index.js --transport http --port 8080

# Use Streamable HTTP transport with custom auth token
node dist/index.js --transport http --token mytoken

# Use Streamable HTTP transport with auth token from environment variable
AUTH_TOKEN=mytoken node dist/index.js --transport http
```

### Docker Usage Examples

#### Using Docker directly
```bash
# Run with stdio transport (default)
docker run --rm \
  -e SLACK_BOT_TOKEN="xoxb-your-bot-token" \
  -e SLACK_TEAM_ID="your-team-id" \
  slack-mcp-server

# Run with HTTP transport on port 3000
docker run --rm -p 3000:3000 \
  -e SLACK_BOT_TOKEN="xoxb-your-bot-token" \
  -e SLACK_TEAM_ID="your-team-id" \
  slack-mcp-server --transport http

# Run with HTTP transport on custom port
docker run --rm -p 8080:8080 \
  -e SLACK_BOT_TOKEN="xoxb-your-bot-token" \
  -e SLACK_TEAM_ID="your-team-id" \
  slack-mcp-server --transport http --port 8080

# Run with custom auth token
docker run --rm -p 3000:3000 \
  -e SLACK_BOT_TOKEN="xoxb-your-bot-token" \
  -e SLACK_TEAM_ID="your-team-id" \
  -e AUTH_TOKEN="mytoken" \
  slack-mcp-server --transport http
```

#### Using Docker Compose
Create a `docker-compose.yml` file:

```yaml
version: '3.8'

services:
  slack-mcp:
    build: .
    # Or use published image:
    # image: @zencoderai/slack-mcp-server
    environment:
      - SLACK_BOT_TOKEN=xoxb-your-bot-token
      - SLACK_TEAM_ID=your-team-id
      - SLACK_CHANNEL_IDS=channel1,channel2,channel3  # Optional
      - AUTH_TOKEN=your-auth-token  # Optional for HTTP transport
    ports:
      - "3000:3000"  # Only needed for HTTP transport
    command: ["--transport", "http"]  # Optional: specify transport type
    restart: unless-stopped
```

Then run:
```bash
# Start the service
docker compose up -d

# View logs
docker compose logs -f slack-mcp

# Stop the service
docker compose down
```

## Transport Types

### Stdio Transport
- **Use case**: Command-line tools and direct integrations
- **Communication**: Standard input/output streams
- **Default**: Yes

### Streamable HTTP Transport
- **Use case**: Remote servers and web-based integrations
- **Communication**: HTTP POST requests with optional Server-Sent Events streams
- **Features**: 
  - Session management
  - Bidirectional communication
  - Resumable connections
  - RESTful API endpoints
  - Bearer token authentication

## Authentication (Streamable HTTP Transport Only)

When using Streamable HTTP transport, the server supports Bearer token authentication:

1. **Command Line**: Use `--token <token>` to specify a custom token
2. **Environment Variable**: Set `AUTH_TOKEN=<token>` as a fallback
3. **Auto-generated**: If neither is provided, a random token is generated

The command line option takes precedence over the environment variable. Include the token in HTTP requests using the `Authorization: Bearer <token>` header.

## Available Tools

1. **slack_list_channels**: List public or predefined channels
2. **slack_post_message**: Post a new message to a channel
3. **slack_reply_to_thread**: Reply to a message thread
4. **slack_add_reaction**: Add emoji reactions to messages
5. **slack_get_channel_history**: Get recent messages from a channel
6. **slack_get_thread_replies**: Get all replies in a thread
7. **slack_get_users**: List workspace users
8. **slack_get_user_profile**: Get detailed user profile information

## Development

### Build
```bash
npm run build
```

### Watch Mode
```bash
npm run watch
```

## API Endpoints (Streamable HTTP Transport)

When using Streamable HTTP transport, the server exposes the following endpoints:

- `POST /mcp` - Client-to-server communication
- `GET /mcp` - Server-to-client notifications (Server-Sent Events streams)
- `DELETE /mcp` - Session termination

## Changes from Previous Version

- **Updated MCP SDK**: Upgraded from v1.0.1 to v1.13.2
- **Modern API**: Migrated from low-level Server class to high-level McpServer class
- **Zod Validation**: Added proper schema validation using Zod
- **Transport Flexibility**: Added support for Streamable HTTP transport
- **Command Line Interface**: Added CLI arguments for transport selection
- **Session Management**: Implemented proper session handling for HTTP transport
- **Better Error Handling**: Improved error handling and logging
