---
layout: post
title: Being idiomatic: Do I care ?
---

## Being idiomatic: Do I care ?

The topic of discussion is based out of below stackoverflow post.
[Is doing x a good thing in language y ?](http://stackoverflow.com/questions/16138232/is-it-a-good-practice-to-use-try-except-else-in-python)

It is a good topic. A very debatable one. I shall distribute some my learnings
nonetheless.

> From my experience, use the language with the language's IDIOMATIC approach.
> However when I need to make a choice between being idiomatic vs. DRY, readable,
> & testable code; I would prefer latter.

<br/>

### What is idomatic ?

- Being peculiar to or characteristic of a particular language or dialect

Earlier, I tried to use functional paradigm in non-functional language (read Java)
to do amazing stuff. However, at the end of day, it seemed like a brilliant piece
of code which is difficult to be understood by mere mortals.

As you know, this is not good for software maintainability & other stuff.

My take, is "In Rome behave like Romans".
If you do not like the baggage of a language you should try for better languages.

<br />

### Adding few facts - The problem I see with scala.

- They have been trying to move the Java to Haskell like stuff in JVM.
- The articles & papers were so tough to understand by me.
- I had to resort to learn Haskell to understand.

This is where I felt scala is forcing me to do things in unnatural ways.

> https://twitter.com/capotej/status/695393215264849920
> scala career timeline:
> year 1: dope, gonna write some terse code
> year 2: hmm, maybe i shouldnt use every feature
> year 3: java 8 looks nice

<br />

### Any opinions on golang

It is a different animal. It forces you to be idiomatic at least till now.
Do not expect brilliant pieces of code. This simplistic approach has been its success.
It even accepts the fact that "it is created for lesser programmers" in a
nonchalant manner. Its type system is not at all verbose when compared to Java.
Having a bit of experience in Groovy, I can say Golang treads a fine balance between
Java & Groovy.

<br />

### Ending with Rob Pike's quotes:

> The key point here is our programmers are Googlers, they’re not researchers.
> They’re typically, fairly young, fresh out of school, probably learned Java, maybe
> learned C or C++, probably learned Python. They’re not capable of understanding a
> brilliant language but we want to use them to build good software. So, the language
> that we give them has to be easy for them to understand and easy to adopt.

> It must be familiar, roughly C-like. Programmers working at Google are early in
> their careers and are most familiar with procedural languages, particularly from
> the C family. The need to get programmers productive quickly in a new language
> means that the language cannot be too radical.
