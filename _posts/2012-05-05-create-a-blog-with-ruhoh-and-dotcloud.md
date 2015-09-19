---
layout: post
title: "Create a blog with ruhoh and dotCloud"
excerpt: "Use ruhoh to create a website and host it through dotCloud"
tags: [ruhoh, dotCloud, Bitbucket]
date: "2012-05-05"
---

While setting up this site I took some notes on installing [ruhoh](http://ruhoh.com "ruhoh.com") and hosting it through [dotCloud](https://www.dotcloud.com "dotcloud.com").
The installation commands are specific to Ubuntu (12.04) but should be easy enough to adapt to your preferred distribution.

### Install ruhoh
In order to install ruhoh we need to update Ruby from Ubuntu's default `v1.8` to `v1.9`:

	$ sudo apt-get install ruby1.9-full
	$ sudo update-alternatives --config ruby
	$ sudo update-alternatives --config gem

and select the 1.9 versions. Note that `/usr/bin/gem1.9.1 --version` returns 1.8.11 and not 1.9.1.
Optionally remove any ruby1.8 packages:

	$ sudo apt-get remove ruby1.8

Now either install ruhoh 0.1.3

	$ sudo gem install ruhoh

or, for the very [latest](http://ruhoh.com/blog/releases/ruhoh-version-020/ "ruhoh v0.2.0"), install `libyaml-dev` and specify the version number:

	$ sudo apt-get install libyaml-dev
	$ sudo gem install ruhoh --version 0.2.0

Ruhoh creates the scaffolding for our new blog `sirrah` with a single command and [rackup](https://github.com/rack/rack/wiki "rackup wiki") can immediately show us a preview:

	$ ruhoh new sirrah
	$ cd sirrah
	$ rackup -p9292

Point your browser to `localhost:9292` and have a look at the default [Twitter Bootstrap](http://twitter.github.com/bootstrap/ "twitter.github.com/bootstrap") template.
If you make any changes to the template or any of the pages the preview through rackup is automatically updated.
The ruhoh site has some more [details](http://ruhoh.com/usage/templating/ "ruhoh templating") on configuring the contents of your templates and [Bootstrap](http://twitter.github.com/bootstrap/examples.html "Bootstrap examples") will get you started on tweaking the appearance.
Selecting nice [fonts](http://theleagueofmoveabletype.com/manifesto "Movable type manifesto") and colors are where I usually get stuck. So lets skip that for now and continue with the default theme.

Before you can put your site online you need to compile your pages and posts.
This creates the `_compiled` directory containing the final static html files which you can preview with any http server:

	$ ruhoh compile
	$ cd _compiled
	$ python -m SimpleHTTPServer 8181

You can now let ruhoh [publish](http://ruhoh.com/usage/publish/ "Publish to ruhoh.com") your site using GitHub.
However, since the site is just a collection of static html pages it is quite simple to host anywhere you want.

### Hosting with dotCloud
DotCloud offers a PaaS but is much more flexible than most providers.
One advantage of running a static site like with ruhoh is that the [free tier](https://www.dotcloud.com/pricing/ "dotCloud pricing") is probably sufficient.
Otherwise, I've been lucky enough to partake in the beta program which offers the professional features for free.


Sign up with [dotCloud](https://www.dotcloud.com/ "dotCloud") and install as per the [instructions](https://docs.dotcloud.com/firststeps/install/ "First steps"):

	$ sudo apt-get install python-setuptools
	$ sudo easy_install pip && sudo pip install dotcloud
	$ dotcloud

Enter your api key when asked.
Now create a new site with:

	$ dotcloud create sirrah

and create a new `dotcloud.yml` file with the contents:

	www:
		type: static

This tells dotCloud your site contains only static html files.
Compile the site with ruhoh and push to dotCloud:

	$ ruhoh compile
	$ cp dotcloud.yml _compiled/
	$ cd _compiled
	$ dotcloud push sirrah

The http address of the site is in the output.
Congratulations, your site is now hosted in the cloud.

### Version control with Bitbucket

Your new ruhoh site comes with a git repository which you might prefer to host at something other than GitHub.
To put it on Bitbucket create a new repository via their [site](https://bitbucket.org/repo/create "Create new repository").
Rename the current `origin` and point your repo to the new bitbucket address:

	$ git remote rename origin ruhoh
	$ git remote add origin ssh://git@bitbucket.org/Sera/sirrah.git
	$ git config branch.master.remote origin
