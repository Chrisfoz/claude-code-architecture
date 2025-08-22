# Contributing to Claude Code Architecture Framework

We welcome contributions to the Claude Code Architecture Framework! This document provides guidelines for contributing to the project.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Contributing Guidelines](#contributing-guidelines)
- [Agent Development](#agent-development)
- [MCP Integration](#mcp-integration)
- [Documentation](#documentation)
- [Testing](#testing)
- [Submitting Changes](#submitting-changes)

## Code of Conduct

This project adheres to a code of conduct that promotes a welcoming and inclusive environment. Please read and follow our [Code of Conduct](CODE_OF_CONDUCT.md).

## Getting Started

### Prerequisites

- Node.js 18+ or TypeScript knowledge
- Familiarity with Claude Code and MCP protocols
- Understanding of software development workflows
- Git for version control

### Development Environment

1. **Fork the repository**
```bash
git clone https://github.com/YOUR_USERNAME/claude-code-architecture.git
cd claude-code-architecture
```

2. **Install dependencies**
```bash
npm install
```

3. **Set up environment variables**
```bash
cp .env.example .env
# Edit .env with your API keys and configuration
```

4. **Run tests to verify setup**
```bash
npm test
```

## Contributing Guidelines

### Types of Contributions

We welcome several types of contributions:

1. **Bug Fixes**: Fix issues in existing agents or framework
2. **New Agents**: Implement new specialized agents
3. **MCP Integrations**: Add support for new MCP servers
4. **Documentation**: Improve or add documentation
5. **Examples**: Provide usage examples and tutorials
6. **Testing**: Add or improve test coverage
7. **Performance**: Optimize existing functionality

### Contribution Process

1. **Check existing issues** to see if your contribution is already being worked on
2. **Create an issue** to discuss your proposed changes before starting work
3. **Fork the repository** and create a feature branch
4. **Implement your changes** following our coding standards
5. **Add tests** for your changes
6. **Update documentation** as needed
7. **Submit a pull request** with a clear description

## Agent Development

### Creating a New Agent

1. **Choose a domain**: Identify a specific development domain not covered by existing agents

2. **Create agent directory**:
```bash
mkdir src/agents/your-agent-name
cd src/agents/your-agent-name
```

3. **Implement the agent**:
```typescript
import { BaseAgent } from '../base/BaseAgent'

export class YourAgent extends BaseAgent {
  constructor(config: AgentConfig) {
    super({
      name: 'Your Agent',
      version: '1.0.0',
      mcps: ['required-mcp-1', 'required-mcp-2'],
      dependencies: ['prerequisite-agent']
    })
  }
  
  async execute(input: AgentInput): Promise<AgentOutput> {
    // Your implementation here
  }
  
  validate(output: AgentOutput): ValidationResult {
    // Validation logic here
  }
}
```

4. **Add configuration**:
```yaml
# config/agents/your-agent.yml
name: "Your Agent"
version: "1.0.0"
mcps: ["required-mcp-1", "required-mcp-2"]
settings:
  # Agent-specific settings
```

5. **Write tests**:
```typescript
describe('YourAgent', () => {
  it('should execute successfully', async () => {
    const agent = new YourAgent(testConfig)
    const result = await agent.execute(testInput)
    expect(result.success).toBe(true)
  })
})
```

6. **Add documentation**:
```markdown
# docs/agents/your-agent.md
# Your Agent

## Overview
[Description of what your agent does]

## Required MCPs
[List of required MCP servers]

## Usage
[Usage examples]
```

### Agent Standards

- **Single Responsibility**: Each agent should focus on one specific domain
- **MCP Integration**: Use MCPs for all external system interactions
- **Error Handling**: Implement comprehensive error handling with retries
- **Validation**: Validate all inputs and outputs
- **Logging**: Provide detailed logging for debugging
- **Testing**: Include unit and integration tests
- **Documentation**: Provide comprehensive documentation

## MCP Integration

### Adding New MCP Support

1. **Research the MCP**: Understand the MCP's capabilities and API

2. **Create MCP configuration**:
```yaml
# config/mcps/new-mcp.yml
new-mcp:
  endpoint: "new-mcp"
  required: false
  description: "Description of the MCP"
  config:
    api_key: "${NEW_MCP_API_KEY}"
```

3. **Add integration patterns**:
```typescript
// src/mcps/new-mcp/NewMCPConnector.ts
export class NewMCPConnector {
  async connect(): Promise<MCPConnection> {
    // Connection logic
  }
  
  async execute(operation: string, params: any): Promise<any> {
    // Execution logic
  }
}
```

4. **Update documentation**:
```markdown
# docs/mcps/new-mcp.md
# New MCP Integration

## Overview
[Description of the MCP and its capabilities]

## Configuration
[Configuration instructions]

## Usage Patterns
[Common usage patterns and examples]
```

### MCP Standards

- **Error Resilience**: Handle MCP failures gracefully
- **Rate Limiting**: Respect MCP rate limits
- **Connection Management**: Properly manage connections and cleanup
- **Security**: Handle authentication securely
- **Testing**: Mock MCPs for testing

## Documentation

### Documentation Standards

- **Clear Structure**: Use consistent heading structure
- **Code Examples**: Include practical code examples
- **Configuration**: Document all configuration options
- **Error Scenarios**: Document common error scenarios
- **Best Practices**: Include best practices and tips

### Writing Guidelines

- Use clear, concise language
- Include practical examples
- Keep documentation up-to-date
- Link to related documentation
- Use proper markdown formatting

## Testing

### Testing Requirements

- **Unit Tests**: Test individual functions and methods
- **Integration Tests**: Test agent and MCP integrations
- **End-to-End Tests**: Test complete workflows
- **Performance Tests**: Test performance under load
- **Error Tests**: Test error handling scenarios

### Testing Standards

```typescript
// Example test structure
describe('AgentName', () => {
  beforeEach(() => {
    // Setup
  })
  
  afterEach(() => {
    // Cleanup
  })
  
  describe('core functionality', () => {
    it('should handle normal case', async () => {
      // Test normal operation
    })
    
    it('should handle edge cases', async () => {
      // Test edge cases
    })
    
    it('should handle errors gracefully', async () => {
      // Test error scenarios
    })
  })
})
```

### Running Tests

```bash
# Run all tests
npm test

# Run specific test suite
npm test -- --grep "AgentName"

# Run tests with coverage
npm run test:coverage

# Run integration tests
npm run test:integration
```

## Submitting Changes

### Pull Request Process

1. **Create a feature branch**:
```bash
git checkout -b feature/your-feature-name
```

2. **Make your changes** following the guidelines above

3. **Test your changes**:
```bash
npm test
npm run lint
npm run type-check
```

4. **Commit your changes**:
```bash
git add .
git commit -m "feat: add new agent for X functionality"
```

5. **Push to your fork**:
```bash
git push origin feature/your-feature-name
```

6. **Create a pull request** with:
   - Clear title and description
   - Reference to related issues
   - Screenshots or examples if applicable
   - Checklist of completed items

### Pull Request Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

## Documentation
- [ ] Code comments updated
- [ ] Documentation updated
- [ ] Examples provided

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Tests added for new functionality
- [ ] No breaking changes (or documented)
```

### Commit Message Convention

We use conventional commits for clear change tracking:

- `feat:` - New features
- `fix:` - Bug fixes
- `docs:` - Documentation changes
- `test:` - Test additions or modifications
- `refactor:` - Code refactoring
- `perf:` - Performance improvements
- `chore:` - Maintenance tasks

Example:
```bash
git commit -m "feat(agent): add stripe payment integration agent"
git commit -m "fix(mcp): resolve github connection timeout issue"
git commit -m "docs(readme): update installation instructions"
```

## Getting Help

- **Issues**: Create an issue for bugs or feature requests
- **Discussions**: Use GitHub Discussions for questions and ideas
- **Documentation**: Check existing documentation first
- **Examples**: Look at existing agents and examples for guidance

## Recognition

Contributors will be recognized in:
- README.md contributors section
- Release notes for significant contributions
- Special recognition for major features or improvements

Thank you for contributing to the Claude Code Architecture Framework!