---
layout: post
title:  "Blogging with Jekyll & Docker"
---

I'v been moving as much software development into virtual machines and
docker. I moved some of my ruby libraries into docker. Eventually I
needed to move my blog into docker. All my blogs and other static
sites are written using [jekyll][]. Jekyll is great for generating
static sites. It has one serious downside: it's distributed as a ruby
gems. Rubygems (and any other packaging system for an interpreted
language) buts a burden on the consumer to maintain that stack on
their computer. These stacks (ruby in particular) can be quite
finicky. Luckily, we have docker! It's straight forward to build an
image that mimics the jekyll executable.

Once the image is ready, the local directory can be mounted as a
volume in th jekyll container. From there it's possible to use jekyll
commands normally. Once the file system is prepared, a container can
run `serve` command to preview it. Then combine that with exposing
some ports and you have a completely isolated (and repeatable)
blogging environment.

Start off by creating a simple `Dockerfile`.

    FROM ruby:2.1.3

    RUN gem install jekyll therubyracer --no-ri --no-rdoc

    VOLUME [ "/data" ]

    WORKDIR /data

    CMD [ "jekyll", "-v" ]

Heads up! The `therubyracer` is not an explicit dependency. Jekyll
lists coffeescript as a dependency. That library requires _a_
javascript runtime (yes), but the runtime depends on the host and
other factors which cannot be listed in the gemspec. So in short,
`therubyracer` must be installed to use jeklly in docker even if you
do not intended to write coffeescript.

This image could also be refined to use `ENTRYPOINT` but there were
some problems getting the `jekyll` command to behave correctly. A
wrapper script could be used (ala the official images) but its not
necessary for this exercise.

Next, use the image to create the site structure through `jekyll`.

    $ docker run -v $(pwd):/data jekyll jekyll new my-site

Heads up! This creates the `my-site` directory in `pwd`, simply move
the files to the correct place if you want a different structure.
Unfortunately `jekyll new` will not generate files if the directory is
not empty (assuming `pwd` is not empty).

Now you can write posts and generate the site using the same approach.

    $ docker run -v $(pwd):/data jekyll jekyll build

Voilla! Now `_site` contains a publishable website! Only one more thing
to do: setup the previewing container. I manage all persistent
containers with [fig][]. Create `fig.yml` to launch the jekyll image
with the `serve` command with `pwd` as a data volume.

    site:
      image: jekyll
      command: jekyll serve --port 9292 --force_polling
      volumes:
        - .:/data
      ports:
        - 9292:9292

It's not mandatory, but I prefer to use explicit ports (for commands
that accept it) so port mapping is obvious. Otherwise the reader must
know a command's default ports. `--force_polling` is required for live
updates. I could not get the native file system even things to work and
`--force_polling` worked well enough. Now hit port 9292 on your docker
host and your site is waiting for you.

Happy writing! This website itself is built using the same process.
You can view the [source][] on github.

[Jekyll]: http://jekyllrb.com
[Source]: https://github.com/dockerbuild-info/dockerbuild.info
