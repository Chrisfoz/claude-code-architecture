# Basic Project Analysis Example

This example demonstrates how to use the Project Discovery Agent to analyze a repository and generate project insights.

## Overview

The basic analysis workflow:
1. Initialize the Project Discovery Agent
2. Configure analysis parameters
3. Execute repository analysis
4. Review generated insights and recommendations

## Prerequisites

- GitHub token for repository access
- Basic understanding of the project you're analyzing
- Claude Code environment set up

## Setup

### Environment Variables

```bash
# .env
GITHUB_TOKEN=your_github_token_here
BRAVE_SEARCH_API_KEY=your_brave_api_key
```

### Configuration

```yaml
# config/basic-analysis.yml
workflow:
  name: "Basic Project Analysis"
  agents:
    - name: "project-discovery"
      config:
        analysis_depth: "standard"
        include_dependencies: true
        research_technologies: true
```

## Implementation

### TypeScript Implementation

```typescript
import { ProjectDiscoveryAgent } from '../../src/agents/project-discovery'
import { MCPManager } from '../../src/core/MCPManager'
import { Logger } from '../../src/utils/Logger'

interface AnalysisConfig {
  repositoryUrl: string
  analysisDepth: 'basic' | 'standard' | 'comprehensive'
  outputFormat: 'json' | 'markdown' | 'yaml'
}

class BasicProjectAnalyzer {
  private agent: ProjectDiscoveryAgent
  private logger: Logger
  
  constructor() {
    this.logger = new Logger('BasicProjectAnalyzer')
    this.agent = new ProjectDiscoveryAgent({
      mcps: {
        github: {
          token: process.env.GITHUB_TOKEN
        },
        webSearch: {
          apiKey: process.env.BRAVE_SEARCH_API_KEY
        }
      }
    })
  }
  
  async analyzeProject(config: AnalysisConfig) {
    this.logger.info('Starting project analysis', { 
      repository: config.repositoryUrl,
      depth: config.analysisDepth 
    })
    
    try {
      // Execute analysis
      const result = await this.agent.execute({
        repository: {
          url: config.repositoryUrl
        },
        analysis: {
          depth: config.analysisDepth,
          includeDependencies: true,
          scanReadme: true,
          analyzePackageFiles: true,
          checkDockerFiles: true,
          scanCIConfig: true
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
      
      // Process results
      if (result.success) {
        this.logger.info('Analysis completed successfully')
        return this.formatOutput(result, config.outputFormat)
      } else {
        this.logger.error('Analysis failed', { errors: result.errors })
        throw new Error(`Analysis failed: ${result.errors.join(', ')}`)
      }
      
    } catch (error) {
      this.logger.error('Analysis error', error)
      throw error
    }
  }
  
  private formatOutput(result: any, format: string) {
    switch (format) {
      case 'json':
        return JSON.stringify(result, null, 2)
      case 'yaml':
        return this.toYAML(result)
      case 'markdown':
        return this.toMarkdown(result)
      default:
        return result
    }
  }
  
  private toMarkdown(result: any): string {
    const sections = []
    
    // Project Overview
    sections.push(`# Project Analysis: ${result.systemContext.projectName}`)
    sections.push(`\n**Description**: ${result.systemContext.description}`)
    sections.push(`**Primary Language**: ${result.systemContext.primaryLanguage}`)
    sections.push(`**Framework**: ${result.systemContext.framework}`)
    sections.push(`**Architecture**: ${result.systemContext.architecture}`)
    
    // Technology Stack
    sections.push(`\n## Technology Stack`)
    if (result.technologyStack.frontend?.length > 0) {
      sections.push(`\n### Frontend`)
      result.technologyStack.frontend.forEach(tech => {
        sections.push(`- ${tech.name} (${tech.version})`)
      })
    }
    
    if (result.technologyStack.backend?.length > 0) {
      sections.push(`\n### Backend`)
      result.technologyStack.backend.forEach(tech => {
        sections.push(`- ${tech.name} (${tech.version})`)
      })
    }
    
    // Dependencies
    sections.push(`\n## Dependencies`)
    sections.push(`- **Production**: ${result.dependencies.production.length} packages`)
    sections.push(`- **Development**: ${result.dependencies.development.length} packages`)
    if (result.dependencies.vulnerabilities?.length > 0) {
      sections.push(`- **⚠️ Vulnerabilities**: ${result.dependencies.vulnerabilities.length} found`)
    }
    
    // Integration Points
    if (result.integrationPoints?.length > 0) {
      sections.push(`\n## Integration Points`)
      result.integrationPoints.forEach(integration => {
        sections.push(`- **${integration.type}**: ${integration.name}`)
        if (integration.description) {
          sections.push(`  - ${integration.description}`)
        }
      })
    }
    
    // Recommendations
    if (result.recommendations) {
      sections.push(`\n## Recommendations`)
      
      if (result.recommendations.architecture?.length > 0) {
        sections.push(`\n### Architecture`)
        result.recommendations.architecture.forEach(rec => {
          sections.push(`- ${rec.title}: ${rec.description}`)
        })
      }
      
      if (result.recommendations.security?.length > 0) {
        sections.push(`\n### Security`)
        result.recommendations.security.forEach(rec => {
          sections.push(`- ${rec.title}: ${rec.description}`)
        })
      }
    }
    
    return sections.join('\n')
  }
  
  private toYAML(result: any): string {
    // Simple YAML conversion (you might want to use a proper YAML library)
    return Object.entries(result)
      .map(([key, value]) => `${key}: ${JSON.stringify(value, null, 2)}`)
      .join('\n')
  }
}

