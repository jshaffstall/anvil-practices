# Anvil Best Practices

This document aims to gather my current best-practices using [Anvil](https://anvil.works/), a tool for writing web apps in nothing but Python.  As such it will evolve over time as my experience with Anvil evolves.

Since a lot of this document will talk about limitations of Anvil, I want to state up front that Anvil is an amazing tool for building web apps in nothing but Python.  They've made a lot of absolutely correct design decisions, and the limitations mentioned in this document are just a matter of Anvil still being a work in progress.  

# How Many Apps?

Anvil makes it ridiculously easy to partition your large web app into a series of smaller, more focused apps that share the same set of data tables.  Using Anvil for hobby projects, this makes a lot of sense.  Trying to use Anvil for more production level work, though, this runs into issues.

In a production environment you want to have a development version of the code, a test version of the code, and a production version of the code.  Right now, Anvil supports only a very limited separation of those environments, with some major drawbacks.  

## Limitations of What Anvil Supports

You can have a development version and a published version of an app.  This setup has the following drawbacks:

1. The development version and the published version share the same data table schema.  This makes it difficult to make radical changes in development without breaking the published version.  

2. The development version and the published version share the same data table contents.  While testing you'll see live data, and vice versa.

3. The only way to access the development version of the app is to run it in the Anvil editor.  There's no way to generate a URL that external users can use to test your changes.

## How Do People Normally Work Around Those Limitations

People normally make a copy of each app.  One of those copies is the development/test version, and the other is the production version.  In the development/test version, the published version is what external testers would see, while the unpublished version is what the developer sees.  

When you are ready to push tested changes to production, you use some Git magic to pull the test version to your local machine, and push it back to the production app.  

This process increases in complexity exponentially when you have a lot of Anvil apps that combined form a single web app, to the point where even with just a few smaller apps combined it's unworkable (for me, at least).

## So How Many Apps?

I'm currently using one Anvil app per project.  This simplifies the dev/test/prod split.

# How Can One Anvil App Represent Multiple Smaller Apps?

In my current project, I have four smaller apps embedded in the one larger one.  One is the admin app, another is a content creator's app, another is a content consumer's app, and another is for website support (more on that later).

The [Hash Routing](https://github.com/s-cork/HashRouting) dependency for Anvil apps is key to maintaining the separation between the smaller apps.  Using routing each smaller app can have its own section of the main app.  For example, http://example.com/#admin, http://example.com/#create, etc.  Each of those subsections can have its own look and feel and navigation within itself.

## Hash Routing?  What About Search Engines?

You don't want to use hash routing for apps that need to be indexed by search engines.  Those apps are private apps that are intended to be used by authorized people.

# So What About Search Engines?

For search engines you need a publicly accessible site that provides non-hash URLs, ideally in a form that will never change and includes as few query parameters as possible.  For my project, I'm using a Python static site generator named [Urubu](https://urubu.jandecaluwe.com/start.html) to generate the public facing site.  This is the site where people will come to see what products are available and, hopefully, to purchase them.  

## How Do You Include Dynamic Content In A Static Site?

The static site does need some dynamic content.  For my use case it doesn't need to pull products from the database; I'm fine building the product list in a series of configuration files for Urubu to read, and I've written some custom Python code that will cause Urubu to generate the various pages needed for each product on the site.  All of that will be static.

The dynamic portions include:

1) A shopping cart
2) The checkout process
3) Possibly a My Account section (although that might end up being a sub app linked to from the static site)

My current approach is to embed Anvil forms into the static site using iframes.  All the iframes share the same session, so the login from one will be reflected in the others.  This seems to work reasonably well.

# What About Production?

I'm not at the point where I need a production version yet, but here are the two approaches I plan to investigate.

## Prodution Anvil App

This is what as described above, where you have an Anvil App that is the production version, and you push changes to it via Git and a local repository.  This has some gotchas I'll need to investigate.

The advantages of this approach are:

1. Since it's an Anvil hosted app, you retain the data tables inspector to be able to investigate and potentiall fix issues easily

The disadvantages of this approach are:

1. Since it's an Anvil hosted app, you have the Anvil restrictions based on your current billing plan.  For example, on the amount of storage space you have for data tables.  An app that provides a sizeable media library, for example, could quickly run into issues, especially when you have a separate copy that is the development verson of the app.

2. Upgrading those limits requires spending more money on Anvil.  If your app is such that you don't hit those limits until you're making money, then you're fine.  If your app is such that you hit those limits early (a storage intensive app, for example), then you may need to upgrade before your revenue supports it.

3. The process to update the production app involves pulling the development version to your local machine and then pushing it to the production app.  

## Open Source Anvil App Engine

Anvil has released their engine to run Anvil apps as open source.  This means you can run an Anvil app on any Linux server.  You could run it from your home machine if you wanted, although that would be an odd choice for a production app.  More typically you could use a cloud based Linux server, such as from DigitalOcean.  

The advantages of this approach:

1. Presumably you're no longer bound by your Anvil billing plan's limits, since you're not running on Anvil infrastructure now.  This should tie you to the cost of the Linux server, which typically scales better than Anvil hosted pricing.  This gives you time to build revenue from storage heavy apps before you would then upgrade your Anvil billing (for higher priority support requests).

2. The process to update the production app is to do a git pull in the app directory on the Linux server.  It'll pull the latest version of the development app down and start using it.  You might need to stop the app to allow the data tables to auto migrate on restart if you made data table changes.

The disantantages of this approach:

1.  You're out of the Anvil hosted toolset now, so getting access to what's happening in your data tables requires poking around in the somewhat abstract Anvil Postgres database.  It's possible, but not convenient or intuitive.  So you'd want to build your diagnostic tools into the app itself, so you can it all through the web interface.


