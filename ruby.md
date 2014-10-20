---
layout: page
title: Ruby Images
permalink: /ruby/
---

[onbuild]: https://docs.docker.com/reference/builder/#onbuild
[image]: https://registry.hub.docker.com/_/ruby/
[vendor everything]: http://ryan.mcgeary.org/2011/02/09/vendor-everything-still-applies/

**Recommended image:** [The Official Docker Image][image].

The official image is as good as its going to get. It's not small by
any means (~800 Mb) but it will cover most project's use cases. It's
based on debian (and the official buildpack base image) so most common
development packages are available. The image comes in two flavors:
normal and [onbuild][]. The onbuild image will automatically run
`bundle install` and add code to the image. The normal image is better
suited for people who want complete control.

## Onbuild vs Normal

I use the normal image because along with `bundle package` because
[vendor everything][] still applies. I prefer this for a few reasons.
First, I prefer to remove as many moving parts as possible from the
build & release chain. I also prefer consistency and resilency. Use
the `ruby` images to generate a `Gemfile.lock` and `vendor/cache` and
commit them to source control. Now the dependencies are completely
frozen. The next step is to run `bundle install` with `--local`. This
ensures the rubygems is not contacted as part of the install process.
You're left with is more consistent & faster builds. The tradeoff is
commiting dependencies to source control. There have been plenty of
discussions around this topic.

Here's an example `Dockerfile`

    FROM ruby:2.1.3

    RUN mkdir -p /usr/src/app
    WORKDIR /usr/src/app

    ADD Gemfile /usr/src/app/
    ADD Gemfile.lock /usr/src/app/
    ADD vendor/ /usr/src/app/vendor
    RUN bundle install --system --local

    ADD . /usr/src/app

Now generate the `Gemfile.lock` and `vendor/cache`:

    $ docker run --rm -it $(pwd):/data ruby bundle package --gemfile /data/Gemfile
