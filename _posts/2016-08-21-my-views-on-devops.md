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

## Real World UseCases

> In the case of Netflix, Powell said, StackStorm is being used to monitor the Simple Queing Service (SQS) on Amazon Web Services where the implementation of Cassandra that Netflix uses resides. Powell said StackStorm sensors listen to SQS and issue triggers into the StackStorm internal message bus. A rules engine then listens to that message bus and does pattern matching to identify performance threshold violations. The system then checks to see if there is a tool that can be invoke to automatically remediate the problem. In cases where no solution to the problem can be found, StackStorm sends an alert to the appropriate admin.

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

- [Stackstorm at yc](https://news.ycombinator.com/item?id=10342000)
- [Stackstorm in Netflix](http://www.datacenterknowledge.com/archives/2015/09/24/netflix-to-use-stackstorm-for-it-automation-under-cassandra/)


