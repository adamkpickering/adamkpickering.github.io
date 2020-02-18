---
title: "Unix Tips"
date: 2020-01-26T16:30:29-07:00
draft: true
---

If you're reading this, you're probably interested in Unix.
Or more likely Linux, which is of course a descendant of Unix.
Congratulations - Unix is not only what I believe to be the
oldest technical tradition in computing, but also the dominant one.
An understanding of Linux, and Unix, is very handy
in today's computerized world.

There are good reasons for Unix's success. Other than its popularity,
it emphasizes practicality and usability over elegance and feature
completeness. It is meant to be *used*, not to be a collection
of supposedly perfect abstractions. But this comes at a price:
as a newbie to the world of Unix might ask, how did all of this get
here? Why are these programs named what they are?
Where do I start learning about all of this?

Indeed, getting started can be difficult. But don't get discouraged!
As I have gone through the process of learning about Unix,
I've picked up a few tricks. The purpose of this page is to
compile them, so that others might benefit. Enjoy!


## Read *The Art of Unix Programming*

[The Art of Unix Programming](http://www.catb.org/esr/writings/taoup/)
is an excellent book for those looking to learn about Unix.
Despite its age (it came out in 2003), it gives
the reader a good idea of what the oft-referred-to "Unix philosophy" is.

In part I *The Art of Unix Programming* first gives a history of Unix -
I believe that knowing the history is an important, but often-overlooked,
prerequisite to expertise in a technical field. It provides a foundation
for the knowledge that is directly useful today. It allows one
to understand why things are the way they are, and not some other way.
*TAoUP*'s treatment of the history is adequate.

Part II is about the design choices that were made in Unix.
This part is great for getting you to think critically about
why Unix is the way it is, and why it is so successful.
It introduces you to the famous "Unix philosophy".
It discusses design patterns that you'll find in the Unix world.

Part III gives some general knowledge about software. This part is largely
out of date, with the exception being chapter 16, which provides
a convincing argument on why you, as a software user and developer,
should gravitate towards open-source software.

Finally, Part IV discusses community, the reason for Unix's existence.
Unix is intimately tied to the open-source world, and always has been.
Also standards, and their importance. Finally it discusses
why Unix is still around in its present format, and why it hasn't
been replaced by anything that has been developed since.

*TAoUP*, while outdated in some sections, is a wonderful place to
start as you dive into Unix. Note that the author, Eric Raymond,
has written some other classics such as *The Cathedral and the Bazaar*,
which are worth checking out.


## Use Mnemonics to Remember Commands and Their Arguments

When learning the CLI you will run into strangely-named commands.
At least, they will seem to be strangely-named... until you learn a
bit about them. Once you have learned what quirk or abbreviation
their name was based on, you'll be able to remember them more easily.

One example is the command `less`, which lets you scroll up and down
through another command's output. At first it seems impossible to find
a reason that `less` is named what it is. But if you read a bit about
the [history](https://en.wikipedia.org/wiki/Less_(Unix)) you'll discover
that `less` is the successor to `more`... because "less is more"!
`more` does the same thing as `less`, with the exception that
it doesn't allow you to scroll back up through the text.
And of course `more` is called what it is because you use it when you
want to read some of the input, and then read "more" when you are ready.
Whether it's actually funny doesn't matter - "less is more"
is a useful mnemonic to remember the command.

Not every command has a joke in its name, but often there is a story or
mnemonic there. So when you can't figure out why a command is named
what it is, take some time to research it. It's worth it!

- argument conventions with shell commands (-a -> all; -n -> number etc)
  - there is a link to this somewhere


## Learn Common Minilanguages

As you might have read in *The Art of Unix Programming*, minilanguages
are a great way to solve a problem efficiently. Indeed, minilanguages
are associated with many Unix programs. Here's a list of some of the
minilanguages I find useful in my day-to-day work with Unix:

  - regex (stick to Extended regex and stay away from language-specific
    extensions)
  - docopt
  - datetime formatting
  - C style string formatting
  - backus-naur

- used inside programming languages and unix tools



## Use the `man` Command

- Knowing about the documentation is vital for any pursuit, especially computers.
  On Unix the dominant way of documenting the Unix-philosophy-adhering tools
  is man pages.
  - Concept of numbered binders referring to certain concepts (use `man man`
    as a reference when you forget)
  - To search the man pages for a concept use `man -k`


## Learn How Unix Boots

This is a more challenging topic, but if you can understand how Unix boots
you'll have a good understanding of a variety of topics, including
filesystems, the separation between kernel space and user space,
the BIOS/UEFI, initrd/initramfs, the kernel ABI, and more.
After you understand how Unix boots, you'll be all the more familiar with it.
