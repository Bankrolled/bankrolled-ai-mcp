Draft for a pull request to https://github.com/docker/mcp-registry (not submitted).
Copy `servers/bankrolled-agent-hub/` into the fork's `servers/` folder. Remote server, no authentication, so no `oauth` block and no `dynamic.tools` field. `tools.json` is `[]` for remote servers, per CONTRIBUTING.md.
