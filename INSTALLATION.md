
# Installation Guide

Complete guide to installing and using the Claude Code Agent Skills Marketplace.

## Quick Start

### Install All Skills

```bash
# Clone the repository
git clone https://github.com/ihiteshgupta/ClaudeSkills.git
cd ClaudeSkills

# Copy all skills to Claude Code skills directory
cp -r skills/* ~/.config/claude/skills/

# Verify installation
ls ~/.config/claude/skills/
```

### Install by Category

#### Frontend Skills
```bash
cp -r skills/frontend/* ~/.config/claude/skills/
```

#### Cloud Skills (AWS, GCP, Azure)
```bash
cp -r skills/cloud/* ~/.config/claude/skills/
```

#### Kubernetes & DevOps
```bash
cp -r skills/kubernetes/* ~/.config/claude/skills/
cp -r skills/devops/* ~/.config/claude/skills/
```

#### Documentation & Diagrams
```bash
cp -r skills/documentation/* ~/.config/claude/skills/
cp -r skills/diagrams/* ~/.config/claude/skills/
```

#### Architecture & Security
```bash
cp -r skills/architecture/* ~/.config/claude/skills/
cp -r skills/security/* ~/.config/claude/skills/
```

### Install Individual Skills

```bash
# Install specific skill
cp -r skills/diagrams/mermaid-diagrams ~/.config/claude/skills/

# Install AWS architect skill
cp -r skills/cloud/aws/aws-architect ~/.config/claude/skills/

# Install React expert skill
cp -r skills/frontend/react-expert ~/.config/claude/skills/
```

## Skill Locations

Claude Code searches for skills in the following locations (in order):

1. **User-level skills**: `~/.config/claude/skills/`
2. **Project-level skills**: `.claude/skills/` (in your project directory)

### Project-Specific Skills

To make skills available only for a specific project:

```bash
# Navigate to your project
cd /path/to/your/project

# Create project skills directory
mkdir -p .claude/skills

# Copy desired skills
cp -r /path/to/ClaudeSkills/skills/frontend/react-expert .claude/skills/
```

## Verification

After installation, verify that skills are available:

```bash
# Check installed skills
ls ~/.config/claude/skills/

# You should see directories like:
# - react-expert/
# - mermaid-diagrams/
# - aws-architect/
# - kubernetes-expert/
# etc.
```

## Usage

Once installed, Claude Code will automatically detect and use the skills. You don't need to explicitly invoke them - Claude will use the appropriate skill based on your request.

### Example Usage

**Creating Diagrams:**
```
"Create a mermaid sequence diagram showing the user authentication flow"
```
Claude will automatically use the `mermaid-diagrams` skill.

**AWS Deployment:**
```
"Design an AWS architecture for a scalable web application with auto-scaling"
```
Claude will use the `aws-architect` skill.

**React Development:**
```
"Create a React component with hooks for managing user data"
```
Claude will use the `react-expert` skill.

**System Design:**
```
"Design a microservices architecture for an e-commerce platform"
```
Claude will use the `system-design` skill.

## Updating Skills

To update skills to the latest version:

```bash
# Navigate to the cloned repository
cd ClaudeSkills

# Pull latest changes
git pull origin main

# Update skills in Claude Code directory
cp -r skills/* ~/.config/claude/skills/
```

## Troubleshooting

### Skills Not Being Used

If Claude isn't using the skills:

1. **Check skill location:**
   ```bash
   ls ~/.config/claude/skills/
   ```

2. **Verify SKILL.md exists:**
   ```bash
   cat ~/.config/claude/skills/react-expert/SKILL.md
   ```

3. **Check file permissions:**
   ```bash
   chmod -R 755 ~/.config/claude/skills/
   ```

### Skills Directory Doesn't Exist

If `~/.config/claude/` doesn't exist:

```bash
# Create directory
mkdir -p ~/.config/claude/skills/

# Copy skills
cp -r skills/* ~/.config/claude/skills/
```

### Permission Issues

If you encounter permission errors:

```bash
# Fix permissions
chmod -R 755 ~/.config/claude/skills/

# If needed, change ownership
chown -R $USER:$USER ~/.config/claude/skills/
```

## Platform-Specific Instructions

### macOS

```bash
# Skills directory
~/.config/claude/skills/

# Installation
cp -r skills/* ~/.config/claude/skills/
```

### Linux

```bash
# Skills directory
~/.config/claude/skills/

# Installation
cp -r skills/* ~/.config/claude/skills/
```

### Windows (WSL)

```bash
# Skills directory
~/.config/claude/skills/

# Installation
cp -r skills/* ~/.config/claude/skills/
```

### Windows (PowerShell)

```powershell
# Skills directory
$env:USERPROFILE\.config\claude\skills\

# Installation
Copy-Item -Recurse skills\* $env:USERPROFILE\.config\claude\skills\
```

## Selective Installation

### Install Only What You Need

If you only work on specific tech stacks, install only relevant skills:

**Full-Stack JavaScript Developer:**
```bash
cp -r skills/frontend/react-expert ~/.config/claude/skills/
cp -r skills/backend/nodejs-expert ~/.config/claude/skills/
cp -r skills/database/sql-expert ~/.config/claude/skills/
cp -r skills/api/rest-api ~/.config/claude/skills/
cp -r skills/testing/unit-testing ~/.config/claude/skills/
```

**DevOps Engineer:**
```bash
cp -r skills/kubernetes/kubernetes-expert ~/.config/claude/skills/
cp -r skills/devops/terraform ~/.config/claude/skills/
cp -r skills/cloud/aws/aws-architect ~/.config/claude/skills/
cp -r skills/cloud/gcp/gcp-architect ~/.config/claude/skills/
cp -r skills/devops/cicd-expert ~/.config/claude/skills/
```

**Cloud Architect:**
```bash
cp -r skills/cloud/* ~/.config/claude/skills/
cp -r skills/architecture/* ~/.config/claude/skills/
cp -r skills/kubernetes/kubernetes-expert ~/.config/claude/skills/
cp -r skills/diagrams/architecture-diagrams ~/.config/claude/skills/
```

**Technical Writer:**
```bash
cp -r skills/documentation/* ~/.config/claude/skills/
cp -r skills/diagrams/* ~/.config/claude/skills/
```

## Uninstallation

To remove skills:

```bash
# Remove all skills
rm -rf ~/.config/claude/skills/*

# Remove specific skill
rm -rf ~/.config/claude/skills/react-expert

# Remove entire skills directory
rm -rf ~/.config/claude/skills/
```

## Next Steps

- Browse the [README.md](README.md) for a complete list of available skills
- Check [CONTRIBUTING.md](CONTRIBUTING.md) to add your own skills
- Read individual skill documentation in their SKILL.md files

## Support

For issues or questions:
- Open an issue on GitHub
- Check existing skills for examples
- Refer to [Claude Code documentation](https://docs.claude.com/en/docs/claude-code/skills)
