---
# Copilot Agentic Workflow Test
name: Copilot AW Test
description: Generates a comment when invoked with /test command on pull request or issue comments.
engine:
  id: copilot

on:
  slash_command:
    name: test
    events: [pull_request_comment, issue_comment]
  workflow_dispatch:
strict: true
permissions:
  pull-requests: read
  issues: read
tools:
  bash: true
  cli-proxy: true
---

This is the test run.
Reply with the comment.
That's it.
