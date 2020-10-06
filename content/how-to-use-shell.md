---
title: "How to Use Shell Scripts"
date: 2020-09-01T19:30:03-07:00
draft: true
---

Shell scripting could be called the first glue language.
It gets a bad rap because it gives the user so much freedom.
In this article, I give my opinion on how shell scripting should
be used (and yes, it does have its place). I also give my opinion
on how it should not be used. Finally, I give a few tips for using
it, and a few pointers to helpful resources. This assumes that
you're already familiar with shell scripting, so if you aren't,
GO READ UP (fix this)!

Shell is a unique language in that it is a consequence of the CLI,
a way of manually and interactively telling a computer what to do.
I assume that the CLI was invented as a way of controlling computers,
and then someone thought, "what if I could take all of the
commands I'm typing manually, put them into a file, and then run that file
so that all of the commands ran without me having to do anything?"
No doubt, at the time this was invented it was a very useful thing.
But as these things go, somebody would have wanted variables just like
in C, but easier to use, which would have been added. And then someone
would have wanted if-else statements... and then do-while... and then
switch-case...

There is a problem with doing things this way: no matter how many
things you add to shell, it will still be shell. It will always have
the characteristics it originally was given to make it a good
**interactive** language. And these characteristics are going to cause
problems when you're using it non-interactively. So what are these
characteristics?

- Shell is parsed line-by-line, even when run as a script. That means
  that you don't get nice things like reports of syntax errors like
  you would in a more purpose-built scripting language like Python
  or Perl. It's frustrating to run a script and see it get halfway
  through, only to fail due to a silly error. Especially so if that
  script is large and took a long time to get to that point. Or if
  before it failed, the script did some things that will be difficult
  to reverse, or will cause the script to fail on subsequent runs.

- There are no exceptions, tracebacks, or real error handling like
  (again) a more purpose-built scripting language would have. Good
  luck debugging that 2000 line shell script that does a
  mission-critical task.

- There are no types beyond (kind of, and depending on the actual
  shell you're using) text and *maybe* numbers. Variables are really
  just buckets of data.

- As a result of the lack of data types, looping is based on spaces,
  which is error-prone. Also, you can't loop over anything more than
  one dimension without shell-specific extensions or hacks that make
  the script more prone to errors and hurt readability.

- Because of its long history, there are a variety of shells in use,
  each with its own behaviour and extensions. It goes without saying
  that this can cause problems when you tell your system to run
  a script with /bin/sh, but this one is new and /bin/sh points to
  a different shell. Even where there has been standardization,
  there are still problems (`read`).

- Beyond a few builtins, shell scripts mostly call external programs.
  These programs are not `import`ed, as in Python, or tracked as
  dependencies at all. The shell looks for the program in a number
  of predefined places and if it can't find it, your program fails.
  Moreover, there is no programmer-friendly way to break a shell
  script up into multiple files - yes, you can do `. <file_name>`,
  but this misses the semantics of Python's `import <module_name>`.

- Shell is full of pitfalls. Try `rm -rf "/${UNDEFINED_VAR}"` if you
  don't believe me (just kidding, don't actually try that).

- Because of the latitude shell gives you, readability can be poor
  if the author of a script doesn't care about it.

- Interactive user input is really not good (`read`). This can be
  hard to work around.

All of this makes shell bad for many tasks. Specifically, you'll want
to think twice about using shell if your task requires any of the following:

- lots of logic (if-else statements, do-while, switch-case)
- working with structured data (nested for loops, working with JSON beyond
  piping it from one command to another)
- a long script, or breaking your script up into multiple files
- a dependence on many external programs
- the script may fail for some reason, and you need to record detailed
  logs that will help someone debug the problem later

If you find yourself wanting any of the extensions that are provided
by bash, you're probably making a mistake by writing whatever you're
writing in shell. It doesn't matter what extensions are added - shell
is based on a foundation of interactive use, and will always be
hobbled by that for large, logic-dense applications. Use Python.
Or even Perl. Of course, the other problem with using extensions
is that you're now tied to a specific shell. Or worse, you have extensions
that do different things in different shells. Better to stick to plain
old POSIX shell - that way, when you find that the language is preventing
you from writing what you want to write, notice this, and rewrite
your script in a language better suited to your problem.

--------------------------------------------------------------------------------

Wow, that is a lot of problems. You must be wondering: "why are you
writing about shell in the first place? Why not vow never to use it
again?" Honestly, that's what I thought a few years ago. It turns
out that shell scripting has its niche. But actually, it has strengths
that make it really useful for a certain set of tasks. Some of these strengths are:

- familiar to anyone who already uses the CLI on a Unix-like OS
- lack of structured data is actually liberating in many cases
- willy-nilly nature means it can be adapted to many programs
  that think in different ways
- extremely small (was originally designed for machines with kB's
  of memory) because most functionality comes from external programs
- pipes let you link together unrelated programs to do powerful
  things (link about shell being faster than hadoop, but you
  shouldn't really use shell for data science)
- maps nicely to, and elegantly uses, unix process model
  - grouping commands
  - && and ||

So then, where should it be used? I believe that it shines in short
scripts that don't contain much logic, that are idempotent. It should
be looked at exclusively as a **glue** language, not a programming
language. You don't build things out of shell scripting. You link
together separate, domain-specific programs that are very good
at doing what they do. You use it as the **procedural** part of a process
of configuring **declarative** tools. Any logic that the shell script
contains should be fed, as directly as possible, into the input
of a better tool.

Examples:
- script to configure your dotfiles
- script to ensure a bunch of kubernetes resources exist on cluster
- init scripts
- really any configuration that you want to save as code
- basic data filtering for data science

Now for some tips. I like [shellhaters.org](https://shellhaters.org)
because it has great docs on all of POSIX shell scripting. It has been
my main resource while learning shell.

- use {VAR:?error msg}
- for any programs that are external to shell, check that they are there
  before starting into the main body of your script
- avoid the use of read - pass variables as args instead
- quote variable substitutions, which prevents errors due to spaces
  in vars
- learn and use &&, ||, and block syntax
- learn the three ways of declaring variables, and what they do
- comment each step, especially when you know you are writing a command
  that is going to be hard to read


- if the programs you're calling from shell have an option to produce
  output that facilitates parsing, use it
  - a weakness of shell is parsing output of commands... so just try
    to avoid this. Use shell for its strengths.

- There are many attempts to add data structures to shell. These
  attempts are missing the point. Shell is not for data structures.

- Goes nicely with declarative tools

- make scripts POSIX-compatible
  - what is the POSIX standard?

bash, ngs, fish, consh, ash, dash, tcsh, zsh, oil shell
