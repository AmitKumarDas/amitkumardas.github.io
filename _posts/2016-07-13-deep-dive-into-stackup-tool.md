---
layout: post
title: Deep dive into Stack Up deployment tool
---

# What is ```Stack Up``` ?

> Stack Up is marketed as a tool that can run specified set of commands on multiple
> hosts. You can think it in similar lines with Ansible, Fabric etc. The site is
> [here](https://github.com/pressly/sup). It is also known as sup & has settings
> in a yaml based configuration file known as supfile.

<br />

# What makes ```Stack Up``` so special ?

> I find sup is really good at creating customized commands & allowing placement
> of command's implementation in the run, local, etc tags. This lets one to expose
> reusable shell snippets as customized commands. In addition, the interactive mode
> provides the ability to pass prepared commands via sup. To summarize, I find this to
> be an effective replacement of building aliases, plugins, etc. found in oh-my-zsh
> bash-it, etc. shell based libraries.

<br />

# Features of ```Stack Up```:

> Note: Some of these features may be a work-in-progress.

- Create your own commands.
  - Command name & its implementations are written in the supfile.
  - There can be multiple commands in a single supfile.
  - The implementations are unix commands or cli commands that runs over shell.
- Leverage shell to the maximum.
  - You get to use the unix tools, pipe, etc... without any changes.
- Ability to execute commands conditionally.
  - [link](https://github.com/pressly/sup/issues/76#issuecomment-217142451)
- Ability to create categorized sup files & refer them from a top supfile.
  - [link](https://github.com/pressly/sup/issues/76#issuecomment-217170041)
- Write logic in a flow based programming style.
  - [link](https://github.com/pressly/sup/issues/42)

<br />

# Similar stuff elsewhere

- [Turtle](https://github.com/Gabriel439/Haskell-Turtle-Library)
- [invoke](https://github.com/pyinvoke/invoke)

<br />

# FAQs:

- Will the ```local``` tag eliminate the need for ```networks``` tag ?
