# Network Layer Repo

### Build Status

**TravisCI**
[![Build Status](https://travis-ci.org/cmmeyer/cfnlintdemo.svg?branch=master)](https://travis-ci.org/cmmeyer/cfnlintdemo)

### Intro
This repo has three templates used to build a simple network environment, plus a buildspec.yml file for CodeBuild and this README file including a build badge.
We're going to use this repo to walk through a basic demo of using cfn-python-lint to validate CloudFormation templates in Pull Requests prior to allowing
the changes into our repo.

Currently this repo does not validate for `Warnings`.

### Contents

```
.
├── README.md
├── buildspec.yml
├── config
│   └── networkconfig.json
└── templates
    ├── load-balancers.yaml
    ├── security-groups.yaml
    └── vpc.yaml
```

## Installation
``` bash

brew install pre-commit



```
## Simple Template
``` .pre-commit-config.yaml
# .pre-commit-config.yaml
repos:
-   repo: https://github.com/awslabs/cfn-python-lint
    rev: v0.15.0 # The version of cfn-lint to use
    hooks:
    -   id: cfn-python-lint
        files: templates/.*\.(json|yml|yaml)$
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v2.3.0
    hooks:
    -   id: check-yaml
        files: templates/.*\.(json|yml|yaml)$
    -   id: end-of-file-fixer
    -   id: trailing-whitespace

```


## Advance
``` .pre-commit-config.yaml
exclude: ^(.travis.yml|.pre-commit-config.yaml|.bumpversion.cfg)$
fail_fast: true
repos:
- repo: https://github.com/pre-commit/mirrors-isort
  rev: v4.3.21
  hooks:
  - id: isort
    # language_version: python3.6
- repo: https://github.com/ambv/black
  rev: 19.10b0
  hooks:
  - id: black
    exclude: regions_to_partitions.py
- repo: https://github.com/pre-commit/pre-commit-hooks
  rev: v2.4.0
  hooks:
  - id: check-case-conflict
  - id: end-of-file-fixer
  - id: mixed-line-ending
    args:
    - --fix=lf
  - id: trailing-whitespace
  - id: flake8
    additional_dependencies:
    - flake8-bugbear>=19.3.0
    - flake8-builtins>=1.4.1
    - flake8-commas>=2.0.0
    - flake8-comprehensions>=2.1.0
    - flake8-debugger>=3.1.0
    - flake8-pep3101>=1.2.1
    - flake8-print>=3.1.0
    # language_version: python3.6
  - id: pretty-format-json
    args:
    - --autofix
    - --indent=4
    - --no-sort-keys
  - id: check-merge-conflict
- repo: https://github.com/pre-commit/pygrep-hooks
  rev: v1.4.4
  hooks:
  - id: python-check-blanket-noqa
  - id: python-check-mock-methods
  - id: python-no-log-warn
- repo: https://github.com/PyCQA/bandit
  rev: 1.6.2
  hooks:
  - id: bandit
    files: "^taskcat/"
    args: ["--skip", "B322"]
- repo: https://github.com/pre-commit/mirrors-mypy
  rev: v0.761
  hooks:
  - id: mypy
    files: "^taskcat/"
- repo: local
  hooks:
  - id: update-schema
    name: update-schema
    description: generate schema from dataclasses
    entry: python generate_schema.py
    language: system
    pass_filenames: false
    always_run: true
  - id: git-secrets-register-aws
    name: git-secrets-register-aws
    description: Register AWS patterns with git-secrets
    entry: git-secrets --register-aws
    language: system
    always_run: true
    pass_filenames: false
  - id: git-secrets
    name: git-secrets
    description: Run git-secrets
    entry: git-secrets --scan
    language: system
    always_run: true
  - id: update-region-mappings
    name: update-region-mappings
    description: update region-partition mappings
    entry: ./update_partition_region_map.py
    language: system
    pass_filenames: false
    always_run: true
  - id: pylint-local
    name: pylint-local
    description: Run pylint in the local virtualenv
    entry: pylint "setup.py" "taskcat/" "bin/"
    language: system
    pass_filenames: false
    always_run: true
  - id: pytest-local
    name: pytest-local
    description: Run pytest in the local virtualenv
    entry: pytest --cov="./taskcat/"
    language: system
    pass_filenames: false
    always_run: true
```
