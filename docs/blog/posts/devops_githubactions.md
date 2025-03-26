---
date: 2024-09-20  
categories:
- Devops
readtime: 20  
authors:
- rolo
---

# `Github Actions`: Automate Your Way to Success in Development

## The problem
In today’s fast-paced software development world, teams are constantly under pressure to deliver high-quality applications quickly. However, as applications grow in complexity, manual processes often become a bottleneck, leading to errors, delays, and inconsistent releases. This is where DevOps practices come into play, offering solutions to streamline and automate these processes.

Let’s split the problem into two distinct but intertwined realms: Continuous Integration (CI) and Continuous Deployment (CD). These practices are designed to help development teams avoid common pitfalls and improve the speed and reliability of software delivery.


### Continuous Integration (CI)
Humans are prone to errors: we forget dependencies, make linting mistakes, and work with complex systems that have interdependencies—especially in large teams. These mistakes can lead to technical debt, production bugs, and negative impacts on end users, which is the last thing any developer wants.

### Continuous Deployment (CD)
With CD, the challenge is automating software updates. In a typical CD process, code is automatically built, tested, and then deployed. Manual intervention introduces risk, slows things down, and leaves room for mistakes. Automating the deployment pipeline ensures faster and more reliable software delivery.

## Cast of characters 
### Workflows
A workflow is a configurable automated process consisting of one or more jobs. These workflows are defined in `.github/workflows` and can be triggered by a variety of events. Common workflows include:

- Building and testing pull requests.
- Deploying applications after a release.
- Automatically adding labels to new issues.

### Events
Events are activities in a repository that trigger workflows. Examples include:

- Pushing code.
- Opening a pull request.
- Creating a new issue.
- Workflows can also be triggered manually or on a predefined schedule. For a full list, see [GitHub’s documentation on Events](https://docs.github.com/en/rest/using-the-rest-api/github-event-types?apiVersion=2022-11-28) that trigger workflows.

### Jobs
A job is a collection of steps executed on the same runner. Each step can either be a shell script or an action that is part of the workflow. Steps are executed in order, and you can share data between them.

Jobs run in parallel unless configured otherwise, meaning independent jobs can speed up processes. For example, you can have parallel build jobs for different architectures followed by a packaging job that only starts after all builds finish successfully.

### Actions
An action is a reusable application that performs specific tasks. Actions help reduce repetitive code in workflows. Common actions include:

- Pulling your Git repository.
- Setting up toolchains for building.
- Authenticating with cloud providers.

You can write custom actions or use those available in the GitHub Marketplace.

### Runners
A runner is a server that executes your workflows. [GitHub provides runners](https://docs.github.com/en/actions/using-github-hosted-runners/using-github-hosted-runners/about-github-hosted-runners) for Ubuntu, Windows, and macOS, which run in isolated virtual machines.

You also have the option to host your own runner if you need a specific OS or hardware configuration.


## Example of Usage

Here's an example of a pipeline configuration for deploying a static website using GitHub Actions. This pipeline includes a `workflow` that automates the deployment process whenever code is pushed to the `main` or `master` branch.

The workflow is triggered by two specific events: a push to the `main` or `master` branch. When triggered, the pipeline runs a `job` with several `steps`, each using `actions` to configure credentials, fetch the code, install dependencies, and deploy the website.

```yaml
#.github/workflows/action.yml
name: CD
on:
  push:
    branches:
      - master
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Configure Git Credentials
        run: |
          git config user.name <cool_action_name>
          git config user.email <cool_action_email>
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV
      - uses: actions/cache@v4
        with:
          key: mkdocs-material-${{ env.cache_id }}
          path: .cache
          restore-keys: |
            mkdocs-material-
      - run: pip install mkdocs-material[imaging] && pip install mkdocs-material
      - run: mkdocs gh-deploy --force
```

For more examples and detailed information, check the [GitHub Actions Documentation](https://docs.github.com/en/actions/use-cases-and-examples/creating-an-example-workflow).


## Conclusion
This post aimed to introduce the basics of CI/CD and demonstrate how it can streamline your development process. By automating workflows like the one shown above, you can improve the speed and reliability of your deployments. CI/CD isn't just for large teams—it's a valuable tool for developers of all sizes and stages to enhance code quality and simplify deployment :). 