<p align="center">
    <a href="https://github.com/CICDToolbox/">
        <img src="https://cdn.wolfsoftware.com/assets/images/github/organisations/cicdtoolbox/black-and-white-circle-256.png" alt="CICDToolbox logo" />
    </a>
    <br />
    <a href="https://github.com/CICDToolbox/awesomebot/actions/workflows/cicd-pipeline.yml">
        <img src="https://img.shields.io/github/workflow/status/CICDToolbox/awesomebot/CICD%20Pipeline/master?style=for-the-badge" alt="Github Build Status">
    </a>
    <a href="https://github.com/CICDToolbox/awesomebot/releases/latest">
        <img src="https://img.shields.io/github/v/release/CICDToolbox/awesomebot?color=blue&label=Latest%20Release&style=for-the-badge" alt="Release">
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
    <br />
    <a href="https://wolfsoftware.com/">
        <img src="https://img.shields.io/badge/Created%20by%20Wolf%20Software-blue?style=for-the-badge" />
    </a>
</p>

## Overview

A tool to link check your files with [awesome_bot](https://rubygems.org/gems/awesome_bot) in CI/CD pipelines.

This tool has been written and tested using GitHub Actions but it should work out of the box with a lot of other CI/CD tools.

## Usage

```yml
jobs:
  build:
    name: Awesomebot
    runs-on: ubuntu-latest
    steps:
      - name: Checkout the code
        uses: actions/checkout@v3
      - name: Set up Ruby 3.1
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.1
      - name: Run Awesomebot
        run: bash <(curl -s https://raw.githubusercontent.com/CICDToolbox/awesomebot/master/pipeline.sh)
```

### Other Options

The following environment variables can be set in order to customise the script.

| Name          | Default Value  | Purpose                                                                                                                                                                         |
| :------------ | :------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| EXCLUDE_FILES | Unset          | A comma separated list of files to exclude from being scanned. You can also use `regex` to do pattern matching.                                                                 |
| FLAGS         | Unset          | Any command line options that are valid for awesome_bot. (You can also use 'FLAGS: "default" to use the default set of flags that we have defined, see below for more details.) |
| NO_COLOR      | False          | Turn off the color in the output.                                                                                                                                               |
| REPORT_ONLY   | False          | Generate the report but do not fail the build even if an error occurred.                                                                                                        |
| SHOW_ERRORS   | True           | Show the actual errors instead of just which files had errors.                                                                                                                  |
| SHOW_SKIPPED  | False          | Show which files are being skipped.                                                                                                                                             |
| WHITELIST     | Unset          | A comma separated list of files to be excluded from being checked.                                                                                                              |

You can use any combination of the above settings.

```yml
jobs:
  build:
    name: Awesomebot
    runs-on: ubuntu-latest
    steps:
      - name: Checkout the code
        uses: actions/checkout@v3
      - name: Set up Ruby 3.1
        uses: ruby/setup-ruby@v1
        with:
           ruby-version: 3.1	
      - name: Run Awesomebot
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
-------------------------------------------------------------------------- Stage 1 - Parameters --
 Excluded: CHANGELOG.md
 Flags: --allow-redirect --allow-dupe --allow-ssl --skip-save-results --set-timeout 10 --allow 429
 Show skipped: true
 Whitelist: https://img.shields.io
--------------------------------------------------------------- Stage 2 - Install Prerequisites --
 [  OK  ] gem install --quiet awesome_bot
----------------------------------------------------------- Stage 3 - Run awesome_bot (v1.20.0) --
 [  OK  ] .github/CODEOWNERS
 [  OK  ] .github/CODE_OF_CONDUCT.md
 [  OK  ] .github/CONTRIBUTING.md
 [  OK  ] .github/FUNDING.yml
 [  OK  ] .github/ISSUE_TEMPLATE/ask_question.yml
 [  OK  ] .github/ISSUE_TEMPLATE/bug_report.yml
 [  OK  ] .github/ISSUE_TEMPLATE/config.yml
 [  OK  ] .github/ISSUE_TEMPLATE/feature_request.yml
 [  OK  ] .github/PULL_REQUEST_TEMPLATE.md
 [  OK  ] .github/SECURITY.md
 [  OK  ] .github/workflows/pipeline.yml
 [  OK  ] .gitignore
 [ Skip ] CHANGELOG.md
 [  OK  ] LICENSE.md
 [  OK  ] README.md
 [  OK  ] VERSION.txt
 [  OK  ] stale.yml
------------------------------------------------------------------------------ Stage 4 - Report --
 Total: 17, OK: 16, Failed: 0, Skipped: 1
---------------------------------------------------------------------------- Stage 5 - Complete --
```

## File Identification

Target files are identified using the following code:

```shell
file -b "${filename}" | grep -qE '^(HTML document|ASCII text)'

AND

[[ ${filename} =~ \.(txt|md)$ ]]
```
