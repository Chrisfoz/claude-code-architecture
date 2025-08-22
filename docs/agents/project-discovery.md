# Project Discovery Agent

The Project Discovery Agent is responsible for analyzing project requirements, dependencies, and integration points to provide comprehensive project context and foundation for other agents.

## Overview

**Purpose**: Analyze existing codebases, identify dependencies, map integration points, and establish project context

**Position in Workflow**: First agent to run in most workflows, provides foundation for all subsequent agents

**Key Responsibilities**:
- Repository structure analysis
- Dependency identification and mapping
- Technology stack assessment
- Integration point discovery
- External entity identification
- System context generation

## Required MCPs

### Core MCPs
- **github** - Repository analysis and codebase inspection
- **web_search** - Technology research and best practices discovery
- **conversation_search** - Historical project context retrieval
- **memory-server** - Project knowledge graph storage

### Optional MCPs
- **server-filesystem** - Local file system analysis
- **artifacts** - Documentation and diagram generation

## Input Specification

```typescript
interface ProjectDiscoveryInput {
  // Repository information
  repository: {
    url: string
    branch?: string
    accessToken?: string
  }
  
  // Analysis configuration
  analysis: {
    depth: 'basic' | 'standard' | 'comprehensive'
    includeDependencies: boolean
    scanReadme: boolean
    analyzePackageFiles: boolean
    checkDockerFiles: boolean
    scanCIConfig: boolean
  }
  
  // GitHub analysis settings
  github?: {
    analyzeCommits: boolean
    checkIssues: boolean
    scanPullRequests: boolean
    includeWiki: boolean
  }
  
  // Web search configuration
  webSearch?: {
    researchTechnologies: boolean
    findBestPractices: boolean
    checkSecurityAdvisories: boolean
  }
  
  // Output preferences
  output?: {
    generateDiagrams: boolean
    includeExamples: boolean
    createRecommendations: boolean
  }
}
```

## Output Specification

```typescript
interface ProjectDiscoveryOutput {
  // System context
  systemContext: {
    projectName: string
    description: string
    primaryLanguage: string
    framework: string
    architecture: 'monolith' | 'microservices' | 'serverless' | 'hybrid'
  }
  
  // External entities
  externalEntities: ExternalEntity[]
  
  // Technology stack
  technologyStack: {
    frontend: TechnologyInfo[]
    backend: TechnologyInfo[]
    database: TechnologyInfo[]
    infrastructure: TechnologyInfo[]
    tools: TechnologyInfo[]
  }
  
  // Dependencies
  dependencies: {
    production: Dependency[]
    development: Dependency[]
    peer: Dependency[]
    vulnerabilities: SecurityVulnerability[]
  }
  
  // Integration points
  integrationPoints: IntegrationPoint[]
  
  // Recommendations
  recommendations: {
    architecture: ArchitectureRecommendation[]
    security: SecurityRecommendation[]
    performance: PerformanceRecommendation[]
    maintenance: MaintenanceRecommendation[]
  }
  
  // Generated artifacts
  artifacts: {
    systemContextDiagram: string
    dependencyGraph: string
    integrationMap: string
    technologyReport: string
  }
}
```

## Core Functions

### Repository Analysis
```typescript
async analyzeRepository(repoUrl: string): Promise<RepositoryAnalysis> {
  const github = await this.mcpManager.connect('github')
  
  // Get repository metadata
  const metadata = await github.getRepositoryInfo(repoUrl)
  
  // Analyze file structure
  const fileStructure = await github.getFileTree(repoUrl)
  
  // Identify key files
  const keyFiles = this.identifyKeyFiles(fileStructure)
  
  // Analyze package files
  const dependencies = await this.analyzeDependencies(keyFiles)
  
  // Generate analysis
  return {
    metadata,
    structure: fileStructure,
    keyFiles,
    dependencies,
    framework: this.detectFramework(fileStructure),
    architecture: this.inferArchitecture(fileStructure)
  }
}
```

### Technology Research
```typescript
async researchTechnologies(technologies: string[]): Promise<TechnologyResearch[]> {
  const webSearch = await this.mcpManager.connect('web_search')
  const results: TechnologyResearch[] = []
  
  for (const tech of technologies) {
    const searchResults = await webSearch.search(`${tech} best practices 2024`)
    const securityInfo = await webSearch.search(`${tech} security vulnerabilities`)
    
    results.push({
      name: tech,
      bestPractices: this.extractBestPractices(searchResults),
      securityConsiderations: this.extractSecurityInfo(securityInfo),
      currentVersion: await this.getCurrentVersion(tech),
      recommendations: this.generateTechRecommendations(tech)
    })
  }
  
  return results
}
```

### Integration Point Mapping
```typescript
async mapIntegrationPoints(fileStructure: FileNode[]): Promise<IntegrationPoint[]> {
  const integrationPoints: IntegrationPoint[] = []
  
  // Scan for API endpoints
  const apiEndpoints = await this.scanForAPIEndpoints(fileStructure)
  integrationPoints.push(...this.convertToIntegrationPoints(apiEndpoints, 'api'))
  
  // Scan for database connections
  const dbConnections = await this.scanForDatabaseConnections(fileStructure)
  integrationPoints.push(...this.convertToIntegrationPoints(dbConnections, 'database'))
  
  // Scan for external service calls
  const externalServices = await this.scanForExternalServices(fileStructure)
  integrationPoints.push(...this.convertToIntegrationPoints(externalServices, 'service'))
  
  // Scan for message queues
  const messageQueues = await this.scanForMessageQueues(fileStructure)
  integrationPoints.push(...this.convertToIntegrationPoints(messageQueues, 'queue'))
  
  return integrationPoints
}
```

