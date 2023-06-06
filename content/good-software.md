---
title: "What makes good software?"
date: 2023-06-06T12:17:30-06:00
draft: true
---

In my opinion, there is one governing principle that can be used to
tell whether software is good or not. The principle is this: software
should do its thing and get out of the user's way. It should not
make their lives more difficult than necessary in any way. Rather,
it should make whatever aspect of their lives it pertains to quicker
and easier.

That doesn't mean that software should abstract over everything
and make everything simple and easy. If a given domain is complex,
the software should be capable of dealing with that complexity. It
should be up to the user to learn to work with the complexity that is
intrinsic to that domain.

So now that we've defined our governing principle, what does that mean
in practice?

The most obvious thing is that software has to work well from the user's
point of view. That means:

- It is reasonably lean and performant. It is snappy. It does not use
  more memory than makes sense for its purpose. It does not occupy
  more space on disk than it needs for its purpose.
- Its user interface should be easy to make sense. If it is a graphical
  application, it should have a well-designed interface. If it is a
  terminal application, it should follow whatever patterns are typical
  for its platform.
- It should be easy to install, and update.
- It should be easy to uninstall. When uninstalled, it should not leave
  orphaned files on the host system.
- It should be secure.
- It should be free of bugs.

But there is more to it than that. Any time a person decides to start
using a specific program, they are making a commitment to that sofware.
That commitment comes in the form of:

- time spent learning how to use the software
- data accumulated in the formats that the software uses

The problem is that users don't always want to continue using a piece
of software. Sometimes we want to switch. Sometimes we want to stop
using that type of software altogether. For software to work well
for a user, this process needs to be as easy as possible. This means:

- Software should not lock us in. This is a popular tactic for businesses
  to retain customers, but it is really an abuse of power.
- It should be possible to self-host the software if applicable. There is
  of course a place for organizations that host software for users.
  The important thing is that users have the choice as to whether to self-host.
- Any data software accumulates should be accessible to the user, and
  should be in a format that makes it easy to write scripts or tools
  to convert it into the formats other similar programs use.

There is another aspect that most users will never think about: it must be
easy to understand how the software works. It may not be obvious why this
is important. Here is why: what a piece of software looks
like under the hood facilitates the things I've listed above. If it
is harder to understand and work on, it will be less likely that the
software performs well, is secure, et cetera. So what does this entail?

- It must be open-source. You can't understand what your software is
  doing if you can't read the code!
- It must be written in a language that facilitates writing easy to read, debuggable
  code. That means Perl is not an option!
- It must be well designed and written. This facilitates future modifications,
  additions and fixes.
- Little or no technical debt.

Other points:
- Fits into a larger technical tradition (for example "unix philosophy")
- Supports Linux as a first-class citizen, not an afterthought
- Not excessively controlled by one entity
- Or, controlled by an entity I trust
- Privacy
- Has a process in place to deal with security issues
- Actively maintained
