# Slack

A highly-configurable Github Action that sends Slack notifications from within Github Action workflows.

## Usage

1. Create an [Incoming Webhook](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks) in your Slack workspace. Copy the webhook URL, you'll need it in the next step.
1. Create a [Github secret](https://docs.github.com/en/actions/reference/encrypted-secrets) in the settings tab in a repository, or organization if you want it to be used across all repositories within an organization. Give it the name "SLACK_WEBHOOK_URL" and value as the webhook URL.
1. Add "SLACK_WEBHOOK_URL" as an [`env`](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#env) to every workflow file you want to use this action in.
    ```
    env:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
    ```
1. Add this action as a step:
    ```
    - name: Send Slack Notification
      uses: keithalpichi/slack
    ```
1. Add your desired inputs from the input section below to the step.

## Github Action Inputs

required
- [`template`](#template)

optional

Usage of these optional inputs is determined by the template you use. See the [Templates](#templates) section below for images and details of each template.
- [`title`](#title)
- [`description`](#description)
- [`status`](#status)
- [`channel`](#channel)

### `template`
**`string`** (required)

The template ID that identifies the template to use for the Slack notification message. You can use the following templates: 

- `plain1`- a template for plain messages that displays a custom description.
- `plain2`- a template for plain messages that displays the link to the current Github Action run.

See the [Templates](#templates) section below for images and details of each template.

#### Example usage
```
- name: Send Slack Notification
  uses: keithalpichi/slack
  with:
    template: plain1
```
### `title`
**`string`**

The title to display on the Slack notification message. If this is not provided, "Github Action" is used.

#### Example usage
```
- name: Send Slack Notification
  uses: keithalpichi/slack
  with:
    template: plain1
    title: Success
```

### `description`
**`string`**

The description to display on the Slack notification message.

#### Example usage
```
- name: Send Slack Notification
  uses: keithalpichi/slack
  with:
    template: plain1
    description: Everything looks good!
```


### `status`
**`job.status|string`**

The current status of the job to use within the template. Usage of this input depends on the `template` input provided. If you'd like for Github to report the current status of the job use [`${{ job.status }}`](https://docs.github.com/en/actions/reference/context-and-expression-syntax-for-github-actions#job-context). Possible status values reported by Github are success, failure, or cancelled. You may also choose to provide any string value.

#### Example usage
Using the `job.status` context:
```
- name: Send Slack Notification
  uses: keithalpichi/slack
  with:
    status: ${{ job.status }}
    template: plain2
```
Providing a custom status:
```
- name: Send Slack Notification
  uses: keithalpichi/slack
  with:
    status: all green
    template: plain2
```

### `channel`
**`string`**

The channel or user to send the Slack message to.
- If this is not provided, it will default to the channel assigned to the Slack Incoming Webhook.
- If a channel is provided it must be a valid channel that starts with '#'
- If a user is provided it must be a valid user that starts with '@'

#### Example usage
```
- name: Send Slack Notification
  uses: keithalpichi/slack
  with:
    template: plain1
    channel: #cicd
```
```
- name: Send Slack Notification
  uses: keithalpichi/slack
  with:
    template: plain1
    channel: @octocat
```

## Examples

### Using [Github Action status check functions](https://docs.github.com/en/actions/reference/context-and-expression-syntax-for-github-actions#job-status-check-functions) to conditionally run this Github Action
This is useful if you want to run this Github Action only when a certain status exists:
```
steps:
  ...
  - name: Always notify Slack no matter what happens
    status: ${{ job.status }}
    if: ${{ always() }}
```
```
steps:
  ...
  - name: Only notify Slack when it is successful
    status: ${{ job.status }}
    if: ${{ success() }}
```
```
steps:
  ...
  - name: Only notify Slack when there is a failure
    status: ${{ job.status }}
    if: ${{ failure() }}
```

## Templates

### `plain1`
A message with a text description and default title. This template uses the following inputs:
- description (required)- the description in the message
- title - if provided overrides the default title "Github Action" 
```
with:
  template: plain1
  description: We're all green!
```
![](https://user-images.githubusercontent.com/14797743/110732183-80472f80-81d8-11eb-96d3-a37404c09b33.png)

### `plain2`
A message with a link to the Github Action run and default title. This template uses the following inputs:
- title - if provided overrides the default title "Github Action" 
```
with:
  template: plain2
```

![](https://user-images.githubusercontent.com/14797743/110732185-80dfc600-81d8-11eb-94ac-5eb238977993.png)
```
with:
  title: ${{ job.status }}
  template: plain2
```
A message with a link to the Github Action run and job status as the title.
![](https://user-images.githubusercontent.com/14797743/110749277-c959ac80-81f5-11eb-8521-a25ee07d79f5.png)

```
with:
  title: We're all green!
  template: plain2
```
A message with a link to the Github Action run and a custom title as the title.
![](https://user-images.githubusercontent.com/14797743/110732181-80472f80-81d8-11eb-8f4f-b659882800b7.png)