// Usage example
export async function runBasicAnalysis() {
  const analyzer = new BasicProjectAnalyzer()
  
  const config: AnalysisConfig = {
    repositoryUrl: 'https://github.com/vercel/next.js',
    analysisDepth: 'standard',
    outputFormat: 'markdown'
  }
  
  try {
    const results = await analyzer.analyzeProject(config)
    console.log('Analysis Results:')
    console.log(results)
    
    // Save results to file
    await saveToFile(`analysis-${Date.now()}.md`, results)
    
  } catch (error) {
    console.error('Analysis failed:', error)
  }
}

async function saveToFile(filename: string, content: string) {
  const fs = require('fs').promises
  await fs.writeFile(filename, content, 'utf8')
  console.log(`Results saved to ${filename}`)
}

// Run if this file is executed directly
if (require.main === module) {
  runBasicAnalysis().catch(console.error)
}
```

### CLI Implementation

```bash
#!/bin/bash
# scripts/analyze-project.sh

set -e

# Default values
REPO_URL=""
ANALYSIS_DEPTH="standard"
OUTPUT_FORMAT="markdown"
OUTPUT_FILE=""

# Parse command line arguments
while [[ $# -gt 0 ]]; do
  case $1 in
    -r|--repo)
      REPO_URL="$2"
      shift 2
      ;;
    -d|--depth)
      ANALYSIS_DEPTH="$2"
      shift 2
      ;;
    -f|--format)
      OUTPUT_FORMAT="$2"
      shift 2
      ;;
    -o|--output)
      OUTPUT_FILE="$2"
      shift 2
      ;;
    -h|--help)
      echo "Usage: $0 -r <repo_url> [-d depth] [-f format] [-o output_file]"
      echo "  -r, --repo     Repository URL (required)"
      echo "  -d, --depth    Analysis depth: basic|standard|comprehensive (default: standard)"
      echo "  -f, --format   Output format: json|yaml|markdown (default: markdown)"
      echo "  -o, --output   Output file (default: stdout)"
      exit 0
      ;;
    *)
      echo "Unknown option $1"
      exit 1
      ;;
  esac
done

# Validate required arguments
if [[ -z "$REPO_URL" ]]; then
  echo "Error: Repository URL is required"
  echo "Use -h for help"
  exit 1
fi

# Run analysis
echo "Analyzing repository: $REPO_URL"
echo "Analysis depth: $ANALYSIS_DEPTH"
echo "Output format: $OUTPUT_FORMAT"
echo

