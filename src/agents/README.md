# Agent Implementation Templates

This directory contains implementation templates and base classes for building agents in the Claude Code Architecture Framework.

## Base Agent Interface

All agents implement the `BaseAgent` interface:

```typescript
interface BaseAgent {
  name: string
  version: string
  mcps: string[]
  dependencies: string[]
  
  initialize(config: AgentConfig): Promise<void>
  execute(input: AgentInput): Promise<AgentOutput>
  validate(output: AgentOutput): ValidationResult
  cleanup(): Promise<void>
}
```

## Directory Structure

```
src/agents/
├── base/                    # Base agent implementation
│   ├── BaseAgent.ts
│   ├── AgentTypes.ts
│   └── AgentUtils.ts
├── project-discovery/       # Project Discovery Agent
├── architecture-design/     # Architecture Design Agent
├── ui-ux-design/           # UI/UX Design Agent
├── data-architecture/      # Data Architecture Agent
├── security-implementation/ # Security Implementation Agent
├── api-integration/        # API Integration Agent
├── code-quality/           # Code Quality Agent
├── stripe-payment/         # Stripe Payment Agent
├── cicd-pipeline/          # CI/CD Pipeline Specialist
└── documentation/          # Documentation Agent
```

## Getting Started

1. Choose an agent template from the directories above
2. Copy the template to your project
3. Implement the required methods
4. Configure the necessary MCPs
5. Test the agent implementation

## Implementation Guidelines

### 1. Agent Structure
```typescript
export class ProjectDiscoveryAgent extends BaseAgent {
  constructor(config: AgentConfig) {
    super({
      name: 'Project Discovery Agent',
      version: '1.0.0',
      mcps: ['github', 'web_search', 'memory-server'],
      dependencies: []
    })
  }
  
  async execute(input: AgentInput): Promise<AgentOutput> {
    // Implementation here
  }
}
```

### 2. MCP Integration
```typescript
// Use the MCP manager for all external integrations
const github = await this.mcpManager.connect('github')
const analysis = await github.analyzeRepository(repoUrl)
await this.mcpManager.disconnect('github')
```

### 3. Error Handling
```typescript
try {
  const result = await this.executeOperation()
  return this.createSuccessOutput(result)
} catch (error) {
  this.logger.error('Operation failed', error)
  return this.createErrorOutput(error)
}
```

### 4. Output Validation
```typescript
validate(output: AgentOutput): ValidationResult {
  const errors: string[] = []
  
  if (!output.artifacts || output.artifacts.length === 0) {
    errors.push('No artifacts generated')
  }
  
  return {
    valid: errors.length === 0,
    errors
  }
}
```

## Testing

Each agent should include:
- Unit tests for core functionality
- Integration tests with MCPs
- Output validation tests
- Performance benchmarks

```typescript
describe('ProjectDiscoveryAgent', () => {
  it('should analyze repository structure', async () => {
    const agent = new ProjectDiscoveryAgent(testConfig)
    const result = await agent.execute(testInput)
    
    expect(result.success).toBe(true)
    expect(result.artifacts).toHaveLength(3)
  })
})
```

## Best Practices

1. **Single Responsibility**: Focus on one specific domain
2. **MCP-First**: Use MCPs for all external interactions
3. **Context Preservation**: Maintain project context
4. **Error Resilience**: Handle errors gracefully
5. **Comprehensive Logging**: Log all significant operations
6. **Output Quality**: Validate all outputs before returning
7. **Resource Management**: Clean up resources properly