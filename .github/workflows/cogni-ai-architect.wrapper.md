---
name: Cogni AI Architect Wrapper
on:
  workflow_dispatch:
    inputs:
      prompt:
        description: User prompt
        required: false
        default: 'Hello from wrapper'

jobs:
  call-architect:
    uses: ./.github/workflows/cogni-ai-architect.lock.yml
    with:
      prompt: ${{ github.event.inputs.prompt || inputs.prompt }}
    secrets: inherit
---

This is a wrapper workflow to test `workflow_call` support in `cogni-ai-architect`.
It passes the `prompt` input to the called workflow.