node -e "
const { runBasicAnalysis } = require('./examples/workflows/basic-analysis.js');
runBasicAnalysis({
  repositoryUrl: '$REPO_URL',
  analysisDepth: '$ANALYSIS_DEPTH',
  outputFormat: '$OUTPUT_FORMAT'
}).then(results => {
  if ('$OUTPUT_FILE') {
    require('fs').writeFileSync('$OUTPUT_FILE', results);
    console.log('Results saved to $OUTPUT_FILE');
  } else {
    console.log(results);
  }
}).catch(console.error);
"
```

## Usage Examples

### Command Line Usage

```bash
# Basic analysis
./scripts/analyze-project.sh -r https://github.com/user/project

# Comprehensive analysis with JSON output
./scripts/analyze-project.sh -r https://github.com/user/project -d comprehensive -f json -o analysis.json

# Quick analysis for CI/CD
./scripts/analyze-project.sh -r https://github.com/user/project -d basic -f yaml
```

### Programmatic Usage

```typescript
import { runBasicAnalysis } from './examples/workflows/basic-analysis'

// Analyze multiple repositories
const repositories = [
  'https://github.com/vercel/next.js',
  'https://github.com/facebook/react',
  'https://github.com/microsoft/vscode'
]

for (const repo of repositories) {
  const results = await runBasicAnalysis({
    repositoryUrl: repo,
    analysisDepth: 'standard',
    outputFormat: 'json'
  })
  
  console.log(`Analysis for ${repo}:`, results)
}
```

### Integration with CI/CD

```yaml
# .github/workflows/project-analysis.yml
name: Project Analysis

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  analyze:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        
    - name: Install dependencies
      run: npm install
      
    - name: Run project analysis
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        BRAVE_SEARCH_API_KEY: ${{ secrets.BRAVE_SEARCH_API_KEY }}
      run: |
        npm run analyze -- -r ${{ github.repository }} -f json -o analysis.json
        
    - name: Upload analysis results
      uses: actions/upload-artifact@v3
      with:
        name: project-analysis
        path: analysis.json
```

## Expected Output

### Sample Markdown Output

```markdown
# Project Analysis: Next.js

**Description**: The React Framework for Production
**Primary Language**: TypeScript
**Framework**: React
**Architecture**: monolith

## Technology Stack

### Frontend
- React (18.2.0)
- TypeScript (4.9.5)
- CSS Modules (built-in)

### Backend
- Node.js (16+)
- Webpack (5.x)
- Babel (7.x)

## Dependencies
- **Production**: 45 packages
- **Development**: 123 packages
- **⚠️ Vulnerabilities**: 2 found

## Integration Points
- **API**: Built-in API routes
- **Database**: Multiple adapter support
- **Deployment**: Vercel integration

## Recommendations

### Architecture
- Consider migrating to App Router for new features
- Implement proper error boundaries

### Security
- Update vulnerable dependencies
- Implement Content Security Policy
```

## Troubleshooting

### Common Issues

1. **GitHub API Rate Limiting**
   - Use a personal access token
   - Wait for rate limit reset
   - Use GitHub App authentication for higher limits

2. **Repository Access Denied**
   - Ensure token has repository access
   - Check if repository is private
   - Verify repository URL is correct

3. **Analysis Timeout**
   - Reduce analysis depth
   - Exclude large dependencies
   - Increase timeout configuration

### Debug Mode

```bash
# Enable debug logging
DEBUG=true npm run analyze -- -r https://github.com/user/project

# Verbose output
VERBOSE=true npm run analyze -- -r https://github.com/user/project
```

## Next Steps

After running basic analysis:

1. **Review Results**: Examine the generated analysis for insights
2. **Address Issues**: Fix identified vulnerabilities and issues
3. **Architecture Review**: Use results for architecture design agent
4. **Integration Planning**: Plan integrations based on discovered points
5. **Team Discussion**: Share results with your development team

## Related Examples

- [Full-Stack Workflow](./full-stack-workflow.md)
- [Architecture Design](./architecture-design.md)
- [Security Assessment](./security-assessment.md)