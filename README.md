<!-- markdownlint-disable -->
<p align="center">
    <a href="https://github.com/CICDToolbox/">
        <img src="https://cdn.wolfsoftware.com/assets/images/github/organisations/cicdtoolbox/black-and-white-circle-256.png" alt="CICDToolbox logo" />
    </a>
    <br />
    <a href="https://github.com/CICDToolbox/awesomebot/actions/workflows/cicd.yml">
        <img src="https://img.shields.io/github/actions/workflow/status/CICDToolbox/awesomebot/cicd.yml?branch=master&label=build%20status&style=for-the-badge" alt="Github Build Status" />
    </a>
    <a href="https://github.com/CICDToolbox/awesomebot/blob/master/LICENSE.md">
        <img src="https://img.shields.io/github/license/CICDToolbox/awesomebot?color=blue&label=License&style=for-the-badge" alt="License">
    </a>
    <a href="https://github.com/CICDToolbox/awesomebot">
        <img src="https://img.shields.io/github/created-at/CICDToolbox/awesomebot?color=blue&label=Created&style=for-the-badge" alt="Created">
    </a>
    <br />
    <a href="https://github.com/CICDToolbox/awesomebot/releases/latest">
        <img src="https://img.shields.io/github/v/release/CICDToolbox/awesomebot?color=blue&label=Latest%20Release&style=for-the-badge" alt="Release">
    </a>
    <a href="https://github.com/CICDToolbox/awesomebot/releases/latest">
        <img src="https://img.shields.io/github/release-date/CICDToolbox/awesomebot?color=blue&label=Released&style=for-the-badge" alt="Released">
    </a>
    <a href="https://github.com/CICDToolbox/awesomebot/releases/latest">
        <img src="https://img.shields.io/github/commits-since/CICDToolbox/awesomebot/latest.svg?color=blue&style=for-the-badge" alt="Commits since release">
    </a>
    <br />
    <a href="https://github.com/CICDToolbox/awesomebot/blob/master/.github/CODE_OF_CONDUCT.md">
        <img src="https://img.shields.io/badge/Code%20of%20Conduct-blue?style=for-the-badge" />
    </a>
    <a href="https://github.com/CICDToolbox/awesomebot/blob/master/.github/CONTRIBUTING.md">
        <img src="https://img.shields.io/badge/Contributing-blue?style=for-the-badge" />
    </a>
    <a href="https://github.com/CICDToolbox/awesomebot/blob/master/.github/SECURITY.md">
        <img src="https://img.shields.io/badge/Report%20Security%20Concern-blue?style=for-the-badge" />
    </a>
    <a href="https://github.com/CICDToolbox/awesomebot/issues">
        <img src="https://img.shields.io/badge/Get%20Support-blue?style=for-the-badge" />
    </a>
</p>

## Overview

