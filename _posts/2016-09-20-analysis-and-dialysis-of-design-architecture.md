---
layout: post
title: Analysis & Dialysis of design, techniques & architecture
---

## Analysis & Dialysis of design, techniques & architecture

## Reverse Engineering st2 web interface design

refer - [st2web](https://github.com/StackStorm/st2web/)

<br />

#### st2-api

- st2-api consists of index.js & api.service.js

<br />

#### st2-history-child

- a folder/component that consists of
  - template.html, style.less, index.js, & history-child.directive.js
- index registers the directive & exports it as a angular module
  - export follows a namespace
  - e.g. ```main.modules.st2HistoryChild```
  - registration is done via name of the directive
- directive does the following:
  - sets certain well understood properties
  - sets task name in a ```clever way``` into the scope
  - taskname is in-turn a function that has the core logic
- template does the following:
  - lot of control structures seen in the template
  - especially the ng-if structures

<br />

#### st2-action-reporter

- directive does the following:
  - implements the Reporter & Traceback functions into the scope
  - These scope.Reporter & scope.Traceback functions are closures to link property
  - link seems to be a well understood property by st2web

<br />

#### queries in st2web

- $event.stopPropagation()
- this.$apply() & bind(this)


## Java's move towards being functional
