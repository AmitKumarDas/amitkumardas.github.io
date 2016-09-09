---
layout: post
title: My views on devops
---

## Useful thoughts on DevOps

- Do we understand our painpoints ?
- Can we prioritize our painpoints ?
- Will solving the prioritized painpoint give us the maximum ROI ?
- Does the entire dev team checkin the source code daily ?
- Does the source code checkin consist of unit test checkin ?
- Is master branch the stable branch ?
- Does the dev checkin into master branch daily ?
- Does the dev care about building components with Single Responsibility Principle ?
- Does the dev care about composition of components instead of coupling ?
- Are we version controlling the operations config, logic & design decisions ?
- Are we able to version control the workflow definition ?
- Does the devops automation architecture support multiple language ?

## What does StackStorm provide ?

- YAML based workflow specs
- Implementation in any programming language
  - A workflow is a collection of actions
  - Actions can be written in any programming language/script
- Loops, conditions, subroutines handled via jinja2 (high level) & python (step level)
- Advantage over chef & puppet ?
  - st2 can compose chef & puppet.
  - Chef & Puppet can be used to manage state of a single node
- Advantages over terraform, juju & bosh
  - Terraform, juju & bosh are purpose built for app & infra deployments
- How about concourse ?
  - Stackstorm is not a CI solution but pretty good for orchestrating various automations points.
- Provides event updates as actions take place, intercept triggers & execute workflows

## Good Source for DevOps

- [stackstorm in yc](https://news.ycombinator.com/item?id=10342000)


