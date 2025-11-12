# component-tests-deploy-action

A GitHub action that deploys Playwright component test reports to S3.
It's the second action of sequence "Test -> Deploy Report".

## Inputs

- `project` (required) - ID of your project.
- `github-token` (required) - PAT of a GitHub user which maintains PR's deploy comment, usually it's a bot.
- `s3-key-id` (required) - S3 access key ID for uploading to S3.
- `s3-secret-key` (required) - S3 secret access key for uploading to S3.

## Example

```yaml
name: Component Tests Deploy

on:
  workflow_run:
    workflows: ['Component Tests']
    types:
    - completed

jobs:
  deploy:
    name: Deploy Report
    if: >
      github.event.workflow_run.event == 'pull_request' &&
      github.event.workflow_run.conclusion == 'success'
    runs-on: ubuntu-latest
    steps:
    - uses: gravity-ui/component-tests-deploy-action@v1
      with:
        project: uikit
        github-token: ${{ secrets.YC_UI_BOT_GITHUB_TOKEN }}
        s3-key-id: ${{ secrets.STORYBOOK_S3_KEY_ID }}
        s3-secret-key: ${{ secrets.STORYBOOK_S3_SECRET_KEY }}
```
