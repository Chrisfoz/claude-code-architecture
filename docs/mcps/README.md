# MCP Integration Guide

This directory contains comprehensive guides for integrating with Model Context Protocol (MCP) servers in the Claude Code Architecture Framework.

## Supported MCPs

### Core MCPs (Required)
- [GitHub Integration](./github.md) - Repository management and code operations
- [Server Filesystem](./server-filesystem.md) - Local file system operations
- [Artifacts](./artifacts.md) - Document and diagram generation
- [Memory Server](./memory-server.md) - Context and knowledge management
- [Web Search](./web-search.md) - Research and discovery
- [REPL](./repl.md) - Code analysis and validation

### Specialized MCPs (Optional)
- [Puppeteer](./puppeteer.md) - UI testing and automation
- [Vercel](./vercel.md) - Deployment management
- [Supabase](./supabase.md) - Database operations
- [Conversation Search](./conversation-search.md) - Historical context
- [Hugging Face](./hugging-face.md) - AI-powered generation

## MCP Integration Patterns

### 1. Discovery Pattern
```typescript
async function discoverProject(repoUrl: string) {
  const codebase = await github.analyzeRepository(repoUrl)
  const context = await conversationSearch.findRelatedProjects()
  const knowledge = await memoryServer.storeProjectContext({
    codebase,
    context,
    timestamp: new Date()
  })
  return knowledge
}
```

### 2. Analysis Pattern
```typescript
async function analyzeCode(filePath: string) {
  const content = await serverFilesystem.readFile(filePath)
  const analysis = await repl.analyzeCode(content)
  const standards = await webSearch.findBestPractices(analysis.language)
  return { content, analysis, standards }
}
```

### 3. Generation Pattern
```typescript
async function generateDocumentation(specs: TechnicalSpec[]) {
  const documentation = await artifacts.createDocument({
    type: 'technical_design',
    content: specs,
    format: 'markdown'
  })
  await github.commitChanges('docs/', documentation)
  return documentation
}
```

### 4. Deployment Pattern
```typescript
async function deployApplication(config: DeploymentConfig) {
  const testResults = await puppeteer.runE2ETests()
  if (testResults.success) {
    const deployment = await vercel.deploy(config)
    await github.createTag(`v${deployment.version}`)
    return deployment
  }
  throw new Error('Tests failed, deployment cancelled')
}
```

## Configuration Management

### MCP Server Configuration
```yaml
# mcp-config.yml
mcp_servers:
  github:
    endpoint: "github"
    required: true
    config:
      token: "${GITHUB_TOKEN}"
      
  server-filesystem:
    endpoint: "server-filesystem"
    required: true
    config:
      allowed_directories: ["/project", "/tmp"]
      
  vercel:
    endpoint: "vercel"
    required: false
    config:
      team_id: "${VERCEL_TEAM_ID}"
      token: "${VERCEL_TOKEN}"
```

### Connection Management
```typescript
class MCPManager {
  private connections: Map<string, MCPConnection> = new Map()
  
  async connect(mcpName: string): Promise<MCPConnection> {
    if (!this.connections.has(mcpName)) {
      const config = this.getConfig(mcpName)
      const connection = await this.createConnection(config)
      this.connections.set(mcpName, connection)
    }
    return this.connections.get(mcpName)!
  }
  
  async disconnect(mcpName: string): Promise<void> {
    const connection = this.connections.get(mcpName)
    if (connection) {
      await connection.close()
      this.connections.delete(mcpName)
    }
  }
}
```

## Error Handling

### Retry Logic
```typescript
async function withRetry<T>(operation: () => Promise<T>, maxRetries = 3): Promise<T> {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await operation()
    } catch (error) {
      if (attempt === maxRetries) throw error
      await new Promise(resolve => setTimeout(resolve, 1000 * attempt))
    }
  }
  throw new Error('Max retries exceeded')
}
```

### Fallback Strategies
```typescript
async function generateWithFallback(content: string): Promise<Artifact> {
  try {
    return await artifacts.createDocument(content)
  } catch (error) {
    // Fallback to local generation
    return await generateLocalDocument(content)
  }
}
```

## Best Practices

1. **Connection Pooling**: Reuse MCP connections when possible
2. **Error Handling**: Implement comprehensive error handling with retries
3. **Timeout Management**: Set appropriate timeouts for MCP operations
4. **Resource Cleanup**: Always clean up connections and resources
5. **Configuration Management**: Use environment variables for sensitive config
6. **Monitoring**: Log all MCP interactions for debugging and monitoring

## Testing MCP Integrations

### Unit Testing
```typescript
describe('GitHub MCP Integration', () => {
  it('should analyze repository structure', async () => {
    const mockGitHub = createMockMCP('github')
    const agent = new ProjectDiscoveryAgent({ github: mockGitHub })
    
    const result = await agent.analyzeCodebase('test-repo')
    
    expect(result.structure).toBeDefined()
    expect(result.dependencies).toBeInstanceOf(Array)
  })
})
```

### Integration Testing
```typescript
describe('End-to-End MCP Flow', () => {
  it('should complete discovery to deployment workflow', async () => {
    const workflow = new AgentWorkflow()
    
    const result = await workflow.execute([
      'project-discovery',
      'architecture-design', 
      'code-quality',
      'cicd-pipeline'
    ])
    
    expect(result.success).toBe(true)
    expect(result.artifacts).toHaveLength(4)
  })
})
```