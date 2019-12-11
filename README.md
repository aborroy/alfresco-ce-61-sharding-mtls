# Alfresco CE 6.1 with Search Services including Sharding and MTLS

Deployment template based in official [Docker Composition](https://github.com/Alfresco/acs-community-deployment/tree/master/docker-compose) using SOLR Sharding with two nodes routing the content by DB_ID.

>> DBID (DB_ID): This method is available in Alfresco Search Services 1.0 and later versions and is the default sharding option in Solr 6. Nodes are evenly distributed over the shards at random based on the murmur hash of the DBID. The access control information is duplicated in each shard. The distribution of nodes over each shard is very even and shards grow at the same rate.

This configuration provides two SOLR Shards, identified as "0" and "1".

## Source Images

* [alfresco/alfresco-content-repository-community:6.1.2-ga](https://hub.docker.com/r/alfresco/alfresco-content-repository-community)
* [alfresco-share:6.1.0](https://hub.docker.com/r/alfresco/alfresco-share)
* [alfresco-search-services:1.4.0](https://hub.docker.com/r/alfresco/alfresco-search-services)
* [postgres:10.1](https://hub.docker.com/_/postgres)
* [nginx:stable-alpine](https://hub.docker.com/_/nginx)

## Project structure

```
.
├── .env
├── alfresco
│   └── Dockerfile
├── config
│   ├── nginx.conf
│   └── nginx.htpasswd
├── docker-compose.yml
├── keystores
│   ├── alfresco
│   │   ├── keystore
│   │   ├── keystore-passwords.properties
│   │   ├── ssl-keystore-passwords.properties
│   │   ├── ssl-truststore-passwords.properties
│   │   ├── ssl.keystore
│   │   └── ssl.truststore
│   ├── client
│   │   └── browser.p12
│   └── solr
│       ├── ssl-keystore-passwords.properties
│       ├── ssl-truststore-passwords.properties
│       ├── ssl.repo.client.keystore
│       └── ssl.repo.client.truststore
├── search
│   └── Dockerfile
└── share
    └── Dockerfile
```

* `.env` includes Docker environment variables to set Docker Image release numbers
* `config` folder includes configuration for HTTP Proxy based in NGINX
* `docker-compose.yml` is a Docker Compose template to use ACS Community with SOLR Sharding
* `search` folder includes a customised Dockerfile to configure SOLR Docker Image with Sharding
* `share` folder includes a customised Share Web App
* `keystores` folder includes truststores and keystores for MTLS


# How to use this composition

## Start Docker

Start docker and check the ports are correctly bound.

```bash
$ docker-compose up -d
$ docker ps --format '{{.Names}}\t{{.Image}}\t{{.Ports}}'
sharding-mtls_proxy_1           nginx:stable-alpine                         80/tcp, 0.0.0.0:8080->8080/tcp
sharding-mtls_share_1           alfresco-ce-61-sharding-mtls_share          8000/tcp, 8080/tcp
sharding-mtls_solr6secondary_1  alfresco-ce-61-sharding-mtls_solr6secondary 0.0.0.0:8084->8983/tcp
sharding-mtls_solr6_1           alfresco-ce-61-sharding-mtls_solr6          0.0.0.0:8083->8983/tcp
sharding-mtls_alfresco_1        alfresco-ce-61-sharding-mtls_alfresco       8080/tcp, 0.0.0.0:8443->8443/tcp
sharding-mtls_postgres_1        postgres:10.1                               0.0.0.0:5432->5432/tcp
sharding-mtls_content-app_1     alfresco/alfresco-content-app:master-latest 80/tcp, 8080/tcp
```

## Access

Use the following username/password combination to login.

 - User: admin
 - Password: admin

Alfresco and related web applications can be accessed from the below URIs when the servers have started.

```
http://localhost:8080/alfresco    - Alfresco Repository
https://localhost:8443/alfresco   - Alfresco Repository
https://localhost:8083/solr       - Alfresco Search Services (Shard 0)
https://localhost:8084/solr       - Alfresco Search Services (Shard 1)
http://localhost:8080/share       - Alfresco Share
```
