---
layout: post
title: "DropWizard and Graphite"
description: "Collecting java metrics and pushing them into graphite"
category: articles
tags: [graphite, metrics]
logstash: true
share: true
---

I'm working on a new project at work, and we seem to have fallen on [DropWizard](http://dropwizard.codahale.com/) as the back-end of choice for building our service.

This is very nice, because it affords a metrics service as a viewport into the embedded Jetty engine and with various JVM metrics and the ability to time and count individual Java methods.

I have written a metrics scraper that pulls the DropWizard JSON metrics, flattens them and pushes them up into Graphite.

There are many [Graphite](http://graphite.wikidot.com/) front-ends out there, and I have tried a few. None quite fit my bill, and almost all need an amount of manual customisation I'm uncomfortable with. I'd like more automation.  
I know this is quite hard as everyone has different metrics and different paths under which they are stored.

My other bug-bear is that most are written in Ruby. I do like ruby for certain tasks, but I am not a fan of Gems and the version mismatches I always stumble on when I write on a laptop with Ruby 2+ and have to back-port for a CentOS server on 1.8.7!

[Opscode](http://www.opscode.com/) have done brilliant things with Chef Omnibus to overcome such annoyances.

### Python

So my plan is to try and knock up something a little more custom for myself using Flask as a caching server to Graphite's list of JSON metrics, and attempt a cubism.js style graphing front-end.

Let's see how I get on!