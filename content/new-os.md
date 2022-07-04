---
title: "A New OS"
date: 2022-06-30T11:00:12-06:00
draft: false
---


For a while now I have been mulling over an idea for a Linux distribution.
While I have been learning concepts that are relevant to it, I haven't had the chance to flesh it out.
So when it came time for the annual SUSE hack week, I figured it would be a fun thing to explore.
This post is the result of that exploration.
It was put together in somewhat of a rush, and I'm talking about complex stuff
that I don't understand completely, so my ideas are half-baked in many places.
But, it should get the main idea across.
If you have any comments please send them my way!

Oh, and a caveat: according to [this freedesktop.org page] we can't rely on
containers for security purposes yet. So for now, anything built in the
pattern described here is experimental only!


## Motivation

I have always disliked Unix's fundamental security model.
By "fundamental security model", I mean the use of users, groups and
file modes to restrict access to files. For one, it always seems to be
getting in the way. There is no clean separation between the domain of
unprivileged users and privileged users. For example, many routine tasks I
might do as a regular user require running a command as root,
i.e. installing a program using the system package manager.
I am constantly having to run `sudo !!`. I can just do a `sudo su` and run
everything as root, but that can be risky if I forget I'm working as root.

Another problem is that "users" end up being created for daemons that we want to lock down.
Of course, these users are not "users" in the sense that most would think of them -
in my mind at least, the term "user" implies a human user. So we have, for example,
/etc/passwd, which contains a large number of entries, only a few of which correspond to human users.
Yet a reasonable person would assume that it would contain only the three or four entries
that actually pertain to human users.

I'm not the only one that has noticed the shortcomings of this security model - 
the existence of SELinux, AppArmor, file ACLs, capabilities and seccomp are evidence of this.
These solutions are nice to have, but when looked at as a whole they are too low-level
for everyday use. They lack ease of use; they lack a unified way of thinking.
For example, if I want to lock down a daemon, how exactly do I do that?
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
Distributions that fix it have been created.
Usually they do so by completely changing the paradigm of Linux system administration.
Some examples of these distributions are NixOS and GoboLinux.
But these distros have a problem: they aren't popular.
It seems that if you want to actually solve this problem, you have to find
a way to do it that doesn't involve making radical changes to peoples' workflows.


## The Idea

You may have guessed where I'm going with this: containers.
Rather than thinking of a Linux system as one big system containing all
users, I want you to think of a Linux system as a hierarchy of containers.
In this setup, each container has exactly one user.
At the root of the hierarchy is the "root container". This container is
identical to Linux systems as you know them, except that the only user is root.
Then for each user, there is a container that is a child container
of the root container (I will refer to these as "user containers").
Each daemon has its own container as well.
Each user/daemon container may have their own children, and so on.

But there is a problem: how does a user make changes to the root container?
This is something that must be possible - kernel modules must be loaded,
the boot process must be tweaked, iptables must be configured, and so on.
The solution to this is to provide a controlled mechanism for "breaking out" of
the current user container and into its parent.

In order to implement this mechanism, you need two things: a daemon running
in the parent container, and a CLI tool that the user runs in the child container
when they want to get into the parent container.
The daemon is responsible for creating and managing all containers that are children
of the container in which it runs.
Upon creation of a child container, it inserts a unix domain socket into the child container's rootfs.
It then monitors the socket, implementing a protocol that allows the user to prove they have
permission to access the parent container.
If they are able to satisfy the conditions of the protocol, the user gets access to a
shell prompt that is tunnelled through the unix domain socket.


## How is this better?

Turning the OS into a hierarchy of containers is a powerful paradigm
and provides a number of benefits:

- For most tasks, the user isn't burdened by the need to call `sudo`.
They don't have to understand what privileges are appropriate for a given file,
or what `umask` is: if they can see the file, they can do whatever they want to it.

- There is a clear separation between working in the context of a regular user,
and working in the context of a superuser.

- There is no need to abuse the concept of users to isolate daemons -
they just go in their own container, and can access anything that appears in it.

- It is easy to start fresh - you ensure your home directory is backed up and blow away
the container. Then you can spin up a new container with the same home directory.

- There is no need for the user to have intimate knowledge of how the security
facilities of Linux come together to provide them with this system. They just think
something along the lines of, "A is in a separate container from B,
so they cannot mess with each other."

- Performance is not impacted because all containers are talking to the same kernel.

Most importantly, these benefits come while keeping a number of things the same.
For one, the filesystem layout is kept the same. Every container can adhere to the
FHS... or not, all at the user's discretion.
Also, packages are managed in the same way: nothing changes here other than
the removal of the need to put `sudo` before every invocation of the package manager.
The only real difference from traditional Linux distros is the way you escalate privileges:
`sudo` is replaced by the CLI tool that is used to get into the parent container.
Minimizing differences will maximize adoption.


## Components

### The Daemon

The main job of the daemon is to be a "Container Manager" as described on
[this link on freedesktop.org]. It manages all child containers of the container
in which it runs, and registers them with with `systemd-machined` so that we can
use the usual tools (`ps`, `systemctl`) to get info about child containers.
The daemon runs as a systemd service and maintains a unix domain socket
over which the CLI, as invoked in its container, can talk to it.
It also maintains and listens on a unix domain socket for each child container,
to facilitate users in that container escalating to the parent container as
described above.


### The CLI

The CLI is just an interface to the daemon. It allows the user to interact with
the daemon in order to:
- get a shell in the parent container
- get, create, list, update, and remove containers
- start, stop and restart containers
- get a shell inside a child container


### PAM Module

Deals with anything related to user/daemon containers and logins.
There is more work to be done here to figure out how this should work.


## Other Miscellanous Points

This is where I put my really undeveloped ideas. It is all subject to change!

### Login Process

This is a half-baked thinking-through of how the login process might work.
I just learned about PAM, so it's probably wrong, and definitely incomplete.

1. Keyboard input on TTY.
1. `getty` invokes a special version of `login`.
1. `login` calls PAM modules. One gets called that authenticates the user.
   Another gets called that performs the necessary steps to change the environment
   of the process so that it is inside the relevant user's user container.


### Future Directions/More Questions

- How does networking work?
- How does a user container work with a GUI?
  - Can each user container have its own GUI setup?
  - Should we make access to parent containers through CLI only?
- How *exactly* does the login process work?
  - Via TTY?
  - Via `sshd`?
  - With a GUI?
  - For future, unanticipated programs?
- How many of the ideas from [Fitting Everything Together] can we implement? Particularly:
  - immutability of /usr/
  - use of `systemd-homed`
- Is a user container persisted in its parent container as an entire rootfs, or just as
  that user's home directory?
- What parts of systemd need to be cut out? Which parts conflict with these ideas?
- What does the daemon use for a container runtime?

[this freedesktop.org page]: https://www.freedesktop.org/wiki/Software/systemd/ContainerInterface/
[Fitting Everything Together]: https://0pointer.net/blog/fitting-everything-together.html
[this link on freedesktop.org]: https://www.freedesktop.org/wiki/Software/systemd/writing-vm-managers/
