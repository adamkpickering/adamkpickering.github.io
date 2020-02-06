---
title: "Unix Tips"
date: 2020-01-26T16:30:29-07:00
draft: true
---

If you're reading this, you're probably interested in Unix.
Or more likely Linux, which is of course a descendant of Unix.
Congratulations - Unix is not only what I believe to be the
oldest technical tradition in computing, but also the dominant
(as of 2020) one. There are good reasons for Unix's success:
first came its emphasis on practicality rather than elusive elegance
and feature completeness; and then its (no doubt related) adoption by
the free and open source software community.

But getting started with it can be difficult.
Documentation tends to be scattered across the internet,
out-of-date, or nonexistent. But don't get discouraged!
While I am nowhere close to being an expert on Unix,
I have learned a few things up to this point that might help
a newbie out. So, I have compiled them here. Enjoy!


## Read *The Art of Unix Programming*

*The Art of Unix Programming* is an excellent book for those looking
to learn about Unix. Despite its age, it gives the reader a good idea of
what the of-referred-to "Unix philosophy" is. Along the way, it provdes
a good history of Unix,
as well as its relation to the computing community. I believe that to
know any field well, you must also know the history of that field.
Knowing the history of Unix allows you to understand the reasons behind
certain design decisions. It might even keep you from repeating the
mistakes of others!

In addition to history *TAoUP* discusses design. It is a good introduction
to how to think about how programs fit together in the real world.

Also introduces the idea that there is a best tool for every job.

Introduces the idea of mechanism vs policy.


## Understand the Origins of Common CLI Programs

When learning the CLI you will run into commands whose function
appears to have nothing to do with their names. This can make it difficult
to remember what the command is for. Going back to the idea of learning the
history, often there is a reason that the commands have the names they do.

The best example is the command `less`, which lets you scroll up and down
through another command's output. At first it seems impossible to find
a reason that `less` is named what it is. But if you read a bit about
the [history](https://en.wikipedia.org/wiki/Less_(Unix)) you'll discover
that it is the successor to `more`, which does the same thing as `less`
but it doesn't allow you to scroll back up through the text. So the name `less`
comes from the joke that `less` is the opposite of `more`. Whether or not
it's actually funny doesn't matter - it is a useful mnemonic to remember the command.

Not every command has a joke in its name, but often there is a story or
mnemonic there. I will leave it to you to discover them.


## Discover and Understand Common Minilanguages


- There are number of minilanguages that are useful to learn,
  and which are de facto standards. Often they are extended in incompatible ways,
  but the core is always the same. Some of these are:
  - regex
  - datetime formatting
  - C style string formatting
  - argument conventions with shell commands (-a -> all; -n -> number etc)
    - there is a link to this somewhere
  - backus-naur
  - docopt

- Knowing about the documentation is vital for any pursuit, especially computers.
  On Unix the dominant way of documenting the Unix-philosophy-adhering tools
  is man pages.
  - Concept of numbered binders referring to certain concepts (use `man man`
    as a reference when you forget)
  - To search the man pages for a concept use `man -k`
