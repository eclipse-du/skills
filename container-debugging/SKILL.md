# Container Debugging & OpenClaw Management

## Description
Guidelines and best practices for debugging Docker containers and managing OpenClaw services, including cross-platform shell compatibility, configuration schema validation, and persistent service management with PM2.

## Table of Contents
1. [Cross-Platform Compatibility](#cross-platform-compatibility)
2. [Service Exploration Policy](#service-exploration-policy)
3. [Environment Awareness](#environment-awareness)
4. [OpenClaw Specific Troubleshooting](#openclaw-specific-troubleshooting)
5. [Service Persistence (PM2)](#service-persistence-pm2)

## Cross-Platform Compatibility
- **Avoid Shell Splice**: Do not use Linux-specific command chaining like `&&` when executing commands from Windows PowerShell if not supported or when the syntax between the host and container might conflict.
- **Encoding Issues**: Standardize on UTF-8. Avoid direct PowerShell redirection to files that might use UTF-16LE, which causes issues for Linux-based tools inside containers. Use `docker cp` for large log extraction.

## Service Exploration Policy
- **Help First**: Before guessing CLI arguments for a containerized service (e.g., `openclaw`), always run `--help` inside the container or check `README.md`.
- **Anatomy of a Task**:
  1. Inspect container environment: `docker exec <id> ls -R /path`
  2. Verify command availability: `docker exec <id> app --version`
  3. Consult the "Manual": Read source configuration schemas (e.g., Zod files) before modifying JSON configs.

## Environment Awareness
- **Tool Selection**: Use appropriate tools for the host OS. For example, use `grep_search` (ripgrep) for cross-directory searches on the host instead of trying to `grep` via complex Docker strings unless necessary.
- **Terminal Context**: Always verify if the current command is intended for the host or the container.

## OpenClaw Specific Troubleshooting
- **OpenRouter 402 Error**: Caused by exceeding `max_tokens` on free tier. Correct fix is in the global `models` mapping with `merge` mode, NOT in `agents.defaults.models`.
- **Invalid Config Keys**: Use `openclaw doctor --fix` to prune unrecognized or misaligned configuration keys.
- **Node Execution**: Use `node -e` or small temporary `.js` scripts inside the container for safe JSON manipulation to avoid host shell escaping issues.

## Service Persistence (PM2)
- **Startup Protection**: Wrap the main entry point (e.g., `openclaw.mjs`) in PM2.
- **Automatic Recovery**: Ensure `pm2 resurrect` is added to `.bashrc` or the container's entrypoint script to maintain service availability after container restarts.
