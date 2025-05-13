# :vertical_traffic_light: Check First Commit
**A GitHub Action to detect if the current push is the first commit on the default branch of a non-fork repository.**
Ideal for template repositories, onboarding automations, or one-time project setup logic.
---
## :mag: What It Does
This action checks the following conditions:
- The workflow was triggered by a `push` event (not a PR or a fork).
- The push is to the **default branch**.
- The repository has **only one commit** (`HEAD` is the root commit).
If all conditions are met, it sets the output `first_commit` to `"true"`.
---
## :white_check_mark: Outputs
```
outputs:
  first_commit: "true" | "false"
```
Use this in conditional logic within your workflow:
```
if: steps.first.outputs.first_commit == 'true'
```
---
## :rocket: Usage Examples
### :bulb: Run a One-Time Setup Script in Template Repos
```yaml
name: Template Bootstrap
on: [push]
jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Check if this is the first commit
        id: first
        uses: rynodivino/check-first-commit@v1
      - name: Replace tokens with repo name
        if: steps.first.outputs.first_commit == 'true'
        run: ./scripts/setup-template.sh
```
---
### :test_tube: Conditional CI Only on First Push
```yaml
name: Initial Lint and Test
on: [push]
jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: rynodivino/check-first-commit@v1
        id: first
      - name: Run CI checks
        if: steps.first.outputs.first_commit == 'true'
        run: |
          npm ci
          npm run lint
          npm test
```
---
## :open_file_folder: Use Cases
- :bricks: **Template Repositories**
  Run a setup script when a new repo is created from a template (e.g. replacing placeholder tokens, setting project name).
- :rocket: **Project Bootstrapping**
  Automatically create `.env` files, initial configuration, or secrets for new projects.
- :bulb: **Preventing Redundant Steps**
  Skip expensive or unnecessary workflows unless it's the very first push.
- :lock: **Secure Defaults**
  Avoid running setup logic on forks or pull requests for security-sensitive projects.
---
## :hammer_and_wrench: Implementation Details
This is a [composite GitHub Action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action) using a bash script to perform the check.
Internally, it:
- Verifies the workflow was triggered by a `push`
- Skips execution if from a fork or PR
- Confirms the current branch is the default
- Counts commits to determine if it's the first
---
## :page_facing_up: License
[MIT](LICENSE)
---
## :speech_balloon: Feedback & Contributions
Contributions, issues, and suggestions are welcome!
Feel free to open a PR or file an issue to improve the action.