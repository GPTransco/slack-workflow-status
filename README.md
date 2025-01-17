# Slack Workflow Status
This action will post workflow status notifications into your Slack channel. The notification includes the name of the Actor, Event, Branch, Workflow Name, Status and Run Durations. This action can optionally include the status and duration of individual jobs in a workflow to quickly help you identify where failures and slowdowns occur.

<img src="./docs/images/example.png" title="Slack Example">

## Action Inputs

| Name | Required | Description |
| ---- | -------- | ----------- |
| **slack_webhook_url** | _required_ | Create a Slack Webhook URL using the [Incoming Webhooks App](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks?next_id=0). It is recommended that you create a new secret on your repo `SLACK_WEBHOOK_URL` for holding this value, and passing it to the action with `${{secrets.SLACK_WEBHOOK_URL}}`.
| **repo_token** | _required_ | A token is automatically available in your workflow secrets var. `${{secrets.GITHUB_TOKEN}}`. You can optionally send an alternative self-generated token.
| **channel** | _optional_ | Accepts a Slack channel name where you would like the notifications to appear. Overrides the default channel created with your webhook.
| **name** | _optional_ | Allows you to provide a name for the slack bot user posting the notifications. Overrides the default name created with your webhook.
| **icon_emoji** | _optional_ | Allows you to provide an emoji as the slack bot user image when posting notifications. Overrides the default image created with your webhook. _[Emoji Code Cheat Sheet](https://www.webfx.com/tools/emoji-cheat-sheet/)_
| **icon_url** | _optional_ | Allows you to provide a URL for an image to use as the slack bot user image when posting notifications. Overrides the default image created with your webhook.
| **include_jobs** | _optional_ | When set to `true`, individual job status and durations in the slack notification. When `false` only the event status and workflow status lines are included.
| **hide_successful_jobs** | _optional_ | When set to `true`, jobs with success status will not be added to the notification jobs list.

## Usage
To use this action properly, you should create a new `job` at the end of your workflow that `needs` all other jobs in the workflow. This ensures that this action is only run once all jobs in your workflow are complete.

```yaml
name: World Greeter
on:
  push:
    branches: [ master, staging ]
jobs:
  job-1:
    runs-on: ubuntu-latest
    steps:
      - name: Say Hello
        run: echo "Hello"
  job-2:
    runs-on: ubuntu-latest
    steps:
      - name: Say World
        run: echo "World"
  slack-workflow-status:
    if: always()
    name: Post Workflow Status To Slack
    needs:
      - job-1
      - job-2
    runs-on: ubuntu-latest
    steps:
      - name: Slack Workflow Notification
        uses: Gamesight/slack-workflow-status@master
        with:
          # Required Input
          repo_token: ${{secrets.GITHUB_TOKEN}}
          slack_webhook_url: ${{secrets.SLACK_WEBHOOK_URL}}
          # Optional Input
          channel: '#anthony-test-channel'
          name: 'Anthony Workflow Bot'
          icon_emoji: ':poop:'
          icon_url: 'https://avatars0.githubusercontent.com/u/1701160?s=96&v=4'
```

This action can also be used for Pull Request workflows and will include pull request information in the notification.

<img src="./docs/images/example-pr.png" title="Slack Pull Request Example">


_developed and maintained by: [gamesight.io](https://gamesight.io)_
