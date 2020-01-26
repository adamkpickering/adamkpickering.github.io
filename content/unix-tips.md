---
title: "Unix Tips"
date: 2020-01-26T16:30:29-07:00
draft: true
---

# Unix Tips

- The Art of Unix Programming is a big step towards joining
  the hacker culture for which Unix is famous.

- There are number of minilanguages that are useful to learn,
  and which are de facto standards. Often they are extended in incompatible ways,
  but the core is always the same. Some of these are:
  - regex
  - datetime formatting
  - C style string formatting
  - argument conventions with shell commands (-a -> all; -n -> number etc)

- Any time you see a command, file extension etc that looks like gibberish,
  try to find out why it is spelled like that.
  There is always a reason why things are named the way they are,
  and the process of learning the history will help you remember
  the command and how to use it.

- Knowing about the documentation is vital for any pursuit, especially computers.
  On Unix the dominant way of documenting the Unix-philosophy-adhering tools
  is man pages.
  - Concept of numbered binders referring to certain concepts (use `man man`
    as a reference when you forget)
  - To search the man pages for a concept use `man -k`
