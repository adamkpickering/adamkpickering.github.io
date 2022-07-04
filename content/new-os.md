---
title: "A New OS"
date: 2022-06-30T11:00:12-06:00
draft: true
---


For a while now I have been mulling over an idea for an operating system.
While I have been learning concepts that are relevant to it, I haven't had the chance to flesh it out.
So when it came time for the annual SUSE hack week, I figured it would be a fun thing to explore.
This post is the result of that exploration.
It was put together in somewhat of a rush, and I'm talking about complex stuff
that I don't understand completely, so my ideas are half-baked in many places.
But it should get the main idea across.
If you have any comments please send them my way!

Oh, and a caveat: we unfortunately can't rely on containers for security purposes yet.
This is according to [this freedesktop.org page].
So for now, anything built in the pattern described herein is experimental only!


### Motivation

I have always disliked Unix's fundamental security model.
By "fundamental security model", I mean the use of users, groups and
file modes to restrict access to files. Here are some reasons this is not great:

- It always seems to be getting in the way. There is no clean separation
  between the domain of unprivileged users and privileged users. For
  example, many routine tasks I might do as a regular user require running
  a command as root, i.e. installing a program using the system package manager.
  I am constantly having to run `sudo !!`. I can just do a `sudo su` and run
  everything as root, but that can be risky if I forget I'm working as root.

- "Users" end up being created for daemons that we want to lock down.
  Of course, these users are not "users" in the sense that most would think of them -
  the term "user" implies a human user. This has other implications, like that
  /etc/passwd contains a large number of entries. This is confusing - I would
  expect it to contain only the three or four entries that actually pertain to human users.

I'm not the only one that has noticed the shortcomings of this security model - 
the existence of SELinux, AppArmor, file ACLs, capabilities and seccomp are evidence of this.
These solutions are nice to have, but when looked at as a whole they are too low-level
for everyday use. They lack ease of use, and a unified way of thinking.
If I want to lock down a daemon, how exactly do I do that?
Do I create a user and group for it, and restrict file permissions so that
only it can access the files that pertain to it? What if there is a misconfigured
file elsewhere, that it could then access if compromised? Do I dive into SELinux
(which seems complicated) and come up with some special policies for it?
There is no clear paradigm for how security is done.

Modern Linux distributions have another problem: they get cluttered over time.
As you install programs, configure them, remove them and so on, a lot of cruft accumulates.
Some programs are bigger cuplrits than others (looking at you, Python).
I often feel a desire to start fresh, but the only proper way to do that is to
wipe your hard disk and make a fresh install.

Again, other people have noticed this problem.
Distributions have popped up that fix it by completely changing the paradigm of
Linux system administration.
Some examples of these distributions are NixOS and GoboLinux.
But these distros have a problem: they aren't popular.
It seems that if you want to actually solve this problem, you have to find
a way to do it that doesn't involve making radical changes to peoples' workflows.



### The Idea

You may have guessed where I'm going with this: containers.
Rather than thinking of a Linux system as one big system containing all
users, I want you to think of a Linux system as a hierarchy of containers.
In this setup, each container has exactly one user.
At the root of the hierarchy is the "root container". This container is
identical to Linux systems as you know them, except that the only user is root.
Then, for each user, there is a container that is a child container
of the root container (I will refer to these as "user containers" below).
Each daemon has its own container as well.
Each one of those containers can have their own children, and so on.

There is a problem: how does a user make changes to the root container?
This is something that must be possible - kernel modules must be loaded,
the boot process must be tweaked, iptables must be configured, and so on.
We get around this by providing a controlled mechanism for breaking out of
the current user container and into its parent.

In order to implement this mechanism, you need two things: a daemon running
in the parent container, and a CLI tool that the user runs in the child container
when they want to get into the parent container.
The daemon is responsible for creating and managing all containers that are children
of its container. Upon creation of a child container, it inserts a unix domain socket
into the child container's rootfs. It then monitors the socket, implementing a
protocol that allows the user to prove they have permission to access the parent container.
If successful, the user gets access to a shell prompt that is tunnelled through
the unix domain socket.

The above assumes you are working in a text-based shell, but a similar mechanism should be
possible with a graphical shell.


### How does this improve things?

