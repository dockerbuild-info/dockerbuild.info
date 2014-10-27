---
layout: post
title:  "Development Environments with Docker & Vagrant"
---

I've been developing software for a while mostly all web related
things (JavaScript, Ruby etc). My development process has been install
homebrew, install needed libraries & services, then install rbenv.
Next install rubies and various other things. Next clone repos,
install gems, and get to work. Eventually this started to breakdown in
many annoying ways. Certain libraries in some projects could not be
updated because of C dependencies. Requiring newer versions of
Elasticsearch which in turn requires Java 7, but not wanting to
upgrade for various other things. Then I started working in a
development team and those problems multiplied by the number of people
in the team. I think everyone gets bit by this problem eventually.
Your development environment becomes more complicated and there hidden
dependencies, the process is not documented, and things slowly erode
over time. It was easy for me to realize I'd been doing it wrong for
_years_.

Then Docker came onto the scene. It wasn't a revolutionary idea but it
is a developer friendly implementation and thusly its caught on like
wildfire. Docker has a tremendous upside: standardized deployment
interface and parity between development and production. Starting a
few months ago I decided (professionally and personally) and I be
docker native from that point forward. That means doing all
development tasks with docker images and making docker images project
deliverables. That was only one step of the process.

The problem with manually created environments is that they are not
portable and will erode over time. They're also not portable to do
different machines (people run different operating systems you know).
Essentially, they are dependent on the host system. This is also not
acceptable. In addition to going docker native, I wanted something
that was also automated and repeatable from time zero. Time to move
all work into a virtual machine. Vagrant works perfectly for this. The
virtual machine (with automated provisioning through simple sell
scripts) is perfectly encapsulates responsibility of providing all
tools required to build, run, & test docker images.

Docker & Vagrant work very well together. There's more to the puzzle
though. Environments may require multiple dependent containers (mongo,
redis anyone?) and other roles. Projects may need to build multiple
images as part of their deliverables. You also need to consider how to
orchestrate the process on CI. This is the over all goal: provide
complete parity between environments across all parts of the software
development process.

I wrote this post to explain some of these issues but also as a
statement that I care deeply about the software development process and
how it can be improved. I think there are many people and teams facing
the same problems. Here are my requirements:

* Automated environment setup and teardown across team members and
  machines
* Completely decoupled development from the host operating system
* Used docker to build all project deliverables
* User docker to orchestrate all dependent services
* Enforced the same workflow for development & continuous integration
* Automated process of pushing test images to an upstream registry

This background information is the context for my
work on the template I use for all projects (professional & personal).

My [project template][template] is a solution for those requirements.
It takes care of all the boiler plate code needing to create an
automated environment to build, test, development docker based
applications. It uses make, fig, and vagrant to make it all happen.
This structure has been working well for me. I hope it works out for
you too. It's been extract from personal and professional work so it
should support simple and complex project. I'd love feed back on it so
it can be improved. Check out the [rationale][] for more information
behind it.

[template]: https://github.com/ahawkins/docker-project-template
[Rationale]: https://github.com/ahawkins/docker-project-template/blob/master/doc/RATIONALE.md
