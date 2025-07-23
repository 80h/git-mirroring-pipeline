# Jenkins Git Mirroring Pipeline

[![Jenkins](https://img.shields.io/badge/Jenkins-Docker-blue.svg)](https://www.jenkins.io/doc/book/installing/docker/)
[![GitHub Badge](https://img.shields.io/badge/Author-80h-blue?logo=github)](https://github.com/80h)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A Jenkins pipeline for mirroring Git repositories between different hosts with parallel execution.

## Features

- 🔄 Parallel repository mirroring
- 🔐 SSH key authentication for both source and target
- ⏱️ Execution time tracking
- 📝 Detailed logging with emoji indicators
- 🧹 Workspace cleanup after execution
- 🎨 Colorized console output

## Prerequisites

### System Requirements
- Jenkins 2.303.1 or higher
- Linux-based build agents
- Git 2.20+ installed on all agents

### Jenkins Configuration
- Pipeline support enabled
- SSH Credentials Plugin installed
- Proper network connectivity between:
  - Jenkins and source repositories
  - Jenkins and target repositories

### Permissions
- Write access to target repositories
- Read access to source repositories
- Jenkins agent execution permissions

## Quick Start

```bash
# Clone the repository
git clone https://github.com/80h/git-mirroring-pipeline.git
cd git-mirroring-pipeline

# Install plugins
jenkins-plugin-cli --plugin-file plugins.txt --verbose

# Create config
cp example_config.json repos_config.json
```

## Contribution

We welcome contributions! Please follow these steps:
1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request
