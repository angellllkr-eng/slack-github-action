# Owner-safe Slack integration mode

This repository contains the Slack-maintained GitHub Action source. It is an action implementation, not the Mind-Reply Slack workspace configuration.

## Safety boundary

Mind-Reply should use this action only as a **send-only notification adapter** unless a separate owner-approved workflow explicitly requires a Slack API capability.

The default Mind-Reply integration uses a Slack Incoming Webhook and therefore only posts notifications. It must not use a Slack bot token for ordinary GitHub notifications.

The canonical Mind-Reply notification workflow is:

`Mind-Reply/mind-reply-core/.github/workflows/slack-owner-notify.yml`

## Required secret

Configure this repository/org secret in the repository that runs the notification workflow:

`SLACK_WEBHOOK_URL`

The value must be a Slack Incoming Webhook URL. Never commit it to source control and never put it in workflow YAML, logs, issues, PR comments, or Slack messages.

## Trial procedure

1. Add `SLACK_WEBHOOK_URL` as a GitHub Actions secret.
2. Run `Slack Owner Notify` manually from Actions.
3. Confirm exactly one test message arrives in the intended Slack channel.
4. Confirm the GitHub Actions run reports `Slack notification delivered successfully.`
5. Only then allow normal event triggers.

## Failure behavior

Slack delivery failure must not deploy, merge, delete, modify, or roll back anything in GitHub. Notification is an independent job with read-only GitHub permissions.

The Slack action is configured with bounded retries. If Slack remains unavailable, the notification job fails visibly while the underlying GitHub operation remains unchanged.

## Do not use

Do not add a Slack bot token to general-purpose notification workflows. Do not allow Slack messages to become implicit authorization for production changes. Do not store webhook URLs in repository files.
