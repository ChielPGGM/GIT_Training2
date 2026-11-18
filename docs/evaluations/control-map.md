# Control map — GitHub Copilot in our organisation

Status: ✅ enforced · ⚠️ available but not set · ❓ unknown · 🔒 can't see (admin only)

Source: Copilot plan "business" via organisation PGGMNV (account data) + VS Code *Developer: Policy Diagnostics* on 2026-11-18.

| Layer | Control | Owner | Status |
|---|---|---|---|
| ① GitHub AI controls | Copilot features (chat, agents, cloud agent, code review) | PGGMNV org admin | ✅ chat, agent mode, MCP and cloud sessions enabled for my account · 🔒 per-feature org policy not visible |
| ① GitHub AI controls | Model availability + "Default availability" for new models | PGGMNV org admin | ❓ unknown — see the model picker / Settings → Copilot · 🔒 admin setting |
| ① GitHub AI controls | MCP servers in Copilot policy | PGGMNV org admin | ✅ MCP enabled (`mcp: true`) · ❓ no allow/deny list visible |
| ① GitHub AI controls | Content exclusion | PGGMNV org admin | ❓ unknown — `copilotignore_enabled: false` is only a hint · 🔒 admin setting |
| ① GitHub AI controls | Audit log (agent actions) | PGGMNV org / enterprise admin | 🔒 can't see (admin only) |
| ② Enterprise-managed settings | `permissions` / `sandbox` | Org / enterprise admin | ⚠️ not set — no managed settings active (0 keys, source: none) |
| ② Enterprise-managed settings | Default `model` / `fusionTier` | Org / enterprise admin | ⚠️ not set — 0 managed keys |
| ② Enterprise-managed settings | MCP allow / deny lists | Org / enterprise admin | ⚠️ not set — `allowedMcpServers` / `deniedMcpServers` not supplied |
| ③ VS Code device policies | ChatAgentMode · ChatToolsAutoApprove · ChatMCP | Device / IT admin | ⚠️ not applied — only 1 of 74 policies applied (`CopilotSessionSync`, from account) |
| Repository | preToolUse guardrail hook (`.github/hooks/`) — Step 4 | me (this lab) | ⚠️ not yet created (`.github/hooks/` does not exist) |
| Repository | Branch ruleset on `main` — Step 5 | me (this lab) | ⚠️ not yet set up |
| Repository | CI check `Test` (`.github/workflows/ci.yml`) | | ✅ |

## Findings

- No enterprise or device policy is active on this machine, so the effective limits come from account/org settings and my own settings.
- Policy gate is inactive (no approved organisations configured) and the chat harness is not enforced.
- Gaps to raise with an admin: model availability, content exclusion, audit log and managed MCP lists (❓ / 🔒).
