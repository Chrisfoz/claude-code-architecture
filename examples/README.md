# Examples

This directory contains practical examples and sample implementations for the Claude Code Architecture Framework.

## Directory Structure

- `workflows/` - Complete workflow examples
- `projects/` - Sample project configurations
- `agents/` - Individual agent usage examples
- `integrations/` - MCP integration examples

## Quick Start Examples

### 1. Basic Project Analysis
```typescript
import { ProjectDiscoveryAgent } from '../src/agents/project-discovery'

async function analyzeProject() {
  const agent = new ProjectDiscoveryAgent({
    mcps: {
      github: { token: process.env.GITHUB_TOKEN },
      webSearch: { apiKey: process.env.BRAVE_API_KEY }
    }
  })
  
  const analysis = await agent.execute({
    repositoryUrl: 'https://github.com/user/project',
    analysisDepth: 'comprehensive'
  })
  
  console.log('Project Analysis:', analysis)
}
```

### 2. Full-Stack Workflow
```typescript
import { WorkflowExecutor } from '../src/core/WorkflowExecutor'

async function runFullStackWorkflow() {
  const executor = new WorkflowExecutor()
  
  const result = await executor.execute({
    workflow: 'full-stack-development',
    project: {
      repository: 'https://github.com/user/project',
      features: ['authentication', 'payments', 'database']
    }
  })
  
  console.log('Workflow Result:', result)
}
```

### 3. Custom Agent Implementation
```typescript
import { BaseAgent } from '../src/agents/base/BaseAgent'

class CustomAnalysisAgent extends BaseAgent {
  constructor() {
    super({
      name: 'Custom Analysis Agent',
      version: '1.0.0',
      mcps: ['github', 'repl'],
      dependencies: []
    })
  }
  
  async execute(input: AgentInput): Promise<AgentOutput> {
    // Custom analysis implementation
    const github = await this.mcpManager.connect('github')
    const files = await github.listFiles(input.repository)
    
    // Process files...
    
    return this.createOutput({
      analysis: 'Custom analysis results',
      files: files.length
    })
  }
}
```

## Example Projects

### 1. E-commerce Application
- Full-stack Next.js application
- Stripe payment integration
- Supabase database
- Vercel deployment

### 2. SaaS Dashboard
- React frontend
- Node.js API
- Authentication system
- CI/CD pipeline

### 3. Mobile App Backend
- Express.js API
- PostgreSQL database
- JWT authentication
- Docker deployment

## Integration Examples

### GitHub Integration
```typescript
// Analyze repository structure
const github = await mcpManager.connect('github')
const structure = await github.getRepositoryStructure('user/repo')
const dependencies = await github.analyzeDependencies('user/repo')
```

### Vercel Deployment
```typescript
// Deploy to Vercel
const vercel = await mcpManager.connect('vercel')
const deployment = await vercel.deploy({
  project: 'my-app',
  environment: 'production'
})
```

### Supabase Operations
```typescript
// Database operations
const supabase = await mcpManager.connect('supabase')
const tables = await supabase.listTables()
const migration = await supabase.createMigration('add_users_table')
```

## Running Examples

1. **Install dependencies**
```bash
npm install
```

2. **Set up environment variables**
```bash
cp .env.example .env
# Edit .env with your API keys
```

3. **Run an example**
```bash
npm run example:project-analysis
npm run example:full-stack-workflow
npm run example:custom-agent
```

## Contributing Examples

1. Create a new example in the appropriate directory
2. Include comprehensive documentation
3. Add environment setup instructions
4. Include test cases
5. Update this README with the new example

## Example Categories

### Workflow Examples
- Complete development workflows
- Multi-agent coordination
- Error handling and recovery

### Agent Examples
- Individual agent implementations
- Custom agent development
- Agent configuration patterns

### Integration Examples
- MCP server integrations
- External API connections
- Database operations

### Project Examples
- Real-world project configurations
- Best practices demonstrations
- Performance optimizations