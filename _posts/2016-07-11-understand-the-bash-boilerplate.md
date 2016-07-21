---
layout: post
title: Understand the bash boilerplate
---

# Understand the bash boilerplate

- [bash boilerplate](https://github.com/alphabetum/bash-boilerplate).

<br />

## Usage

- Download the repo to your environment.
- Navigate to the repository location.

```bash

git clone https://github.com/AmitKumarDas/bash-boilerplate.git

# cd /home/damit/bash-boilerplate

# ls -ltr
total 88
drwxr-xr-x 2 root root  4096 Jun 30 14:59 test
-rw-r--r-- 1 root root 11785 Jun 30 14:59 README.md
-rw-r--r-- 1 root root  1082 Jun 30 14:59 LICENSE
-rw-r--r-- 1 root root  5043 Jun 30 14:59 helpers.bash
-rw-r--r-- 1 root root  3467 Jun 30 14:59 functions.bash
-rwxr-xr-x 1 root root 14377 Jun 30 14:59 bash-simple-plus
-rwxr-xr-x 1 root root  7293 Jun 30 14:59 bash-simple
-rwxr-xr-x 1 root root 26551 Jun 30 14:59 bash-commands
```

<br />

## Using bash-simple-plus

- Familiarize yourself with the valid as well as **invalid** usage.

```bash

valid# ./bash-simple-plus
Perform a simple operation.

valid# ./bash-simple-plus -x
Perform a simple operation with --option-x.

invalid# ./bash-simple-plus -y
â  Unexpected option: -y

valid# ./bash-simple-plus --option-x
Perform a simple operation with --option-x.

invalid# ./bash-simple-plus --option-u
â  Unexpected option: --option-u

valid# ./bash-simple-plus -o abc
Perform a simple operation.
Short option parameter: abc

valid# ./bash-simple-plus -oabc
Perform a simple operation.
Short option parameter: abc

invalid# ./bash-simple-plus -o abc def
Perform a simple operation.
Short option parameter: abc

invalid# ./bash-simple-plus -o abc -o def
Perform a simple operation.
Short option parameter: def

invalid# ./bash-simple-plus -oabc -odef
Perform a simple operation.
Short option parameter: def

invalid# ./bash-simple-plus -o=abc
Perform a simple operation.
Short option parameter: =abc

invalid# ./bash-simple-plus --long-option-with-argument
â  Option requires a argument: --long-option-with-argument

valid# ./bash-simple-plus --long-option-with-argument hello
Perform a simple operation.
Long option parameter: hello

valid# ./bash-simple-plus --long-option-with-argument hello -o abc
Perform a simple operation.
Short option parameter: abc
Long option parameter: hello

valid# ./bash-simple-plus --long-option-with-argument=hello -o abc
Perform a simple operation.
Short option parameter: abc
Long option parameter: hello

invalid# ./bash-simple-plus --long-option-with-argument=hello -o=abc
Perform a simple operation.
Short option parameter: =abc
Long option parameter: hello

valid# ./bash-simple-plus --debug
ðerforming operation...
ââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââ
Perform a simple operation.

valid# ./bash-simple-plus --debug -o abc
ðerforming operation...
ââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââ
Perform a simple operation.
Short option parameter: abc

valid# ./bash-simple-plus --debug --long-option-with-argument=hello -o abc
ðerforming operation...
ââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââââ
Perform a simple operation.
Short option parameter: abc
Long option parameter: hello
```

<br />

## Using bash-commands

- Familiarize yourself with the valid as well as **invalid** usage.

```bash

valid# ./bash-commands
... displays huge amount of good info...

valid# ./bash-commands help help
Usage:
  bash-commands help [<command>]

Description:
  Display help information for bash-commands or a specified command.

valid# ./bash-commands help commands
Usage:
  bash-commands commands [--raw]

Options:
  --raw  Display the command list without formatting.

Description:
  Display the list of available commands.

valid# ./bash-commands commands
Available commands:
  commands
  example
  help
  version

valid# ./bash-commands commands --raw
commands
example
help
version

valid# ./bash-commands help version
Usage:
  bash-commands ( version | --version )

Description:
  Display the current program version.

  To save you the trouble, the current version is 0.1.0-alpha

valid# ./bash-commands version
0.1.0-alpha

valid# ./bash-commands --version
0.1.0-alpha

valid# ./bash-commands help example
Usage:
  bash-commands example [<name>] [--farewell]

Options:
  --farewell  Print "Goodbye, World!"

Description:
  Print "Hello, World!"

valid# ./bash-commands  example
Hello, World!

valid# ./bash-commands  example AmitDas --farewell
Goodbye, AmitDas!

valid# ./bash-commands  example  --farewell
Goodbye, World!

invalid# ./bash-commands  example Amit Das --farewell
Goodbye, Amit!

valid# ./bash-commands  example "Amit Das" --farewell
Goodbye, Amit Das!
```
