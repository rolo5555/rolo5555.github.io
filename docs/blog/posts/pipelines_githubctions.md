---
date: 2024-09-20  
categories:
- CI/CD  
readtime: 20  
authors:
- rolo
---

# `Github Actions`: Automate Your Way to Success in Development

## The problem
Let’s split the problem into two distinct but intertwined realms: Continuous Integration (CI) and Continuous Deployment (CD).


### Continuous Integration (CI)
Humans are prone to errors: we forget dependencies, make linting mistakes, and work with complex systems that have interdependencies—especially in large teams. These mistakes can lead to technical debt, production bugs, and negative impacts on end users, which is the last thing any developer wants.

### Continuous Deployment (CD)
With CD, the challenge is automating software updates. In a typical CD process, code is automatically built, tested, and then deployed. Manual intervention introduces risk, slows things down, and leaves room for mistakes. Automating the deployment pipeline ensures faster and more reliable software delivery.

## Cast of characters 
### Workflows
A workflow is a configurable automated process consisting of one or more jobs. These workflows are defined in .github/workflows and can be triggered by events (such as a push or pull request) or run on a set schedule. Common workflows include:

Building and testing pull requests.
Deploying applications after a release.
Automatically adding labels to new issues.
You can even reference one workflow from within another for better reusability.

### Events
Events are activities in a repository that trigger workflows. Examples include:

Pushing code.
Opening a pull request.
Creating a new issue.
Workflows can also be triggered manually or on a predefined schedule. For a full list, see GitHub’s documentation on Events that trigger workflows.

### Jobs
A job is a collection of steps executed on the same runner. Each step can either be a shell script or an action that is part of the workflow. Steps are executed in order, and you can share data between them.

Jobs run in parallel unless configured otherwise, meaning independent jobs can speed up processes. For example, you can have parallel build jobs for different architectures followed by a packaging job that only starts after all builds finish successfully.

### Actions
An action is a reusable application that performs specific tasks. Actions help reduce repetitive code in workflows. Common actions include:

Pulling your Git repository.
Setting up toolchains for building.
Authenticating with cloud providers.
You can write custom actions or use those available in the GitHub Marketplace.

### Runners
A runner is a server that executes your workflows. GitHub provides runners for Ubuntu, Windows, and macOS, which run in isolated virtual machines.

You also have the option to host your own runner if you need a specific OS or hardware configuration.

For more information, see GitHub’s guide on self-hosted runners.

