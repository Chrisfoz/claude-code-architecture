# Agent Specifications

This directory contains detailed specifications for each agent in the Claude Code Architecture Framework.

## Agent Categories

### Discovery & Planning Layer
- [Project Discovery Agent](./project-discovery.md)
- [Architecture Design Agent](./architecture-design.md) 
- [UI/UX Design Agent](./ui-ux-design.md)

### Implementation Layer
- [Data Architecture Agent](./data-architecture.md)
- [Security Implementation Agent](./security-implementation.md)
- [API Integration Agent](./api-integration.md)
- [Code Quality Agent](./code-quality.md)
- [Stripe Payment Agent](./stripe-payment.md)

### Deployment & Operations Layer
- [CI/CD Pipeline Specialist](./cicd-pipeline.md)
- [Documentation Agent](./documentation.md)

## Agent Interface Standards

All agents implement the base `Agent` interface:

```typescript
interface Agent {
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

## Common Patterns

### Input/Output Structure
```typescript
interface AgentInput {
  projectContext: ProjectContext
  previousOutputs: AgentOutput[]
  configuration: Record<string, any>
  timestamp: Date
}

interface AgentOutput {
  agentName: string
  version: string
  outputs: OutputArtifact[]
  metadata: OutputMetadata
  timestamp: Date
}
```

### MCP Usage Pattern
```typescript
async function useMCP(mcpName: string, operation: string, params: any) {
  const connection = await connectToMCP(mcpName)
  const result = await connection.execute(operation, params)
  await connection.disconnect()
  return result
}
```

## Agent Development Guidelines

1. **Single Responsibility**: Each agent focuses on one domain
2. **MCP Integration**: Use MCPs for all external interactions
3. **Context Preservation**: Maintain project context throughout execution
4. **Error Handling**: Implement comprehensive error handling
5. **Validation**: Validate all inputs and outputs
6. **Documentation**: Generate comprehensive documentation

## Testing Requirements

- Unit tests for core functionality
- Integration tests with MCPs
- Output quality validation
- Performance benchmarks
- Error scenario testing