# Continuous integration

[Back to the README](../README.md)

## What the green check means

SocialScope currently has documentation and repository configuration only. The
`repository-checks` job checks Markdown formatting, local documentation links and
anchors, and GitHub Actions workflow syntax. It does not run an application build,
unit tests, browser tests, provider integrations, or a deployment.

External web links are deliberately excluded from the required link check. An
unrelated website outage or rate limit should not block a repository change.
Review external sources manually when changing documentation.

## When checks run

The [workflow](../.github/workflows/ci.yml) runs on every pull request, pushes to
`main`, merge-group checks, and manual dispatch. No pull-request branch or path
filters are used. Keep the job name `repository-checks` stable because branch
protection refers to that exact name.

From GitHub, open **Actions → Repository CI**, then select a run to see its steps.
After the workflow is on `main`, **Run workflow** starts a manual run.

## Run the same tools locally

Run these commands from the repository root. The versions below match CI as of
20 October 2026.

Markdown formatting requires a Node.js version supported by
[markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2):

```sh
npx --yes markdownlint-cli2@0.23.2 "**/*.md"
```

Install [lychee 0.24.2](https://github.com/lycheeverse/lychee/releases/tag/lychee-v0.24.2)
and [actionlint 1.7.12](https://github.com/rhysd/actionlint/releases/tag/v1.7.12)
from their official releases for your operating system, then run:

```sh
lychee --offline --include-fragments --no-progress "./**/*.md"
actionlint -color
git diff --check
```

If `actionlint` detects an installed ShellCheck or pyflakes, it also uses those
tools. CI uses GitHub's Ubuntu runner, which includes ShellCheck. These extra
integrations depend on what is installed locally.

## Required check and branch protection

The default branch ruleset should require `repository-checks` from GitHub Actions,
with branches up to date before merging. Pull requests are required, while the
required reviewer approval count is zero so a solo maintainer can merge their own
passing pull requests. Force pushes and deletion of the default branch are blocked.

When introducing or renaming a check, run it successfully on a pull request before
updating the required-check list. Never require a job that does not run for every
pull request targeting the protected branch.

## Troubleshooting

| Symptom | What to do |
| --- | --- |
| Markdown fails | Find the file, line, and rule in the failed step; fix it and push again. |
| A local link fails | Fix the path or heading anchor; paths on CI are case-sensitive. |
| A workflow fails | Use actionlint's file and line diagnostic to fix the YAML or expression. |
| A check is pending | Confirm the job name, inspect its run, and update the PR from `main`. |
| A tool download fails | Check the Actions log for a network error, then rerun the failed job. |

Do not add `continue-on-error` or disable a required check to make a failing
validation appear successful.

## Add application checks when source is published

Once the actual application and lockfile are committed, extend CI to install
dependencies from the lockfile and run the project's real lint, type checking,
unit tests, and production build. Add a disposable database service if tests need
one. Provider calls should use test fixtures instead of live credentials or
billable APIs.

Keep `repository-checks` as a stable required gate. If checks move into separate
jobs, make the gate depend on them and explicitly fail when any required job fails
or is unexpectedly skipped. Test that failure path before changing protection.

## Security and maintenance

CI has read-only repository permissions, does not persist Git credentials, and
does not need any user-supplied secrets. It uses `pull_request`, not privileged
`pull_request_target`, for contributor changes.

Actions are pinned to full commit SHAs. [Dependabot](../.github/dependabot.yml)
checks those action pins weekly. Review and test update PRs before merging. Also
review the explicit lychee and actionlint versions; updating actionlint requires
updating its archive SHA-256 from the official release metadata. The workflow
verifies that checksum before extracting or running actionlint.

References:

- [GitHub workflow syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)
- [Markdownlint action](https://github.com/DavidAnson/markdownlint-cli2-action)
- [Lychee action](https://github.com/lycheeverse/lychee-action)
- [Actionlint usage](https://github.com/rhysd/actionlint/blob/main/docs/usage.md)
