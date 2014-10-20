# Docker & Vagrant Based Project Template

[Docker]: http://docker.io
[Fig]: http://fig.sh
[Vagrant]: http://vagrantup.com
[Make]: http://www.gnu.org/software/make
[Makefile]: makefile
[Jekyll]: http://jekyllrb.com

This project used [Docker][], [Vagrant][], [Fig][], and [Make][]
for building, testing, and publishing a [Jekyll][] based website.

The structure favors easily creating images(s) and standardizing on
ways to do common development tasks. [Fig][]
orchestrates the development environment when multiple containers are
required. [Vagrant][] encapsulates the entire environment
for repeatable development environments across computers & machines.

## New to the Project?

Here's how to get started:

```
$ vagrant up --provision
$ vagrant ssh
$ make environment # open up port 4000 on your host system
```

This will spin up the virtual machine, ssh inside it, build and start
start a preview server. That should be enough to get
going. Continue reading to learn more things.

## Files & Directories

* `/doc` - Accompany code documentation
* `/dockerfiles` - Dockerfiles to build all the project's images
* `/vagrant` - Scripts used to provision the environment
* `/tmp` - temporary artifcats and other things.
* `Makefile` - defines the minimum tasks required to build the project
* `Vagrantfile` - Vagrant configuration
* `fig.yml` - defines all the docker containers in the environment
  Circle CI.
* `.dockerignore` - Things not to send to docker when building.

Add more directories at the root level as you see fit.

## Vagrant

**All work is intended to be done in the virtual machine!** If things
happen to work on the host system that is by accident rather than by
design! You've been warned!

[Vagrant][] manages the development environment. The
virtual machine encapsulates everything needed to build and run docker
containers. `docker` and `fig` are installed with the shell
provisioner. If the workflow defined in the `Makefile` requires more
CLI utils install them in the virtual machine. The
[VagrantFile](Vagrantfile) should be used to expose ports to the host
system. See the section on [coordinating development
environments](#development-environments) for more information.

## Make

`make` coordinates most day to day activities. The common targets:

* `make environment` - Build & start the preview server
* `make` - generate the site
* `make clean` - kill all things docker

### Building Single & Multiple Docker Images

Docker makes projects with multiple images a real pain. The `docker
build` command assumes there is only one `Dockerfile`. It can read
content from stdin, but then you cannot add/copy filers and other
things. The `makefile` uses a replacement workaround to solve this
problem. There is no `Dockerfile` at the root. Instead each file
inside `dockerfiles/` is linked to `/Dockerfile` then `docker build`
is run. This solution allows N images to be built with or without
order dependence. It also decomposes acceptably when the project only
requires one image. See the [makefile][] for more information.

## Development Environments

[fig][] and [vagrant][] manage the complete
development environment. Fig manages which containers should be
running, links, and how to expose ports. Example, fig will start
things like mongo, elasticsearch, redis etc. Use these containers
with docker links to provide services to the projects containers.
Project specific containers may be configured as well. See the [fig
configuration reference](http://www.fig.sh/yml.html) for more
information. Once the containers are running, the `Vagrant` file can
be used export guest ports to the host system. See the vagrant docs on
[port
forwarding](https://docs.vagrantup.com/v2/networking/forwarded_ports.html)
for more information.

## Workflow

1. Define dependent images in `fig.yml` and the `Makefile`.
2. Run `make environment`
3. Create/edit source files
4. Look at preview server until correct
5. Run `make`
6. Commit
7. Push
