# report-action-usage

> Action to create a CSV or Markdown report of GitHub Actions used

[![Test](https://github.com/ActionsDesk/report-action-usage/actions/workflows/test.yml/badge.svg)](https://github.com/ActionsDesk/report-action-usage/actions/workflows/test.yml) [![styled with prettier](https://img.shields.io/badge/styled_with-prettier-ff69b4.svg)](https://github.com/prettier/prettier)

## Usage

**Scheduled GitHub Enterprise Cloud report example**

```yml
name: GitHub Actions usage report (scheduled)

on:
  schedule:
    # Runs at 00:42 UTC on the first of every month
    #
    #        ┌─────────────── minute
    #        │  ┌──────────── hour
    #        │  │ ┌────────── day (month)
    #        │  │ │ ┌──────── month
    #        │  │ │ │ ┌────── day (week)
    - cron: '42 0 1 * *'

jobs:
  enterprise:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2.3.4

      - uses: ActionsDesk/report-action-usage@v2.0.1
        id: action-uses
        with:
          token: ${{ secrets.ENTERPRISE_ADMIN_TOKEN }}
          enterprise: my-enterprise-slug
          csv: reports/actions-used.csv
          md: reports/actions-used.md
          push_results_to_repo: true

      # example: output
      - run: |
          echo "${{ steps.action-uses.outputs.json_result }}"
          echo "${{ steps.action-uses.outputs.csv_result }}"
          echo "${{ steps.action-uses.outputs.md_result }}"
```

<details>
  <summary><strong>On-demand GitHub Enterprise Cloud report example</strong></summary>

```yml
name: GitHub Actions usage report

on:
  workflow_dispatch:
    inputs:
      enterprise:
        description: 'GitHub Enterprise Cloud account slug'
        required: true
      exclude:
        description: |
          Exclude actions created by GitHub
          i.e. actions from https://github.com/actions and https://github.com/github organizations
        default: 'false'
        required: false
      csv:
        description: 'Path to CSV for the output, e.g. /path/to/action-uses.csv'
        default: ''
        required: false
      md:
        description: 'Path to markdown for the output, e.g. /path/to/action-uses.md'
        default: ''
        required: false
      push_results_to_repo:
        description: Push the CSV/markdown results to the repoository
        default: 'false'
        required: false

jobs:
  enterprise:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2.3.4

      - uses: ActionsDesk/report-action-usage@v2.0.1
        with:
          token: ${{ secrets.ENTERPRISE_ADMIN_TOKEN }}
          enterprise: ${{ github.event.inputs.enterprise }}
          csv: ${{ github.event.inputs.csv }}
          md: ${{ github.event.inputs.md }}
          push_results_to_repo: ${{ github.event.inputs.push_results_to_repo }}
```

</details>

### Action Inputs

| Name                   | Description                                                                                                                    | Default | Required |
| :--------------------- | :----------------------------------------------------------------------------------------------------------------------------- | :------ | :------- |
| `token`                | GitHub Personal Access Token ([PAT]) with appropriate user/organization/enterprise scope                                       |         | `true`   |
| `enterprise`           | GitHub Enterprise Cloud account slug, will require `read:org`, `read:enterprise`, and `repo` scoped [PAT] for `token`          |         | `false`  |
| `owner`                | GitHub organization/user login, will require `read:org` (only if querying an organization) and `repo` scoped [PAT] for `token` |         | `false`  |
| `exclude`              | Exclude actions created by GitHub, i.e. actions from https://github.com/actions and https://github.com/github organizations    | `false` | `false`  |
| `unique`               | List unique GitHub Actions only                                                                                                | `false` | `false`  |
| `csv`                  | Path to CSV for the output, e.g. /path/to/action-uses.csv                                                                      |         | `false`  |
| `md`                   | Path to markdown for the output, e.g. /path/to/action-uses.md                                                                  |         | `false`  |
| `push_results_to_repo` | Push the CSV/markdown results to the repoository                                                                               | `false` | `false`  |

Note: If the `enterprise` input is omitted, the report will only be created for the organization the repository belongs to.

### Action Outputs

| Name          | Description                                                        |
| :------------ | :----------------------------------------------------------------- |
| `json_result` | GitHub Actions usage report JSON                                   |
| `csv_result`  | GitHub Actions usage report CSV (only if `csv` input provided)     |
| `md_result`   | GitHub Actions usage report markdown (only if `md` input provided) |

## License

- [MIT License](./license)

[pat]: https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token 'Personal Access Token'
