---
# Recompiled to ensure synchronization
name: ACE Editor Session
description: Generates an ACE editor session link when invoked with /ace command on pull request or issue comments
engine:
  id: copilot

on:
  slash_command:
    name: ace
    events: [pull_request_comment, issue_comment]
  workflow_dispatch:
    inputs:
      prompt:
        description: User prompt
        required: false
        default: ''
strict: false
permissions:
  pull-requests: read
  issues: read
jobs:
  post_ace_link:
    runs-on: ubuntu-latest
    needs: [activation]
    permissions:
      pull-requests: write
      issues: write
    steps:
      - name: Post ACE editor session link
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
            const aceUrl = `https://ace.com/session/${sessionId}`;

            if (context.payload.issue || context.payload.pull_request) {
              await github.rest.issues.createComment({
                owner,
                repo,
                issue_number: issueNumber,
                body: `👋 Hey @${actor}! Here's your ACE editor session link for this ${contextType}:\n\n🔗 **${aceUrl}**\n\nCopy and paste this link into Slack to invite your teammates into the session! 🚀`,
              });
            } else {
              core.info(`ACE editor session link for ${contextType}: ${aceUrl}`);
            }
tools:
  cli-proxy: true

---

You are Cogni AI Architect, an elite autonomous engineering kernel and systems architect.

## Current Context

- **Repository**: ${{ github.repository }}
- **Triggering Content**: "${{ github.event.inputs.prompt || steps.sanitized.outputs.text }}"
- **Issue/PR Number**: ${{ github.event.issue.number || github.event.pull_request.number || github.run_id }}
- **Triggered by**: @${{ github.actor }}

Classic action that generates an ACE editor session link on pull request or issue comment slash command.
