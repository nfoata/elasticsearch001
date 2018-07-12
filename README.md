# elasticsearch001
Elasticsearch test

# Chapter I: Installation on ubuntu 18.04

## Download the debian package from the official website
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.3.1.deb
```

NB:
The official website is https://www.elastic.co/fr/
A french website for the installation https://doc.ubuntu-fr.org/elasticsearch

## Optional: Install some tools for working with elasticsearch
```
\# For having the netstat comand and see the open ports on your server
sudo apt-get install net-tools

\# For having cURL and make manual http request 
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
```

## Install the debian package
```
sudo dpkg -i elasticsearch-6.3.1.deb
```

## Check the package is well install and you can see also if there is other versions on you apt lists
```
sudo apt-cache policy elasticsearch
```
or
```
sudo apt-get install apt-show-versions
sudo apt-show-versions elasticsearch
```

## Check the services which are running on your ubuntu
```
service --status-all
```

NB:
The elasticsearch statius is not running normally

## Start elasticsearch service
```
sudo service elasticsearch start
```

## Check the status of elasticsearch service
```
sudo service elasticsearch status
```

### Check the elasticsearch service is well install
- Open your browser
- Go on the following URL


# Chapter II: Configure your elasticsearch service

## Reminder
In Linux/Debian environments, the configuration files are in:
`/etc/elasticsearch/`

To know where are stored the log files and the data, you can open elasticsearch.yml.
By default, these files are respectively in `/var/log/elasticsearch` and in `/var/lib/elasticsearch/`

##
The official documentation is available at https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html

## Open the main configuration file elasticsearch.yml and fill in the following variables
cluster.name: my-application
node.name: node-1-dev

## Restart the service
```
sudo service elasticsearch restart
```

## Test that the configuration has been taken in account
```
curl -X GET http://localhost:9200

{
  "name" : "node-1-dev",
  "cluster_name" : "my-application",
  "cluster_uuid" : "mZaeIeiHR_ariqn1WoKNLg",
  "version" : {
    "number" : "6.3.1",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "eb782d0",
    "build_date" : "2018-06-29T21:59:26.107521Z",
    "build_snapshot" : false,
    "lucene_version" : "7.3.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

## See the indices on elasticsearch
```
curl -X GET http://localhost:9200/_cat/indices?v

health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```

NB: This last line means there is no index within elasticsearch for now
https://www.elastic.co/guide/en/elasticsearch/reference/current/_list_all_indices.html

# Chapter III: Play with elasticsearch

## Introduction

**Index**: An index can be seen as a database with its rules for the replication, shards ... 
> e.g: a shop

**Type**: A type can be seen as a table. 
> e.g: the DVDs in a shop, the books in a shop

**Alias**: An alias is another name of an index or can be also a pre configured filter accessible via this new name (a kind of another view)
> e.g: The media which contains both DVDs and books 
>      The DVDs with Leonardo DiCaprio (pre configurerd filtering on DVDs)
>      The DVDs of USA = DVDs of North America (same name for accessing the same data)

## Make your first index

The official documenation for the indices https://www.elastic.co/guide/en/elasticsearch/reference/6.3/indices-create-index.html

When you build your index, you can fix the number of shard and replicas

```
curl -X PUT http://localhost:9200/myfirstindex
{                                        \
    "settings" : {                       \
        "index" : {                      \
            "number_of_shards" : 5,      \
            "number_of_replicas" : 1     \
        }                                \
    }                                    \
}
```

Warnings:
- The index name must be in lower case
- You have to use PUT and not POST http verb

After or having information on your index, you can directly make the following request:
```
curl -X GET http://localhost:9200/myfirstindex/_settings
```

Moreover, if you ask again what are the available indices on elasticsearch server, now you will have one:

```
curl -X GET http://localhost:9200/_cat/indices?v

health status index        uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   myfirstindex QZj3F6eqQriKXS6tY2SIXg   5   1          0            0      1.2kb          1.2kb
```

## Make a second index and specify the mapping

```
curl -X PUT http://localhost:9200/mysecondindex \
{
  "settings" : { 
    "index" : { 
      "number_of_shards" : 3, 
      "number_of_replicas" : 1  
    }  
  },                                                                    
  "mappings" : { 
    "myfirsttype": {
      "properties": {  
        "myfirstfield": {"type":"text"}             
      }                                           
    } 
  }
}

# curl -X PUT http://localhost:9200/mysecondindex {"settings":{"index":{"number_of_shards":3,"number_of_replicas":1}},"mappings":{"myfirsttype":{"properties":{"myfirstfield":{"type":"text"}}}}}
# curl -X PUT http://localhost:9200/mysecondindex -D '{\"settings":{\"index\":{\"number_of_shards\":3,\"number_of_replicas\":1}},\"mappings":{\"myfirsttype\":{\"properties\":{\"myfirstfield\":{\"type\":\"text\"}}}}}'
{"acknowledged":true,"shards_acknowledged":true,"index":"mysecondindex"}```

NB:
- If you make a mistake on your index, you can delete it as
```
curl -X DELETE http://localhost:9200/mysecondindex

{"acknowledged":true}
```

Then you can check that:
1. elasticsearch has the new index named `mysecondindex` as previously
```
curl -X GET http://localhost:9200/_cat/indices?v

health status index         uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   mysecondindex fkyFaOYcRBmyBRMQ11vheQ   3   1          0            0       690b           690b
yellow open   myfirstindex  QZj3F6eqQriKXS6tY2SIXg   5   1          0            0      1.2kb          1.2kb
```
2. the index named `mysecondindex` has the correct settings
```
curl -X GET http://localhost:9200/mysecondindex/_settings

{"mysecondindex":{"settings":{"index":{"creation_date":"1531386908605","number_of_shards":"3","number_of_replicas":"1","uuid":"fkyFaOYcRBmyBRMQ11vheQ","version":{"created":"6030199"},"provided_name":"mysecondindex"}}}}
```
3. the index named `mysecondindex` has the correct mappings
```
curl -X GET http://localhost:9200/mysecondindex/_mappings

{"mysecondindex":{"mappings":{"myfirsttype":{"properties":{"myfirstfield":{"type":"text"}}}}}}
```


## Make a third index and specify also some aliases
The official documentation for aliases https://www.elastic.co/guide/en/elasticsearch/reference/6.3/indices-aliases.html

```
curl -X GET http://localhost:9200/_aliases

{"myfirstindex":{"aliases":{}}}
```

As we can see, we do not have any alias.

# Chapter V: Appendices

## Markdown syntax
The official format is available here: https://help.github.com/articles/basic-writing-and-formatting-syntax/

