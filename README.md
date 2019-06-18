# docker-elk-compose
Elastic ELK Stack - Elasticsearch, Logstash, Kibana in one package. To be used by docker-compose. 

Based on the official Docker images from Elastic:

* [Elasticsearch](https://github.com/elastic/elasticsearch-docker)
* [Logstash](https://github.com/elastic/logstash-docker)
* [Kibana](https://github.com/elastic/kibana-docker)

## Pre-requisites

- Docker (EE/CE)
- Docker-Compose

## Quick-Start instrux

By default, the services in the stack will expose the following ports:
* 5000: Logstash TCP input
* 9200: Elasticsearch HTTP
* 9300: Elasticsearch TCP transport
* 5601: Kibana Web Frontend

The configuration is stored in:
* Elasticsearch configuration: [`elasticsearch/config/elasticsearch.yml`][config-es]
* Kibana default configuration: [`kibana/config/kibana.yml`][config-kbn]
* Logstash configuration: [`logstash/config/logstash.yml`][config-ls]

### Getting started

1. Clone the files using `git clone https://github.com/MrSjodin/docker-elk-compose.git` (This will create subdirectory `docker-elk-compose`.)

2. Edit (at least the following) config files with right preferences - remember to CHANGE THE PASSWORD!
  * `./docker-elk-compose/elasticsearch/config/elasticsearch.yml`
  * `./docker-elk-compose/kibana/config/kibana.yml`
  * `./docker-elk-compose/logstash/config/logstash.yml`

3. All done? Well, now bring up the containers using `docker-compose up -d`

## Further notes

> :information_source: Configuration isn't dynamically reloaded, you will have to restart individual components after you've made any configuration change.

> :information_source: Basic licensing is enabled by default. To change to full (trial), modify ./docker-elk-compose/elasticsearch/config/elasticsearch.yml and replace  `basic` with `trial`.

> :heavy_exclamation_mark: You have to run `docker-compose build` first if you switch branch version or update a base image.

## Persistent storage of indexed data

The data stored in Elasticsearch will be persistent after container reboot but not after container removal. For production, it's **heavily recommended** to make storage  persistent. 

In order to persist Elasticsearch data even after removing the Elasticsearch container, you'll have to mount a volume on your Docker host. Update the `elasticsearch` service declaration to:

```yml
elasticsearch:

  volumes:
    - /path/to/storage:/usr/share/elasticsearch/data
```

This will store the Elasticsearch indexed data in `/path/to/storage`.