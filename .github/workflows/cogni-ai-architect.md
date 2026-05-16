---
# Cogni AI Architect
# Triggered by /co, /cogni
name: Cogni AI Architect
description: Runs Cogni AI Architect, an elite autonomous engineering kernel and systems architect.
engine:
  id: copilot
imports:
  - Cogni-AI-OU/cogni-ai-agents/cogni-ai-architect/cogni-ai-architect.agent.md@main
on:
  slash_command:
    name: co
    events: [issue_comment, pull_request_comment]
  workflow_dispatch:
    inputs:
      prompt:
        description: User prompt
        required: false
        default: ''
permissions:
  contents: read
  actions: read
  discussions: read
  issues: read
  pull-requests: read
safe-outputs:
  create-pull-request-review-comment:
    max: 20
strict: false
jobs:
  init:
    runs-on: ubuntu-latest
    needs: [activation]
    steps:
      - name: Checkout cogni-ai-agents
        shell: bash
        run: |
          git clone --depth 1 \
            https://github.com/Cogni-AI-OU/cogni-ai-agents.git \
            ${{ runner.temp }}/.agents
      - name: Checkout cogni-ai-agent-instructions
        shell: bash
        run: |
          git clone --depth 1 \
            https://github.com/Cogni-AI-OU/cogni-ai-agent-instructions.git \
            ${{ runner.temp }}/.instructions
      - name: Checkout cogni-ai-agent-skills
        shell: bash
        run: |
          git clone --depth 1 \
            https://github.com/Cogni-AI-OU/cogni-ai-agent-skills.git \
            ${{ runner.temp }}/.skills
  script:
    runs-on: ubuntu-latest
    needs: [activation]
    permissions:
      pull-requests: read
      issues: read
    steps:
      - name: Script test
        uses: actions/github-script@v9
        with:
          script: |
            const issueNumber = context.payload.issue?.number || context.payload.pull_request?.number || context.runId;
            const repo = context.repo.repo;
            const owner = context.repo.owner;
            const actor = context.actor;
            const isPR = !!(context.payload.issue?.pull_request || context.payload.pull_request);
            const contextType = isPR ? 'pull request' : (context.payload.issue ? 'issue' : 'workflow_dispatch');
            const sessionId = `${owner}-${repo}-${isPR ? 'pr' : (context.payload.issue ? 'issue' : 'run')}${issueNumber}`;
tools:
  bash:
    - "cat:*"
    - "echo:*"
    - "mkdir:*"
    - "tee:*"
    - "date:*"
  cli-proxy: true
  github:
    mode: gh-proxy
    toolsets: [default, pull_requests, issues]
timeout-minutes: 60

---

You are Cogni AI Architect, an elite autonomous engineering kernel and systems architect.

## Current Context

- **Repository**: ${{ github.repository }}
- **Triggering Content**: "${{ steps.sanitized.outputs.text }}"
- **Issue/PR Number**: ${{ github.event.issue.number || github.event.pull_request.number }}
- **Triggered by**: @${{ github.actor }}

{{#runtime-import shared/noop-reminder.md}}