### Context Generation
```typescript
async generateSystemContext(analysis: RepositoryAnalysis): Promise<SystemContext> {
  const memoryServer = await this.mcpManager.connect('memory-server')
  
  // Create project entities
  const projectEntity = await memoryServer.createEntity({
    name: analysis.metadata.name,
    type: 'project',
    properties: {
      language: analysis.metadata.language,
      framework: analysis.framework,
      architecture: analysis.architecture
    }
  })
  
  // Create dependency relationships
  for (const dep of analysis.dependencies.production) {
    const depEntity = await memoryServer.createEntity({
      name: dep.name,
      type: 'dependency',
      properties: { version: dep.version }
    })
    
    await memoryServer.createRelation({
      from: projectEntity.id,
      to: depEntity.id,
      type: 'depends_on'
    })
  }
  
  return {
    project: projectEntity,
    dependencies: analysis.dependencies,
    integrations: analysis.integrationPoints,
    recommendations: this.generateRecommendations(analysis)
  }
}
```

## Configuration Example

```yaml
# project-discovery-agent.yml
name: "Project Discovery Agent"
version: "1.0.0"

mcps:
  - github
  - web_search
  - conversation_search
  - memory-server

settings:
  analysis_depth: "comprehensive"
  include_dependencies: true
  scan_readme: true
  analyze_package_files: true
  check_docker_files: true
  scan_ci_config: true
  
  github:
    analyze_commits: true
    check_issues: true
    scan_pull_requests: false
    include_wiki: false
    
  web_search:
    research_technologies: true
    find_best_practices: true
    check_security_advisories: true
    
  output:
    generate_diagrams: true
    include_examples: true
    create_recommendations: true

quality_thresholds:
  min_confidence_score: 0.8
  max_execution_time: "5m"
  required_outputs: ["system_context", "integration_map"]
```

## Usage Examples

### Basic Usage
```typescript
import { ProjectDiscoveryAgent } from './agents/project-discovery'

const agent = new ProjectDiscoveryAgent({
  mcps: {
    github: { token: process.env.GITHUB_TOKEN },
    webSearch: { apiKey: process.env.BRAVE_API_KEY }
  }
})

const result = await agent.execute({
  repository: {
    url: 'https://github.com/user/project'
  },
  analysis: {
    depth: 'comprehensive',
    includeDependencies: true,
    scanReadme: true,
    analyzePackageFiles: true
  }
})

console.log('System Context:', result.systemContext)
console.log('Integration Points:', result.integrationPoints)
```

### Advanced Configuration
```typescript
const result = await agent.execute({
  repository: {
    url: 'https://github.com/user/project',
    branch: 'develop'
  },
  analysis: {
    depth: 'comprehensive',
    includeDependencies: true,
    scanReadme: true,
    analyzePackageFiles: true,
    checkDockerFiles: true,
    scanCIConfig: true
  },
  github: {
    analyzeCommits: true,
    checkIssues: true,
    scanPullRequests: true
  },
  webSearch: {
    researchTechnologies: true,
    findBestPractices: true,
    checkSecurityAdvisories: true
  },
  output: {
    generateDiagrams: true,
    includeExamples: true,
    createRecommendations: true
  }
})
```

## Testing

### Unit Tests
```typescript
describe('ProjectDiscoveryAgent', () => {
  let agent: ProjectDiscoveryAgent
  
  beforeEach(() => {
    agent = new ProjectDiscoveryAgent(testConfig)
  })
  
  it('should analyze repository structure', async () => {
    const mockGitHub = createMockMCP('github')
    agent.mcpManager.registerMock('github', mockGitHub)
    
    const result = await agent.analyzeRepository('test-repo')
    
    expect(result.structure).toBeDefined()
    expect(result.dependencies).toBeInstanceOf(Array)
    expect(result.framework).toBeDefined()
  })
  
  it('should identify integration points', async () => {
    const fileStructure = createMockFileStructure()
    
    const integrations = await agent.mapIntegrationPoints(fileStructure)
    
    expect(integrations).toBeInstanceOf(Array)
    expect(integrations.length).toBeGreaterThan(0)
  })
})
```

### Integration Tests
```typescript
describe('ProjectDiscoveryAgent Integration', () => {
  it('should complete full discovery workflow', async () => {
    const agent = new ProjectDiscoveryAgent(realConfig)
    
    const result = await agent.execute({
      repository: { url: 'https://github.com/test/repo' },
      analysis: { depth: 'standard' }
    })
    
    expect(result.systemContext).toBeDefined()
    expect(result.technologyStack).toBeDefined()
    expect(result.integrationPoints).toBeInstanceOf(Array)
    expect(result.recommendations).toBeDefined()
  })
})
```

## Error Handling

The agent implements comprehensive error handling:

```typescript
try {
  const analysis = await this.analyzeRepository(input.repository.url)
  return this.createSuccessOutput(analysis)
} catch (error) {
  if (error instanceof GitHubAPIError) {
    return this.createErrorOutput('GitHub API error', error)
  } else if (error instanceof NetworkError) {
    return this.createRetryableError('Network error', error)
  } else {
    return this.createFatalError('Unknown error', error)
  }
}
```

## Performance Considerations

- **Parallel Processing**: Analyze multiple aspects simultaneously
- **Caching**: Cache API responses to avoid redundant calls
- **Rate Limiting**: Respect API rate limits
- **Timeout Management**: Set appropriate timeouts for long-running operations
- **Memory Management**: Process large repositories in chunks

## Future Enhancements

- AI-powered code understanding
- Automated architecture pattern detection
- Real-time dependency vulnerability scanning
- Integration with additional code quality tools
- Support for monorepo analysis
- Custom pattern detection