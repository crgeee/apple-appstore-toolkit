# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in this plugin, please report it responsibly:

1. **Do not** open a public GitHub issue
2. Email security concerns to the maintainer via GitHub private vulnerability reporting at https://github.com/crgeee/apple-appstore-toolkit/security/advisories/new

## Scope

This plugin is a static analysis tool that reads source code files. It does not:

- Execute your app code
- Make network requests to external services
- Store or transmit any data from your project
- Modify your source code (read-only agents)

The only agent with write capability (`Bash` tool) is `assets-metadata-reviewer`, which uses `sips` to check image metadata.

## Dependencies

This plugin has no runtime dependencies. It consists entirely of markdown files (agents, commands, skills) interpreted by Claude Code.
