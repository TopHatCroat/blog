+++
title = "Setup Elasticsearch on CircleCI"
description = "How to setup an ES cluster on your Circle CI pipeline to do integration testing"
tags = [
    "development",
	"ci",
	"devops"
]
date = "2021-05-03"
+++

### This is going to be a short one

I'm only writing this one because I could not Google exactly what I needed as easily as I expected, so hopefully it might help someone.
Setting up an Elasticsearch instance to test against on CircleCI is actually quite straightforward.


Your `.circleci/config.yml` should look something like this:

```yaml
jobs:
  build:
    docker:
    - image: circleci/<language>:<version TAG>
    
	- image: docker.elastic.co/elasticsearch/elasticsearch:7.12.0 #1
      environment:
      - transport.host: localhost #2
      - network.host: 127.0.0.1 #3
      - http.port: 9200 #4
      - cluster.name: es-cluster #5
      - discovery.type: single-node #6
      - xpack.security.enabled: false #7
      - ES_JAVA_OPTS: "-Xms256m -Xmx256m" #8
   
   steps:
      - checkout
	  - run: #9
        name: Wait for ES startup
        command: dockerize -wait tcp://localhost:9200
      - run: echo "this is the build job"
```

First thing to do is to add the Elasticsearch image (`#1`)to your test image on Circle CI. Circle CI will use the first `- image` in `docker` YAML array as a starter image, and every following `- image` item will be "joined" into the first image with their ports exposed on localhost.
So in our case, as stated in `#2-4`, Elasticsearch will start at `localhost:9200` and that's where it will be available within your tests running on Circle CI.


Next up is setting the name of the cluster (`#5`), any name will do, use something that is relevant to you.
Setting discovery type to _singe-node_ in `#6` is required to make Elasticsearch work when only one instance is running, which is true in our case as we only want a bare bones Elasticsearch cluster to test on.

`#7` is up to you, it disables security features which you probably don't need on your testing environment, so this just makes things easy, but feel free to enable them if you want to make sure your code runs only on properly secured ES.


Circle CI has limits on how much memory your images can use, with _medium_ resource class, which is the default, this is 4 GB, so you want to be pretty frugal with memory here.
But you might already be aware of this if you ever saw the dreaded `error 137`.
I've found that 256 MB is enough for Elasticsearch to do all I need it to do during testing, much less than that and you start seeing errors during ES start up, you might need more if you are testing some serious workflows with ES, so adjust accordingly.


Lastly, ES takes some time to start up, so in `#9` we use _dockerize_ to await the start up of it, because it would be possible for your tests to start befor ES is ready.
_dockerize_ comes preinstalled in standard CircleCI base images.


And that's it, happy testing.

