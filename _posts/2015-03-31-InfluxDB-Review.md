---
layout: post
title:  "InfluxDB Review"
date:   2015-03-31 17:30:00
categories: blog
---
[InfluxDB](http://influxdb.com/) is fairly new in the [Time Series Database (TSDB)](http://en.wikipedia.org/wiki/Time_series_database) solutions, and it's [open source](https://github.com/influxdb/influxdb). Like other TSDBs, InfluxDB is great for reading and writing timestamp-based data, so it may be suitable for real-time data like transaction per second when you are doing stress testing. I've been using InfluxDB for my current project. It's been functional most of the time, but it took me and several other engineers a lot of blood, tear, and sweat to really tame this beast. This blog will cover the good, the bad, and the ugly about InfluxDB.

### The Good

#### No external dependencies
Setting up an InfluxDB instance is super easy because there's no external dependencies. You simply just install the RPM package, modify the configuration file, and the BOOM your service is up!

#### Arbitrary data
Writing data is extremely simple. As opposed to traditional SQL tables, InfluxDB doesn't need a predefined columns. You can arbitrary add more columns by submitting data for that column, and historical entries would simply have NULLs in that newly added column. Its "write-what-you-want-to-read" philosophy is what makes it an excellent choice for rapid, iterative development.

#### Raft consensus
The InfluxDB uses the [Raft consensus algorithm](https://raftconsensus.github.io/) to support clustering. This is a great feature if high-availability is what you strive for.

#### UDP support for write
When writing high volume of data, UDP is definitely going to be out perform TCP. In the world of tens of thousand of WRITEs per second, dropping a few UDP message won't be a big deal.

### The Meh

#### Front end
It's great that InfluxDB provides an out-of-box front end for management. It's simply a front-end to their existing HTTP APIs, and supports several useful features such as querying data and dropping shards. However, being a quasi-front-end guy myself, I do not think their current front end is really at production level. The pages are client-side rendered, so refreshing the page will always bring you back to the landing page, and this always confuses the hell out of me. Moreover, there is no easy way to update a shard space, despite it is supported by their HTTP API.

#### Grafana support
Ironically, Grafana support was supposed to be in the Good section. Grafana is a great tool that allows a user to easily create a dashboard to view InfluxDB data. The problem is, Grafana is performing client-side polling if its real-time update is turned on. This can really hurt the server if multiple Grafana pages is opened at the same time, especially when its polling large amount worth of aggregated data over and over per second.

#### Documentation
To be fair, InfluxDB is only around for a short time, so it's probably understandable that they are short on documentation. However, the documentations can sometimes be ambiguous and confusing. There may be some important key points hidden within paragraphs of text, which I wish they have highlighted them to be more obvious. Their HTTP APIs aren't all well documented, if documented at all. Their page really feel like a on-going wiki page for an internal tool, which I'd like to see it evolve organically.

### The Ugly

#### Time boundaries
I really wish the InfluxDB's documentation warns their users in large, red font that queries that are missing a time boundary will result in a full table scan. Maybe that is a no-brainer for TSDB, but making non-bounded queries have cause us several performance issue when we were just tinkering on its management page. To combat this issue, I have developed a replacement front-end that would inject time boundary for all queries, but I really wish this is an out-of-box feature.

#### No way to kill slow query
As a database administrator, I should be able to force kill a slow query if I know it's taking too much resource. At the moment, if the Influx query you issued happens to be really, really slow and resource heavy, you are pretty much out of luck. You'd have to either wait indefinitely for it to finish (bad!), or restart the service (worse!)

### Final words
InfluxDB's "write-what-you-need-to-read" and "write-and-throw-away" philosophy is what makes it great for reporting large amount of real-time data. However, it is still fairly young in the tech stack, and requires its user to really understand what is happening behind the curtain to avoid inefficiency. Having gone through all the trouble to get it working, I'd still say it's a viable solution for real time read and write, but I would love to use other better alternatives, if any. Bottom-line, I see a lot of improvement opportunities for InfluxDB, and I believe it will shine in the coming years.