Turning the OS into a hierarchy of containers is a powerful paradigm.
For most tasks, the user isn't burdened by the need to call `sudo` or understand
what privileges are appropriate for a given file, or understand what `umask` is.
If they can see the file, they can do whatever they want to it.
And there is a clear separation between working in the context of a regular user,
and working in the context of a superuser. Nor do we need to abuse the concept of users
to isolate daemons - they just go in their own container, and can access anything in it.
All of this happens without the creator of the containers having knowledge of how
they were created or how they provide isolation from other parts of the system -
this is taken care of by the tooling.

**It is also easy to start fresh - you ensure your home directory is backed up and blow away
the container. Then you can spin up a new container with the same home directory.**

Most importantly, these benefits come while keeping a number of things the same.
First of all is the FHS - everything can be structured the same in each container.
Another is the use of the system package manager.
The only real difference from traditional Linux distros is the way you escalate privileges:
`sudo` is replaced by the CLI tool that is used to get into the parent container.
The fact that it works so similarly to what people are used to means that it won't be
difficult for people to adopt an OS like this. Another important thing to note
is that performance shouldn't be impacted, even by several layers of nested containers,
because all containers are talking to the same kernel.


### Components

#### The Daemon

The main job of the daemon is to be a "Container Manager" as described on
[this link on freedesktop.org]. It manages all child containers of the container
in which it runs, and registers them with with `systemd-machined` so that we can
use the usual tools (`ps`, `systemctl`) to get info about child containers.
The daemon runs as a systemd service and maintains a unix domain socket
over which the CLI can talk to it. It also maintains and listens on a unix domain
socket for each child container, to facilitate users in that container escalating.


#### The CLI

Things it does:
- getting a shell in parent container
- creating a user or daemon container in current one
- listing containers
- matrctl commands:
  - start
  - stop/poweroff
  - restart
  - create
  - delete
  - list
  - get
  - escalate/sudo/su


#### pam_matryoshka.so

Put something here.


### Login Process

1. Keyboard input on TTY.
1. `getty` invokes a special version of `login`.
1. `login` calls PAM modules. One of these, `pam_matryoshka.so`, authenticates the user
    against MatryoshkaOS's user records.
1. `login` executes the necessary code to place itself into the user's container, and
    then execs into their chosen shell.

It may be necessary to take the special logic out of the special version of `login`,
and put it into `pam_matryoshka.so`. This is because `pam_matryoshka.so` will probably
need to be called for other programs which log a user in via PAM modules, such as
`sshd`.


### Why Use systemd for this?


### Pointer to Fitting Everything Together

### Facilitated by systemd and Friends

For some time now I have wondered what it would look like if we built
a distro based primarily on systemd, while removing the historical cruft
from legacy init systems and other replaced tooling.

This might sound like a lot of work to implement.
It would be, but the systemd project has been hard at work creating
building blocks for modern OS's for some time now.
Say what you want about systemd, but it is a powerful standard in the
Linux world and provides a lot of useful things.
Maybe it's time to take a look at what it can do for us?

As it turns out, what it can do for us is a lot.
Recently, Lennart Poettering posted [Fitting Everything Together].
For those who haven't read it, it describes a methodology for building
improved Linux-based operating systems.
I'll try to avoid describing the ideas therein in any great detail here,
but you may find you need to give it a read if there is something here
that isn't making sense.

The services we are most interested in for the purposes of this post
are systemd-nspawn and systemd-machined.


### Future Directions

- how does networking work
- how does a user container work with a GUI
  - each user container has its own GUI setup
  - access to parent containers is through CLI only
- how *exactly* does login work (with PAM modules)
  - how does a login with openssh work
  - with a GUI
- implement many things as in [Fitting Everything Together]
  - immutability of root container
  - is there no /home in the root container (using `systemd-homed`)
- what parts of systemd need to be cut out?
- what does the daemon use for a container runtime?

[this freedesktop.org page]: https://www.freedesktop.org/wiki/Software/systemd/ContainerInterface/
[Fitting Everything Together]: https://0pointer.net/blog/fitting-everything-together.html
[this link on freedesktop.org]: https://www.freedesktop.org/wiki/Software/systemd/writing-vm-managers/
