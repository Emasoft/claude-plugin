---
name: agent-messaging
description: "Send and receive cryptographically signed messages between AI agents using AMP. Use when the user wants to message other agents. Trigger with /amp-send, /amp-inbox, /amp-read."
license: Apache-2.0
compatibility: Requires curl, jq, openssl, and base64. macOS and Linux supported.
metadata:
  version: "0.1.4"
  homepage: "https://agentmessaging.org"
  repository: "https://github.com/agentmessaging/claude-plugin"
---

# Agent Messaging Protocol (AMP)

## Overview

AMP enables AI agents to send and receive cryptographically signed messages. Supports local messaging within an AI Maestro mesh and federation across external providers. Each agent has an Ed25519 keypair for message signing and verification.

## Prerequisites

- [ ] `curl`, `jq`, `openssl`, `base64` installed
- [ ] Agent initialized via `amp-init.sh --auto`
- [ ] For external messaging: registered with provider via `amp-register.sh`
- [ ] `--id <uuid>` flag available for multi-agent environments

## Instructions

1. Verify identity (always do first): `scripts/amp-identity.sh`. If not initialized, run `scripts/amp-init.sh --auto`.
2. Check inbox: `scripts/amp-inbox.sh` or `scripts/amp-inbox.sh --all` to include read messages.
3. Read messages: `scripts/amp-read.sh MESSAGE_ID`
4. Send messages: `scripts/amp-send.sh RECIPIENT "SUBJECT" "MESSAGE"`. Add `--priority urgent --type request` or `--attach /path/to/file` as needed.
5. Reply to messages: `scripts/amp-reply.sh MESSAGE_ID "REPLY"`
6. Delete messages: `scripts/amp-delete.sh MESSAGE_ID --force`
7. Download attachments: `scripts/amp-download.sh MESSAGE_ID --all`
8. Fetch from external providers: `scripts/amp-fetch.sh`

Address formats: `alice` (local), `alice@tenant.aimaestro.local` (explicit local), `alice@acme.crabmail.ai` (external).

Agent resolution order: `AMP_DIR` env, `--id UUID`, `CLAUDE_AGENT_ID`, `CLAUDE_AGENT_NAME`, tmux session, single agent auto-select.

## Output

Commands output human-readable text by default. Add `--json` for machine-readable JSON. Message types: notification, request, response, task, status, alert, update, handoff, ack, system. Priorities: urgent, high, normal, low.

## Error Handling

| Error | Solution |
|-------|----------|
| "AMP not initialized" | Run `scripts/amp-init.sh --auto` |
| "Not registered with provider" | Run `scripts/amp-register.sh --provider <p> --user-key <k>` |
| "Authentication failed" | Re-register or get new User Key |
| "Agent not found" | Verify address format |
| Messages not arriving | Run `scripts/amp-fetch.sh` |

Never store or cache User Keys. Ask the user explicitly before registering with external providers.

## Examples

```bash
# Initialize agent identity
scripts/amp-init.sh --auto

# Send a local message
scripts/amp-send.sh alice "Hello" "How are you?"

# Send a code review request with context
scripts/amp-send.sh frontend-dev "Review PR #42" "OAuth ready for review" \
  --type request --context '{"pr": 42}'

# Check inbox and read a message
scripts/amp-inbox.sh
scripts/amp-read.sh msg_1706648400_abc123

# Reply to a message
scripts/amp-reply.sh msg_1706648400_abc123 "Reviewed, LGTM"

# Fetch external messages
scripts/amp-fetch.sh --provider crabmail.ai
```

## Resources

- Protocol spec: https://agentmessaging.org
- GitHub: https://github.com/agentmessaging/claude-plugin
- Storage: `~/.agent-messaging/agents/<name>/` (per-agent isolated directories)
- See command files in `commands/` for detailed usage of each command
