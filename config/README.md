# Configuration Examples

This directory contains example configurations for agents and MCP servers in the Claude Code Architecture Framework.

## Directory Structure

- `agents/` - Agent-specific configuration examples
- `mcps/` - MCP server configuration templates
- `workflows/` - Workflow configuration examples
- `projects/` - Project-specific configuration templates

## Quick Start

1. Copy the relevant configuration files to your project
2. Update environment variables and settings
3. Test the configuration with your specific setup
4. Customize as needed for your requirements

## Configuration Files

### Agent Configurations
- [project-discovery-agent.yml](./agents/project-discovery-agent.yml)
- [architecture-design-agent.yml](./agents/architecture-design-agent.yml)
- [code-quality-agent.yml](./agents/code-quality-agent.yml)
- [cicd-pipeline-agent.yml](./agents/cicd-pipeline-agent.yml)

### MCP Configurations
- [mcp-servers.yml](./mcps/mcp-servers.yml)
- [github-config.yml](./mcps/github-config.yml)
- [vercel-config.yml](./mcps/vercel-config.yml)

### Workflow Configurations
- [full-stack-workflow.yml](./workflows/full-stack-workflow.yml)
- [frontend-workflow.yml](./workflows/frontend-workflow.yml)
- [api-workflow.yml](./workflows/api-workflow.yml)

## Environment Variables

Create a `.env` file with the following variables:

```bash
# GitHub Integration
GITHUB_TOKEN=your_github_token
GITHUB_OWNER=your_username

# Vercel Integration
VERCEL_TOKEN=your_vercel_token
VERCEL_TEAM_ID=your_team_id

# Supabase Integration
SUPABASE_PROJECT_ID=your_project_id
SUPABASE_API_KEY=your_api_key

# OpenAI Integration (for Hugging Face)
OPENAI_API_KEY=your_openai_key
```