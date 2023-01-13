# Elastic stack (ELK) on Docker using Docker-Compose

![Elastic Stack version 8.5.3](https://img.shields.io/badge/ELK-8.5.3-blue.svg?style=flat)

[Elastic ELK Stack][elk-stack] - [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash), and [Kibana](https://www.elastic.co/products/kibana) in one package. To be used by [Docker Compose](https://docs.docker.com/compose/). 

Based on the official Docker images from [Elastic][elk-stack]:

* [Elasticsearch](https://github.com/elastic/elasticsearch-docker)
* [Logstash](https://github.com/elastic/logstash-docker)
* [Kibana](https://github.com/elastic/kibana-docker)

## Pre-requisites

- Docker (EE/CE)
- Docker-Compose

## Quick-Start instrux

By default, the services in the stack will expose the following TCP ports:
* 514:  Logstash Syslog input
* 5000: Logstash TCP generic input
* 9200: Elasticsearch HTTP
* 9300: Elasticsearch TCP transport
* 5601: Kibana Web Frontend

The configuration is stored in:
* Elasticsearch configuration: [`elasticsearch/config/elasticsearch.yml`][config-elasticsearch]
* Kibana default configuration: [`kibana/config/kibana.yml`][config-kibana]
* Logstash configuration: [`logstash/config/logstash.yml`][config-logstash]

### Getting started

1. Clone the files using `git clone https://github.com/MrSjodin/docker-elk-compose.git` (This will create subdirectory `docker-elk-compose`.)

2. Edit (at least the following) config files with right preferences - remember to CHANGE THE PASSWORD!
  * `./docker-elk-compose/elasticsearch/config/elasticsearch.yml`
  * `./docker-elk-compose/kibana/config/kibana.yml` 
  * `./docker-elk-compose/logstash/config/logstash.yml` 

3. Bring up the containers using `docker-compose up -d` standing in `./docker-elk-compose/`

4. The password for `kibana` user as well as enrollment token for enrolling Kibana is automatically generated. To fetch these, you need to open the logs using `docker logs elasticsearch`.

> :bulb: You might need to scroll up a bit in the logs to find the password and enrollment token

> :heavy_exclamation_mark: The generated password and enrollment token will only be logged once at the first startup

## Verify installation

In order to verify, you can take the following steps:

* `docker ps` should list the three containers; elk-kibana, elk-logstash and elk-elasticsearch
* `telnet localhost 514`, `telnet localhost 5000`, `telnet localhost 5601`, `telnet localhost 9200`, `telnet localhost 9300` should all result in successful connections
* Browsing to `http://localhost:5601/` should present you the Kibana frontend after a few minutes of initialization (using password and token fetched in step 4 above) - [How to connect to Kibana][connect-kibana]

## Getting data into Elasticsearch index

Before you can build up any index, Elasticsearch needs to be fed with data - preferably live data of any kind. You can go to the Kibana front-end in order to find data sources to add - Logging of Metrics for example.

Then, you can create index from the Kibana front-end (by hitting the *Create* button), or via the Kibana API:

```console
$ curl -XPOST -D- 'http://localhost:5601/api/saved_objects/index-pattern' \
    -H 'Content-Type: application/json' \
    -H 'kbn-version: 8.5.3' \
    -u elastic:<your elastic password> \
    -d '{"attributes":{"title":"logstash-*","timeFieldName":"@timestamp"}}'
```

## Further notes

> :information_source: Configuration isn't dynamically reloaded, you will have to restart individual components after you've made any configuration change.

> :information_source: There's a default syslog listener enabled on TCP/UDP 514 (on host, 5514 in the container) - please note that you might need to reconfigure grok patterns, date/time formats and so on. Such edits are to be made in `./docker-elk-compose/logstash/pipeline/logstash.conf`. 

> :information_source: Basic licensing is enabled by default. To change to full (trial), modify `./docker-elk-compose/elasticsearch/config/elasticsearch.yml` and replace  `basic` with `trial`.

> :heavy_exclamation_mark: You have to run `docker-compose build` first if you switch branch version or update a base image.

## Persistent storage of indexed data

The data stored in Elasticsearch will be persistent after container reboot but not after container removal. For production, it's **heavily recommended** to make storage  persistent. 

In order to persist Elasticsearch data even after removing the Elasticsearch container, you'll have to mount a volume on your Docker host. Update the `elasticsearch` service declaration to:

```yml
elasticsearch:

  volumes:
    - /path/to/storage:/usr/share/elasticsearch/data
```
This will store the Elasticsearch indexed data in `/path/to/storage` locally on the host.

You might prefer to store the data persistently in a Docker volume:

```yml
elasticsearch:

  volumes:
    - elasticsearch_data:/usr/share/elasticsearch/data
```

Note that you in this case will need the following at the bottom of the docker-compose file as well:

```yml
volumes:
  elasticsearch_data:
```
For further storage options, please see [the compose file volumes reference][docker-compose-doc-volumes]


[elk-stack]: https://www.elastic.co/elk-stack
[connect-kibana]: https://www.elastic.co/guide/en/kibana/current/connect-to-elasticsearch.html
[config-elasticsearch]: ./elasticsearch/config/elasticsearch.yml
[config-kibana]: ./kibana/config/kibana.yml
[config-logstash]: ./logstash/config/logstash.yml
[docker-compose-doc-volumes]: https://docs.docker.com/compose/compose-file/#volume-configuration-reference