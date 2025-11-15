# Contributing to Claude Code Agent Skills Marketplace

Thank you for your interest in contributing! This document provides guidelines for adding new skills to the marketplace.

## How to Contribute

1. Fork the repository
2. Create a new branch for your skill
3. Add your skill following the structure below
4. Test your skill with Claude Code
5. Submit a pull request

## Skill Structure

Each skill must follow this structure:

```
skill-name/
├── SKILL.md          # Required - Main skill definition
├── REFERENCE.md      # Optional - Reference documentation
├── FORMS.md          # Optional - Templates and forms
└── scripts/          # Optional - Helper scripts
```

### SKILL.md Format

Every skill must have a `SKILL.md` file with YAML frontmatter:

```markdown
---
name: skill-name
description: Brief description of what this skill does and when to use it. Keep under 200 characters.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# Skill Title

## Purpose
Clear explanation of what this skill provides.

## When to Use This Skill
- Specific use case 1
- Specific use case 2
- Specific use case 3

## [Your Content Here]
Detailed instructions, examples, best practices, etc.
```

### Required Frontmatter Fields

- **name**: Kebab-case skill name (e.g., `react-expert`, `aws-architect`)
- **description**: Clear, concise description (when Claude should use this skill)
- **allowed-tools**: List of tools the skill can use (optional)

### Frontmatter Guidelines

**Good description:**
```yaml
description: Expert in React development with hooks, TypeScript, and modern patterns. Use for React component development, optimization, and best practices.
```

**Bad description:**
```yaml
description: React stuff
```

## Skill Categories

Add your skill to the appropriate category:

- `frontend/` - Frontend frameworks and libraries
- `backend/` - Backend languages and frameworks
- `database/` - Database technologies
- `api/` - API development
- `testing/` - Testing frameworks and methodologies
- `documentation/` - Documentation tools
- `architecture/` - System design and architecture
- `cloud/` - Cloud platform specific (aws/, gcp/, azure/)
- `kubernetes/` - Container orchestration
- `devops/` - DevOps tools and CI/CD
- `ui-ux/` - Design and user experience
- `diagrams/` - Visualization and diagramming
- `security/` - Security practices
- `performance/` - Performance optimization
- `monitoring/` - Observability and monitoring

If your skill doesn't fit existing categories, propose a new one in your PR.

## Content Guidelines

### 1. Be Specific and Practical

**Good:**
```markdown
## Creating a React Component with Hooks

\`\`\`typescript
interface Props {
  userId: string;
  onUpdate: (user: User) => void;
}

export const UserProfile: React.FC<Props> = ({ userId, onUpdate }) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser(userId).then(setUser).finally(() => setLoading(false));
  }, [userId]);

  // ... rest of component
};
\`\`\`
```

**Bad:**
```markdown
Use React hooks to manage state.
```

### 2. Include Code Examples

Always provide working code examples that demonstrate:
- Best practices
- Common patterns
- Real-world scenarios
- Error handling

### 3. Use Clear Structure

Organize content with:
- Clear headings
- Bulleted lists
- Code blocks with syntax highlighting
- Tables for comparison
- Diagrams when helpful

### 4. Cover Edge Cases

Address:
- Common pitfalls
- Error handling
- Security considerations
- Performance implications
- Testing strategies

## Example Skill Template

```markdown
---
name: example-skill
description: Expert in [technology]. Use for [specific use cases].
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# [Technology] Expert

## Purpose
Provide expert guidance on [technology] including [key areas].

## When to Use This Skill
- Use case 1
- Use case 2
- Use case 3

## Core Concepts

### Concept 1
Explanation with example:

\`\`\`language
// Code example
\`\`\`

### Concept 2
Explanation with example.

## Best Practices

1. **Practice 1** - Explanation
2. **Practice 2** - Explanation

## Common Patterns

### Pattern 1
\`\`\`language
// Example code
\`\`\`

### Pattern 2
\`\`\`language
// Example code
\`\`\`

## Anti-Patterns to Avoid

- Anti-pattern 1 and why
- Anti-pattern 2 and why

## Tools & Libraries

- Tool 1 - Description
- Tool 2 - Description

## Testing

\`\`\`language
// Testing example
\`\`\`

## Additional Resources

- [Resource 1](url)
- [Resource 2](url)
```

## Testing Your Skill

Before submitting, test your skill:

1. **Install locally:**
   ```bash
   cp -r your-skill ~/.config/claude/skills/
   ```

2. **Test with Claude Code:**
   - Try various prompts that should trigger your skill
   - Verify Claude uses the skill appropriately
   - Check that examples work correctly

3. **Validate structure:**
   ```bash
   # Check SKILL.md exists
   cat your-skill/SKILL.md

   # Verify frontmatter
   head -n 10 your-skill/SKILL.md
   ```

## Pull Request Process

1. **Create a descriptive PR title:**
   - Good: "Add Python backend development skill"
   - Bad: "New skill"

2. **In your PR description:**
   - Describe what the skill does
   - List key features covered
   - Explain when Claude should use it
   - Note any dependencies or prerequisites

3. **Checklist:**
   - [ ] Skill follows directory structure
   - [ ] SKILL.md has proper frontmatter
   - [ ] Description is clear and concise
   - [ ] Includes practical code examples
   - [ ] Tested with Claude Code
   - [ ] No typos or grammar errors
   - [ ] Added to appropriate category
   - [ ] Updated README.md if needed

## Code of Conduct

- Be respectful and inclusive
- Provide constructive feedback
- Focus on quality over quantity
- Help others learn

## Quality Standards

Skills should be:
- **Accurate** - Technically correct and up-to-date
- **Practical** - Includes working examples
- **Comprehensive** - Covers key use cases
- **Clear** - Well-organized and easy to follow
- **Tested** - Verified to work with Claude Code

## Updating Existing Skills

To update an existing skill:

1. Create a PR with your changes
2. Explain what's being updated and why
3. Ensure backward compatibility if possible
4. Update examples to reflect current best practices

## Style Guide

### Markdown Formatting

- Use `#` for main title (h1)
- Use `##` for major sections (h2)
- Use `###` for subsections (h3)
- Use code fences with language specification
- Use tables for structured data
- Use bulleted lists for items
- Use numbered lists for steps

### Code Examples

```markdown
\`\`\`typescript
// Always specify language
// Include comments for clarity
const example = "formatted code";
\`\`\`
```

### Voice and Tone

- Use clear, professional language
- Write in active voice
- Be concise but complete
- Use "you" to address the user
- Avoid jargon unless explained

## Questions?

- Open an issue for questions
- Check existing skills for examples
- Refer to [Claude Code documentation](https://docs.claude.com/en/docs/claude-code/skills)

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

Thank you for contributing to the Claude Code Agent Skills Marketplace!
