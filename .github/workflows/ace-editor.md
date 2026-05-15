---
name: ACE Editor Session
description: Generates an ACE editor session link when invoked with /ace command on pull request or issue comments
on:
  slash_command:
    name: ace
    events: [pull_request_comment, issue_comment]
strict: false
permissions:
  pull-requests: read
  issues: read
jobs:
  post_ace_link:
    runs-on: ubuntu-latest
    needs: [activation]
    if: needs.activation.outputs.activated == 'true'
    permissions:
      pull-requests: write
      issues: write
    steps:
      - name: Post ACE editor session link
        uses: actions/github-script@v9
        with:
          script: |
            const issueNumber = context.payload.issue.number;
            const repo = context.repo.repo;
            const owner = context.repo.owner;
            const actor = context.actor;
            const isPR = !!context.payload.issue.pull_request;
            const contextType = isPR ? 'pull request' : 'issue';
            const sessionId = `${owner}-${repo}-${isPR ? 'pr' : 'issue'}${issueNumber}`;
            const aceUrl = `https://ace.com/session/${sessionId}`;

            await github.rest.issues.createComment({
              owner,
              repo,
              issue_number: issueNumber,
              body: `👋 Hey @${actor}! Here's your ACE editor session link for this ${contextType}:\n\n🔗 **${aceUrl}**\n\nCopy and paste this link into Slack to invite your teammates into the session! 🚀`,
            });
tools:
  cli-proxy: true

---

Classic action that generates an ACE editor session link on pull request or issue comment slash command.
