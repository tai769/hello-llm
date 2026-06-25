---
name: mcp-builder
description: A skill for building MCP (Model Context Protocol) servers
---

# MCP Builder Skill

## Purpose
This skill helps you build MCP servers for tool integration.

## What is MCP?
MCP (Model Context Protocol) is a protocol for connecting AI models to external tools and data sources.

## Key Components
1. Server: Handles tool requests
2. Tools: Functions that can be called
3. Resources: Data that can be accessed
4. Prompts: Templates for interactions

## Steps
1. Define your tools in JSON Schema format
2. Implement tool handlers
3. Set up the server
4. Handle authentication
5. Test thoroughly

## Example Tools
- read_file: Read file contents
- write_file: Write content to file
- execute_command: Run shell commands
- search_files: Find files by pattern
