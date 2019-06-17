# docker-elk-compose
Elastic ELK Stack - Elasticsearch, Logstash, Kibana in one package. To be used by docker-compose


Pre-requisites:

- Docker (EE/CE)
- Docker-Compose

By default, the services in the stack will expose the following ports:
* 5000: Logstash TCP input
* 9200: Elasticsearch HTTP
* 9300: Elasticsearch TCP transport
* 5601: Kibana Web Frontend

1. Clone the files using 'git clone https://github.com/MrSjodin/docker-elk-compose.git'
   (Will create subdirectory docker-elk-compose)

2. Edit (at least the following) config files with right preferences - remember to CHANGE THE PASSWORD!
   ./docker-elk-compose/elasticsearch/config/elasticsearch.yml
   ./docker-elk-compose/kibana/config/kibana.yml
   ./docker-elk-compose/logstash/config/logstash.yml

3. All done? Well, now bring up the containers using 'docker-compose up -d'


Further notes:

- You have to run `docker-compose build` first if you switch branch version or update a base image.
