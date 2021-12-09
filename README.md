# Garage week 2021
## OpenAPI Spec
This repo contains example OpenAPI 3.0 spec and GitHub workflow for SDKs generation.

## SDK Generation Pipeline
When schema is changes, GitHub workflow starts SDK generation using [oco-research/sdk-generator](https://github.com/oco-research/sdk-generator) action.

### Action input params
```
  generator:
    description: The name of the generator (i.e. the language to generate the client library in) to use e.g. "typescript-angular"
    required: true
  github-domain:
    description: GitHub domain to push the generated SDK
    required: false
    default: github.com
  github-owner:
    description: GitHub organization to push the generated SDK
    required: true
  github-repo:
    description: GitHub repo to push the generated SDK
    required: true
  github-branch:
    description: GitHub repo branch to push the generated SDK
    required: true
  github-token:
    description: GitHub user token to push the generated SDK
    required: true
  package-token:
    description: Package system token to publish the generated SDK package
    required: false
  package-username:
    description: Package system username to publish the generated SDK package
    required: false
  config-path:
    description: Path to folder where generator configurations reside (without trailing slash)
    required: false
    default: .openapi-generator/generators
  schema-file:
    description: Path to schema file
    required: false
    default: openapi.json
  github-user-email:
    description: GitHub user email for commits
    required: false
    default: oco-bot@adobe.com
  github-user-name:
    description: GitHub user name for commits
    required: false
    default: Adobe OCO Bot
```
### Example
This Workflow example will generate `javascript`, `php` and `java` SDKs
```
jobs:
  generate:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        include:
          - generator: javascript
            github-owner: oco-research
            github-repo: nodejs-sdk
            github-branch: main
            github-token: REMOTE_GIT_TOKEN
            package-token: REMOTE_NPM_TOKEN
          - generator: php
            github-owner: oco-research
            github-repo: php-sdk
            github-branch: main
            github-token: REMOTE_GIT_TOKEN
            package-token: REMOTE_PACKAGIST_API_TOKEN
            package-username: REMOTE_PACKAGIST_USERNAME
          - generator: java
            github-owner: oco-research
            github-repo: java-sdk
            github-branch: main
            github-token: REMOTE_GIT_TOKEN

    steps:
      - name: Generate ${{ matrix.generator }} SDK
        uses: oco-research/sdk-generator@v1
        with:
          generator: ${{ matrix.generator }}
          github-owner: ${{ matrix.github-owner }}
          github-repo: ${{ matrix.github-repo }}
          github-branch: ${{ matrix.github-branch }}
          github-token: ${{ secrets[matrix.github-token] }}
          package-token: ${{ secrets[matrix.package-token] }}
          package-username: ${{ secrets[matrix.package-username] }}
```

### Addind new language
Simple add a configuration for a new language to the `matrix` workflow node.
You can add `openapi-generator` config if needed as well. Simple add config and templates files to `.openapi-generator` folder in your repository.
