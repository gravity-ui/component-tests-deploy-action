# component-tests-deploy-action

A GitHub action that deploys Playwright component test reports to S3.

This action is a wrapper around the generic `deploy-to-s3-action` that is preconfigured for deploying Playwright test reports to S3 and creating PR comments with preview links.

It's the second action of the sequence "Test -> Deploy Report", typically used with `component-tests-run-action`.

## Inputs

- `project` (required) - ID of your project.
- `github-token` (required) - PAT of a GitHub user which maintains PR's deploy comment, usually it's a bot.
- `s3-key-id` (required) - S3 access key ID for uploading to S3.
- `s3-secret-key` (required) - S3 secret access key for uploading to S3.

## Usage Example

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

## How It Works

1. Downloads the `playwright-report` artifact from the triggering workflow
2. Extracts the PR number from the artifacts
3. Uploads the report to S3 at `s3://playwright-reports/{project}/pulls/{pr-id}/`
4. Creates a comment on the PR with a direct link to the test report

## Notes

This action uses the `deploy-to-s3-action` with the following preconfigured values:
- Source path: `playwright-report`
- Bucket: `playwright-reports`
- Endpoint URL: `https://storage.yandexcloud.net`
- Preview base URL: `https://storage.yandexcloud.net/playwright-reports`
- Preview URL path: `{project}/pulls/{pr}/index.html`
- Region: `ru-central1`
- Comment message: `🎭 [Component Tests Report]({url}) is ready.`
- Final URL format: `https://storage.yandexcloud.net/playwright-reports/{project}/pulls/{pr-id}/index.html`

## Related Actions

- `component-tests-run-action` - Runs component tests and creates artifacts
- `deploy-to-s3-action` - Generic deploy action (used internally)

## License

MIT