A tool to link check your files with [awesome_bot](https://rubygems.org/gems/awesome_bot).

This tool has been tested against the following:

1. GitHub Actions
2. Travis CI
3. CircleCI
4. BitBucket pipelines
5. Local command line

However due to the way that they are built they should work on most CICD platforms where you can run arbitrary scripts.

We provide a [script](https://github.com/CICDToolbox/get-all-tools) which pulls the latest copy of all the CICD tools and
places them in a local bin directory to allow them to be run any time locally for added validation.

## Basic Usage

```yml
jobs:
  build:
    name: Awesomebot
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the Repository
        uses: actions/checkout@v4

      - name: Set up Ruby 3.3
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: "3.3"

      - name: Run Awesomebot
        run: bash <(curl -s https://raw.githubusercontent.com/CICDToolbox/awesomebot/master/pipeline.sh)
```

### Configuration Options

The following environment variables can be set in order to customise the script.

| Name          | Default Value  | Purpose                                                                                                                                                                         |
| :------------ | :------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| INCLUDE_FILES | Unset          | A comma separated list of files to include for being scanned. You can also use `regex` to do pattern matching.                                                                  |
| EXCLUDE_FILES | Unset          | A comma separated list of files to exclude from being scanned. You can also use `regex` to do pattern matching.                                                                 |
| FLAGS         | Unset          | Any command line options that are valid for awesome_bot. (You can also use 'FLAGS: "default" to use the default set of flags that we have defined, see below for more details.) |
| NO_COLOR      | False          | Turn off the color in the output.                                                                                                                                               |
| REPORT_ONLY   | False          | Generate the report but do not fail the build even if an error occurred.                                                                                                        |
| SHOW_ERRORS   | True           | Show the actual errors instead of just which files had errors.                                                                                                                  |
| SHOW_SKIPPED  | False          | Show which files are being skipped.                                                                                                                                             |
| WHITELIST     | Unset          | A comma separated list of files to be excluded from being checked.                                                                                                              |

> If you set INCLUDE_FILES - it will skip ALL files that do not match, including anything in EXCLUDE_FILES.

You can use any combination of the above settings.

```yml
jobs:
  build:
    name: Awesomebot
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the Repository
        uses: actions/checkout@v4

      - name: Set up Ruby 3.3
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: "3.3"

      - name: Run Awesomebot
        env:
          REPORT_ONLY: true
          SHOW_ERRORS: true
        run: bash <(curl -s https://raw.githubusercontent.com/CICDToolbox/awesomebot/master/pipeline.sh)
```

### Default Flags

A set of default flags are used within the scan phase.

```shell
--allow-redirect --allow-dupe --allow-ssl --skip-save-results --set-timeout 10 --allow 429
```

* --allow-redirect = Redirected URLs are allowed
* --allow-dupe = Duplicate URLs are allowed
* --allow-ssl = SSL errors are allowed
* --skip-save-results = Skip saving results
* --set-timeout 10 = Set the timeout to 10 seconds (default is 30)
* --allow 429 = Allow HTTP error code 429 (Too Many Requests)

## Example Output

This is an example of the output report generated by this tool, this is the actual output from the tool running against itself.

```
--------------------------------------------------------------------- Stage 1: Parameters --
 Flags: --allow-redirect --allow-dupe --skip-save-results --set-timeout 10 --allow 429
---------------------------------------------------------- Stage 2: Install Prerequisites --
 [ OK ] awesome_bot is already installed
------------------------------------------------------ Stage 3: Run awesome_bot (v1.20.0) --
 [ OK ] .github/CODEOWNERS
 [ OK ] .github/CODE_OF_CONDUCT.md
 [ OK ] .github/CONTRIBUTING.md
 [ OK ] .github/dependabot.yml
 [ OK ] .github/FUNDING.yml
 [ OK ] .github/ISSUE_TEMPLATE/ask_question.yml
 [ OK ] .github/ISSUE_TEMPLATE/bug_report.yml
 [ OK ] .github/ISSUE_TEMPLATE/config.yml
 [ OK ] .github/ISSUE_TEMPLATE/feature_request.yml
 [ OK ] .github/PULL_REQUEST_TEMPLATE.md
 [ OK ] .github/SECURITY.md
 [ OK ] .github/workflows/cicd.yml
 [ OK ] .github/workflows/cicd.yml.old
 [ OK ] .github/workflows/citation-validation.yml
 [ OK ] .github/workflows/delete-old-workflow-runs.yml
 [ OK ] .github/workflows/dependabot.yml
 [ OK ] .github/workflows/document-validation.yml
 [ OK ] .github/workflows/generate-release.yml
 [ OK ] .github/workflows/generate-release.yml.old
 [ OK ] .github/workflows/generate-test-release.yml
 [ OK ] .github/workflows/generate-test-release.yml.old
 [ OK ] .github/workflows/greetings.yml
 [ OK ] .github/workflows/purge-deprecated-workflow-runs.yml
 [ OK ] .github/workflows/repository-validation.yml
 [ OK ] .github/workflows/security-hardening.yml
 [ OK ] .github/workflows/stale.yml
 [ OK ] .gitignore
 [ OK ] .yamllint
 [ OK ] CITATION.cff
 [ OK ] LICENSE.md
 [ OK ] README.md
------------------------------------------------------------------------- Stage 4: Report --
 Total: 31, OK: 31, Failed: 0, Skipped: 0
----------------------------------------------------------------------- Stage 5: Complete --
```

## File Identification

Target files are identified using the following code:

```shell
file -b "${filename}" | grep -qE '^(HTML document|ASCII text)'

AND

[[ ${filename} =~ \.(txt|md)$ ]]
```

<br />
<p align="right"><a href="https://wolfsoftware.com/"><img src="https://img.shields.io/badge/Created%20by%20Wolf%20on%20behalf%20of%20Wolf%20Software-blue?style=for-the-badge" /></a></p>
