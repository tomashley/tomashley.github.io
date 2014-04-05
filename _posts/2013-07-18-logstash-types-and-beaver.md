---
layout: post
title: "Logstash @types and Beaver"
description: "Logstash discoveries"
category: articles
tags: [beaver, logstash]
comments: true
share: true
---

Something I came across last night has tripped me up a few times and I hadn't really noticed because I'd never really dived into it. 

### Brief overview

My setup at the moment consists of two [Logstash](logstash.net) servers. One receives logs from my production nodes and one from my development environments. Logs are shipped via [Beaver](https://github.com/josegonzalez/beaver) to a Redis queue. They do share the same Elasticsearch cluster, but I separate them here by putting them in different indexes.

Initially, when I started out using Kibana (v2 on Ruby) I had single node all encompassing redis/logstash/elasticsearch. So when I grew up and got an ES cluster, I pushed the logs to separate indexes and it stuck. 

Development is very transient and nodes and environment names change rapidly during development and QA cycles, so splitting the environments makes sense splitting out prod to a separate Logstash process.  

I am also able to test new groks, filters and mutations without affecting production collection.

### So to @types

We are developing some new capabilities in my workplace and that means a new environment, with a new software stack.

I want to split the logs from this new stack into their own Elasticsearch Index, but I don't much want to run up another Logstash server, so I'm reusing my development instance.

Trying to be clever, and knowing the Redis input takes a 'type' field, I've decided to ship logs to a different list name on Redis and placing these logs under a different @type. 

Great.

Or not.

The [Logstash docs for Redis](http://logstash.net/docs/1.1.13/inputs/redis) state:

> If you try to set a type on an event that already has one (for example when you send an event from a shipper to an indexer) then a new input will not override the existing type. A type set at the shipper stays with that event for its life even when sent to another LogStash server.

Well at the Beaver end I'm not actually setting a type before I ship.


### The big reveal

Some well placed tcpdumping to watch what was leaving the node reveals that Beaver was setting a tag - 'file'.

So now, so long as I ensure I set the @type in my Beaver config file, the type will propogate.
