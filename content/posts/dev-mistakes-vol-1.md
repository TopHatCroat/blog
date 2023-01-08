+++
title = "Mistakes of modern development, vol 1"
description = "One of the mistakes is changing stack for not good enough reasons"
tags = [
    "development",
	"opinion"
]
date = "2021-05-04"
[_build]
list = false
render = false
+++

### You don't need a new stack (probably)

Let's imagine you are a lead dev in a small company building an application that is used by a few hundred customers.
The application stack is just a server running your code and a database.
Simple, but you can actually do quite a bit with just these two.

Along with the application, let's also imagine a few simple scenarios and how to tackle them.

#### Scenario 1: We need "big data"

One of the features ouf our application is that it needs to log every action a user does in it somewhere for some sort of audit reason or whatever.
You might think: "Ahh, this is perfect for something like Elasticsearch or Solr, let's add it in to the project".
And you are right, Elasticsearch or Solr would be perfect for such a thing, but just using the database still is almost certainly going to be good enough for this small app for now.
You can implement it much easier, because you don't need to read any significant amount of new documentation, and with less overhead because all that stuff is already there setup and working, so no new bugs to google franticly with deadlines looming.

And anyway, a couple of dozen GBs is not big, thats child's play for modern databases if you use them properly, which means setting up indexes correctly, in a way that they are actually used by your queries and not wasting space by over-indexing. 

#### Scenario 2: We need event processing

Quick pull an image of RabbitMQ or Kafka.

Digital Ocean used a single MySQL table for all their scheduling purposes up to 2017, you can read more about it [on their blog](https://www.digitalocean.com/blog/from-15-000-database-connections-to-under-100-digitaloceans-tale-of-tech-debt/)


