# SocialScope

[![Repository CI](https://github.com/Rohit-kumar-2674/socialscope/actions/workflows/ci.yml/badge.svg)](https://github.com/Rohit-kumar-2674/socialscope/actions/workflows/ci.yml)

Open-source social intelligence dashboard for analyzing public social signals,
trends and online momentum.

## Repository status

This repository currently contains documentation and CI configuration. Application
source code, a dependency manifest, and an application test suite have not been
published here yet. A passing CI badge validates the repository checks below; it
does not mean that the planned dashboard has been built or tested.

## Continuous integration

Every pull request runs the `repository-checks` job:

- Markdown formatting with markdownlint.
- Local documentation links and heading anchors with lychee.
- GitHub Actions workflow syntax and expressions with actionlint.

The same checks run after changes reach `main`, and can be started manually from
the Actions tab. There are no path filters that could leave a required check
waiting forever on unrelated changes.

See the [CI guide](docs/ci.md) for local commands, troubleshooting, branch
protection, and the application checks to add when the source code is published.
