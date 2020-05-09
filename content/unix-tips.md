---
title: "Unix Tips"
date: 2020-01-26T16:30:29-07:00
draft: false
---

If you're reading this, you're probably interested in Unix.
Or more likely Linux, which is of course a descendant of Unix.
Congratulations - Unix is both the oldest technical tradition
in computing, and the dominant one. An understanding of Linux,
and Unix, is very handy in today's computerized world,
and you'll be better for having it.

But Unix is more of a toolkit than it is a complete, functioning system.
On one hand, this buys its users power and flexibility.
But on the other hand, this gives it a steep learning curve.
Newbies can have a hard time figuring out how things are done in the Unix
world. But don't get discouraged! As I have learned (and continue to learn)
about Unix, I've noticed things that would have made the path easier had I known
them to begin with. I've recorded them here so that others might benefit.


## Read *The Art of Unix Programming*

[The Art of Unix Programming](http://www.catb.org/esr/writings/taoup/),
which is available for free online,
is an excellent book for those looking to learn about Unix.
While it is a very old book as books on computing go (it came out in 2003)
its focus on concepts, rather than details, means that the ideas are
just as relevant as they were at the time of release. Some may find it dry,
but there is no better introduction to the world of Unix.

In part I *The Art of Unix Programming* first gives a history of Unix.
I believe that knowing the history is an important, but often-overlooked,
prerequisite to expertise in a technical field - it gives you an awareness
of why things are the way they are, and might help you avoid mistakes
that others have made. Of course, *TAoUP* only covers the history up to 2003.
Personally I am not aware of a source which covers the history since then,
and have learned it from a variety of blog posts, articles etc.
You can do the same - some topics that you might look into are the `systemd`
controversy, the jump to mobile (via Android), the continued lack of adoption
of Unix on the desktop, and containers.

Part II is about the design choices that were made in Unix.
This part is great for getting you to think critically about
why Unix is the way it is, and why it is so successful.
It introduces you to the famous "Unix philosophy", and
discusses design patterns that you'll find in the Unix world.
Finally it introduces you to
[argument naming conventions](http://catb.org/~esr/writings/taoup/html/ch10s05.html),
which are helpful as a mnemonic device.

Part III gives some general knowledge about software. This part is largely
out of date, with the exception being chapter 16, which provides
a convincing argument on why you, a software user and developer,
should prefer open-source software whenever you have the choice.

Finally, Part IV discusses the Unix community, which is an important
but easily-overlooked part of the picture. It also discusses
standards, and their importance. Finally it discusses
why Unix is still around in its present format, and why it hasn't
been replaced by anything that has been developed since.

*TAoUP*, while outdated in some sections, is a great place to
establish some context for your journey into Unix. If you like it,
you might check out other works by the author, Eric Raymond,
particularly *The Cathedral and the Bazaar*.


## Learn the Backstory Behind Commands

When learning the CLI you will run into strangely-named commands.
At least, they will seem to be strangely-named at first. But once you
learn how their name came about, everything will make sense.

One example is the command `less`, which lets you scroll up and down
through another command's output. At first it seems impossible to find
a reason that `less` is named what it is. But if you read a bit about
the [history](https://en.wikipedia.org/wiki/Less_(Unix)) you'll discover
that `less` is the successor to `more`... because "less is more"!
`more` does the same thing as `less`, with the exception that
it doesn't allow you to scroll back up through the text.
And of course `more` is called what it is because you use it when you
want to read some of the input, and then read "more" when you are ready.

The lesson is that oddly-named commands usually have some kind of backstory.
Learning it will help you remember what the command does.


## Learn Common Minilanguages

Another point that is covered in *TAoUP* is that minilanguages
are a great way to solve a problem efficiently. They're frequently used
in the Unix world, not only in CLI programs, but also in programming
languages. Here's a list of some of the minilanguages I find useful in my
day-to-day work with Unix:

  - regex (learn [extended regex](https://regular-expressions.mobi/posix.html?wlr=1)
    and stay away from language-specific extensions, which are not portable
    between tools and thus a waste of time to learn)
  - [docopt](http://docopt.org/), a language for description of command interfaces
  - datetime formatting as per the `date` command, which is used in several
    programming languages
  - C-style string formatting, which is also used in several programming languages


## Know and Use the `man` Command

Unix descendants have extensive documentation which is accessed via the
`man` command. It may seem archaic to read documentation on the command line
rather than looking it up on the web, but it is often quicker to type the `man`
command (not to mention the fact the `man` allows searching for a string in
the documentation). 90% of the time I have a question about
a specific thing on a Unix system, the answer can be found with a thorough
search through a man page.

`man` documents itself. So when you do `man man`, you'll see a bunch of info about it.
One thing that is notable is that each section of the documentation is numbered.
In the wild, you might the `ls` command referred to as `ls(1)`.
The `(1)` part is the section covering that type of thing in the OS.
So we have section 1 for CLI commands, section 5 for file formats and conventions,
section 2 for system calls, and so on. For more information you can check out
[this stackexchange question](https://unix.stackexchange.com/questions/3586/what-do-the-numbers-in-a-man-page-mean).

Finally, as one of the answers says in that stackexchange question, you can
search for man pages using `man -k`. This is a great way to
find man pages when you're not 100% sure what you're looking for.
