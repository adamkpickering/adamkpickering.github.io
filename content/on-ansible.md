---
title: "On Ansible"
date: 2020-01-03T19:17:41-07:00
draft: true
---

*Ansible is a language that was originally designed to be declarative,
but due to real-world requirements and its explosion in popularity,
procedural elements have been forced into the language.
The result is a language that fulfills none of its original promises.*

setting the stage:

- Ansible release date
- LXC
- Docker
- AWS
- GCP
- Kubernetes
- when were cgroups and namespaces added to kernel
- what version of windows
- what version of the iphone was out
- Chef
- Puppet
- phoenix project

I can see the gears turning as the creators of Ansible dreamed it up.
At the time of its initial release in 2012, devops was barely a thing.
Linux containers were still fresh and new, with adoption limited
to those who could afford to take a risk. Docker was not yet released.
Administration of servers was still a largely manual job,
with tasks automated where it was feasible.
Automation was done with various scripting languages:
Perl, Python and especially Shell.
Naturally, systems needed care and feeding frequently.

- hard drives fill up
- network configuration changes
- equipment fails
- logs need rotating
- software needs updating
- certificates need updating
- applications need to be deployed/updated

As we can see, a computer system is a highly dynamic environment.
Things inevitably change, and we want to be able to enforce a certain state
at the touch of a button. That is, we want a *declarative* tool.
This is the mindset under which Ansible seems to have been created.
If you stick to this mindset, Ansible is pretty good.
If your automation consists simple cases such as:

1. making sure certain files are filled out and and in a certain place
2. making sure certain services are started
3. making sure specific applications are installed

then Ansible is great, and should be reliable. That is, with a couple caveats:

- individuals can still mess with things
- configuration drift
- usually a way around needing to use SSH TFTP
- declarative means monitoring
  - you could run playbooks periodically

**in this light YAML + Jinja2 is not unreasonable**

It wasn't perfect, but it was certainly an improvement over
doing things manually or writing a shell script. Or using Chef.
Or Puppet.

But the industry developed. Cloud platforms became a big deal.
All of a sudden, people wanted to provision servers on these platforms.
It seems like a simple task with Ansible. Just run a couple of tasks right?
But to truly fit with Ansible's model, this requires that we create
our virtual servers, and then (since there are pieces of information
about these servers that we cannot know in advance) write the information
AWS sends us back about the servers to an inventory.

In light of Ansible's traditionally static inventory, this became a problem.
**insert problems of dynamic inventory here**

**ways in which ansible still was not perfect**

- configuration drift
  - ultimately a declarative tool means monitoring

Another technological development that occurred after Ansible was released
was containers. While LXC had existed for a while (**how long?** **cgroups,
namespaces**), Docker had yet to be released (**when?**).
Yet with popularity, containers carved out a big chunk of the niche 
that Ansible had occupied.
Why would you use Ansible modules, which are essentially scripts
(with all of the problems of scripts), when you could create a container
that is literally a root filesystem, that is not influenced whatsoever
by the underlying system (yes there are a few caveats here)?
Not to mention the other benefits of containers,
why would I write an Ansible playbook when all I need to run my application
is `docker-compose up` **check this**?
The fact of the matter is that container-based tools are straight-up
**better** than a tool like Ansible at the declarative paradigm.

This situation developed with the relatively recent advent of Kubernetes.
Kubernetes introduces a whole new level of declarative configuration.
Now, we don't even need Ansible for its ability to scale - all of the
scaling business is taken care of.

So where does that leave Ansible? I argue it leaves it on the trash heap.
There are so many well-written tools that do an infinitely
better job of declarative configuration than Ansible, I don't know why
you would use it.

What about other cases?

- business IT - pushing out windows updates
- managing bare-metal servers -> use TFTP or something
- network automation

-----

In part I we examined how Ansible was designed to be a declarative
tool, and how better tools for the declarative paradigm have recently
emerged. In this part we look at how Ansible might be used to complement
these tools, as a glue language (same idea as shell).
We make the assertion that for this type of task,
Ansible would have better been designed as a Python library.
Its problems are ones that Python has already so elegantly solved.

## Looping

In any glue language, looping is an important feature.
We need the ability to perform the exact same procedure to
a list of similar things in sequence or parallel.
In Ansible this is done in the following way:

```
- debug:
    msg: "{{ looped_var }}"
  loop:
    - "hello"
    - "there"
    - "world"
```

Assuming you first learned loops using a traditional C-like language,
this may look a little strange. But once you understand it, it's pretty
clear right? The equivalent in Python would be:

```
vars = ["hello", "there", "world"]
for var in vars:
  print(var)
```

Now, what if we want to run multiple tasks on each iteration
of the loop? In Ansible:

```
- set_fact:
    hello_there_world:
      - "hello"
      - "there"
      - "world"

- debug:
    msg: "debug 1: {{ looped_var }}"
  loop: "{{ hello_there_world }}"

- debug:
    msg: "debug 2: {{ looped_var }}"
  loop: "{{ hello_there_world }}"
```

In Python:

```
vars = ["hello", "there", "world"]
for var in vars:
  print("debug 1: {}".format(var))
for var in vars:
  print("debug 2: {}".format(var))
```

We're starting to see that Python is both more readable and more concise.
Another example, in Ansible:

```
ansible
```

And in Python:

```
python
```

- if statements
  - if statements done in jinja2? If so, what is the relative value of
    learning Jinja2 versus learning Python if statements?
- code encapsulation (roles are not great)
- dependency management
  - local modules
  - unexpected dependencies of modules (credstash)
- error messages suck
- where YAML + Jinja2 was a boon for declarative,
  it sucks for procedural (readability)
- working with structured data
- lack of unit testing

example: credstash, pulling in a secret to use

"three or four layers of indirection deep makes it suck"

-----

Rather than basing it on an
existing programming language, or writing a new language,
the creator(s)? decided to cobble the language together from
two existing languages: YAML and Jinja2.
YAML is a language for structuring data.
Jinja2 is a language for filling templates in tandem with Python.

But hold on, before we go further, I'd like to do a thought experiment.
What were the creators of Ansible thinking when they made it?
It makes sense to me that they would have been thinking along the lines of,
"wouldn't it be nice if I had a declarative alternative to shell scripts,
that I could also use to manage my servers at scale?"
What an attractive idea.
We're all familiar with the pains and pitfalls of shell scripting.
We all want the ability to manage our applications at scale.
If you're making Ansible, and you think it's going to be completely
declarative, it makes sense to base it on YAML because the
declarative model means everything is fixed, right?
Oh, and we can include a templating language because we need
some way to work with variables.

The problem is that the domain which Ansible occupies requires more
than the declarative paradigm can provide.
Procedures need to be repeated. Data needs to be worked with:
read from and written to local and remote stores, filtered, generated,
parsed. None of these things fit into the declarative paradigm -
in other words, we need a procedural language. Or maybe a language
that combines the procedural and declarative paradigms, but
considering that procedural is the more general paradigm,
it has to start from something procedural.

My point is that the most fundamental design decisions in Ansible
reflect the desire for it to be a declarative language.
But in practice it needs procedural capabilities.

