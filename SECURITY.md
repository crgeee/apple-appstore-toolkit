# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in this plugin, please report it responsibly:

1. **Do not** open a public GitHub issue
2. Report via GitHub private vulnerability reporting at https://github.com/crgeee/apple-appstore-toolkit/security/advisories/new

## Scope

This plugin is a static analysis tool that reads source code files. It does not:

- Execute your app code
- Make network requests to external services
- Store or transmit any data from your project
- Modify your source code

The plugin performs only read operations. Two components have Bash access for read-only shell operations: `assets-metadata-reviewer` uses `sips -g` (read-only property inspection) to check whether app icon files contain an alpha channel, and the `review-app` command uses Bash for project type detection. No component writes to your source files.

## Dependencies

This plugin has no runtime dependencies. It consists entirely of markdown files (agents, commands, skills) interpreted by Claude Code.
