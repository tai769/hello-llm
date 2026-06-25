---
name: agent-builder
description: A skill for building AI agents
---

# Agent Builder Skill

## Purpose
This skill helps you build AI agents with tool use capabilities.

## Key Concepts
1. Agent Loop: while stop_reason == "tool_use"
2. Tool Definition: JSON Schema format
3. Tool Execution: dispatch via handlers
4. Message History: maintain context

## Steps
1. Define tools with clear descriptions
2. Implement tool handlers
3. Create the agent loop
4. Handle errors gracefully
5. Add safety checks

## Best Practices
- Keep tools simple and focused
- Use clear, descriptive tool names
- Validate all inputs
- Handle timeouts properly
- Log tool executions
