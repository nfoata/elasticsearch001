Project: elasticsearch001
=========================

**Goal**: Learn how to install and work with Elasticsearch


Chapter I: Installation on ubuntu 18.04
---------------------------------------

### Download the debian package from the official website
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.3.1.deb
```

NB:
The official website is https://www.elastic.co/fr/
A french website for the installation https://doc.ubuntu-fr.org/elasticsearch

### Optional: Install some tools for working with elasticsearch
```
\# For having the netstat comand and see the open ports on your server
sudo apt-get install net-tools

\# For having cURL and make manual http request 
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
```

### Install the debian package
```
sudo dpkg -i elasticsearch-6.3.1.deb
```

### Check the package is well install and you can see also if there is other versions on you apt lists
```
sudo apt-cache policy elasticsearch
```
or
```
sudo apt-get install apt-show-versions
sudo apt-show-versions elasticsearch
```

### Check the services which are running on your ubuntu
```
service --status-all
```

NB:
The elasticsearch statius is not running normally

### Start elasticsearch service
```
sudo service elasticsearch start
```

### Check the status of elasticsearch service
```
sudo service elasticsearch status
```

```
# See the PID of elasticsearch and what are the open ports
sudo lsof -i | grep elasticsearch

java      2782   elasticsearch  172u  IPv6  37504      0t0  TCP ip6-localhost:9300 (LISTEN)
java      2782   elasticsearch  173u  IPv6  37511      0t0  TCP localhost:9300 (LISTEN)
java      2782   elasticsearch  181u  IPv6  37549      0t0  TCP ip6-localhost:9200 (LISTEN)
java      2782   elasticsearch  182u  IPv6  37550      0t0  TCP localhost:9200 (LISTEN)
```

```
sudo netstat -laputen

Connexions Internet actives (serveurs et établies)
Proto Recv-Q Send-Q Adresse locale          Adresse distante        Etat       Utilisatr  Inode      PID/Program name    
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      101        15838      309/systemd-resolve 
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      0          18840      564/cupsd           
tcp        0      0 10.0.2.15:58236         192.30.253.124:443      ESTABLISHED 1000       71926      2150/firefox        
tcp        0      0 10.0.2.15:34036         66.102.1.189:443        ESTABLISHED 1000       71565      2150/firefox        
tcp        0      0 10.0.2.15:46832         216.58.201.234:443      ESTABLISHED 1000       75709      2150/firefox        
tcp        0      0 10.0.2.15:40426         216.58.201.238:443      ESTABLISHED 1000       73698      2150/firefox        
tcp6       0      0 127.0.0.1:9200          :::*                    LISTEN      121        37550      2782/java           
tcp6       0      0 ::1:9200                :::*                    LISTEN      121        37549      2782/java           
tcp6       0      0 127.0.0.1:9300          :::*                    LISTEN      121        37511      2782/java           
tcp6       0      0 ::1:9300                :::*                    LISTEN      121        37504      2782/java           
tcp6       0      0 ::1:631                 :::*                    LISTEN      0          18839      564/cupsd           
udp    52992      0 0.0.0.0:5353            0.0.0.0:*                           115        18290      542/avahi-daemon: r 
udp        0      0 0.0.0.0:39190           0.0.0.0:*                           115        18292      542/avahi-daemon: r 
udp    29952      0 127.0.0.53:53           0.0.0.0:*                           101        15837      309/systemd-resolve 
udp        0      0 0.0.0.0:68              0.0.0.0:*                           0          20263      744/dhclient        
udp        0      0 0.0.0.0:631             0.0.0.0:*                           0          18854      573/cups-browsed    
udp6       0      0 :::35663                :::*                                115        18293      542/avahi-daemon: r 
udp6   34560      0 :::5353                 :::*                                115        18291      542/avahi-daemon: r
```


### Check the elasticsearch service is well install
- Open your browser
- Go on the following URL

- - - 

Chapter II: Configure and install your elasticsearch service
------------------------------------------------------------

### Reminder
In Linux/Debian environments, the configuration files are in:
`/etc/elasticsearch/`

To know where are stored the log files and the data, you can open elasticsearch.yml.
By default, these files are respectively in `/var/log/elasticsearch` and in `/var/lib/elasticsearch/`

The official documentation is available at https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html

### Open the main configuration file elasticsearch.yml and fill in the following variables
cluster.name: my-application
node.name: node-1-dev

### Restart the service
```
sudo service elasticsearch restart
```

### Test that the configuration has been taken in account
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

### See the indices on elasticsearch
```
curl -X GET http://localhost:9200/_cat/indices?v

health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```

NB: This last line means there is no index within elasticsearch for now
https://www.elastic.co/guide/en/elasticsearch/reference/current/_list_all_indices.html

- - -

Chapter III: Use and play with elasticsearch
--------------------------------------------

### Introduction

**Index**: An index can be seen as a database with its rules for the replication, shards ... 
> e.g: a shop

**Type**: A type can be seen as a table. 
> e.g: the DVDs in a shop, the books in a shop

**Alias**: An alias is another name of an index or can be also a pre configured filter accessible via this new name (a kind of another view)
> e.g: The media which contains both DVDs and books
 
>      The DVDs with Leonardo DiCaprio (pre configurerd filtering on DVDs)

>      The DVDs of USA = DVDs of North America (same name for accessing the same data)

**Document**: A document is another name for an item, a record within the database
> e.g: The DVD untitled Blood Diamond with Leonardo DiCaprio as main actor and coming from USA

### Make your first index

The official documenation for the indices https://www.elastic.co/guide/en/elasticsearch/reference/6.3/indices-create-index.html

When you build your index, you can fix the number of shard and replicas

```
curl -X PUT -H 'Content-Type: application/json' http://localhost:9200/myfirstindex -d'
{                                        
    "settings" : {                       
        "index" : {                      
            "number_of_shards" : 5,      
            "number_of_replicas" : 1     
        }                                
    }                                    
}'
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

### Make a second index and specify the mapping

```
curl -X PUT -H 'Content-Type: application/json' http://localhost:9200/mysecondindex -d'
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
}'

# curl -X PUT -H 'Content-Type: application/json' http://localhost:9200/mysecondindex -d '{"settings":{"index":{"number_of_shards":3,"number_of_replicas":1}},"mappings":{"myfirsttype":{"properties":{"myfirstfield":{"type":"text"}}}}}'
{"acknowledged":true,"shards_acknowledged":true,"index":"mysecondindex"}
```

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

At last, you could need to update a mapping
```
curl -X PUT http://localhost:9200/mysecondindex/_mappings/myfirsttype
```

### Make on the first index some aliases
The official documentation for aliases https://www.elastic.co/guide/en/elasticsearch/reference/6.3/indices-aliases.html

```
curl -X GET http://localhost:9200/_aliases

{"mysecondindex":{"aliases":{}},"myfirstindex":{"aliases":{}}}
```

As we can see, we do not have any alias for both indices

So, let's create our first alias on our first index
```
curl -X POST "localhost:9200/_aliases" -H 'Content-Type: application/json' -d '
{
    "actions" : [
        { "add" : { "index" : "myfirstindex", "alias" : "myfirstalias" } }
    ]
}
'

{"acknowledged":true}
```
To verify that everything goes well:
1. Redo a call to elasticsearch to see the current aliases
```
curl -X GET http://localhost:9200/_aliases

{"mysecondindex":{"aliases":{}},"myfirstindex":{"aliases":{"myfirstalias":{}}}}
```
2. Make a call via to `myfirstindex` and via `myfirstalias` and compare the results
```
curl -X GET http://localhost:9200/myfirstindex

{"myfirstindex":{"aliases":{"myfirstalias":{}},"mappings":{},"settings":{"index":{"creation_date":"1531317424617","number_of_shards":"5","number_of_replicas":"1","uuid":"QZj3F6eqQriKXS6tY2SIXg","version":{"created":"6030199"},"provided_name":"myfirstindex"}}}}


**versus**


curl -X GET http://localhost:9200/myfirstalias

{"myfirstindex":{"aliases":{"myfirstalias":{}},"mappings":{},"settings":{"index":{"creation_date":"1531317424617","number_of_shards":"5","number_of_replicas":"1","uuid":"QZj3F6eqQriKXS6tY2SIXg","version":{"created":"6030199"},"provided_name":"myfirstindex"}}}}
```

For deleting an alias, it is reaaly simple.
Indeed, you have only to use the term **delete** instead of **add** within the action property of the JSON body request.
e.g:
```
curl -X POST "localhost:9200/_aliases" -H 'Content-Type: application/json' -d ' 
{
    "actions" : [
        { "delete" : { "index" : "myfirstindex", "alias" : "myfirstalias" } }
    ]
}
'

But until now, we do not have yet see all the power of the aliases.
Indeed, with aliases, it will be possible to regroup indices or to pre configured a filter.
curl -X POST "localhost:9200/_aliases" -H 'Content-Type: application/json' -d'
{
    "actions" : [
        { "add" : { "index" : "myfirstindex", "alias" : "mycommonalias" } },
        { "add" : { "index" : "mysecondindex", "alias" : "mycommonalias" } }
    ]
}
'
```

or another way to do the same stuff

```
curl -X POST "localhost:9200/_aliases" -H 'Content-Type: application/json' -d'
{
    "actions" : [
        { "add" : { "indices" : ["myfirstindex", "mysecond*"], "alias" : "mycommonalias" } }
    ]
}
'
```

After having put the `mycommonalias`, we can check that we have well the information for both of the indices
```
curl -X GET "http://localhost:9200/mycommonalias"

{"mysecondindex":{"aliases":{"mycommonalias":{}},"mappings":{"myfirsttype":{"properties":{"myfirstfield":{"type":"text"}}}},"settings":{"index":{"creation_date":"1531386908605","number_of_shards":"3","number_of_replicas":"1","uuid":"fkyFaOYcRBmyBRMQ11vheQ","version":{"created":"6030199"},"provided_name":"mysecondindex"}}},"myfirstindex":{"aliases":{"mycommonalias":{},"myfirstalias":{}},"mappings":{},"settings":{"index":{"creation_date":"1531317424617","number_of_shards":"5","number_of_replicas":"1","uuid":"QZj3F6eqQriKXS6tY2SIXg","version":{"created":"6030199"},"provided_name":"myfirstindex"}}}}nfoata@nfoata-VirtualBox:~/euby/elasticsearch001$ curl -X GET "http://localhost:9200/mycommonalias/_settings
```

```
curl -X GET "http://localhost:9200/mycommonalias/_settings"

{"myfirstindex":{"settings":{"index":{"creation_date":"1531317424617","number_of_shards":"5","number_of_replicas":"1","uuid":"QZj3F6eqQriKXS6tY2SIXg","version":{"created":"6030199"},"provided_name":"myfirstindex"}}},"mysecondindex":{"settings":{"index":{"creation_date":"1531386908605","number_of_shards":"3","number_of_replicas":"1","uuid":"fkyFaOYcRBmyBRMQ11vheQ","version":{"created":"6030199"},"provided_name":"mysecondindex"}}}}
```

```
curl -X GET "http://localhost:9200/mycommonalias/_mappings"

{"myfirstindex":{"mappings":{}},"mysecondindex":{"mappings":{"myfirsttype":{"properties":{"myfirstfield":{"type":"text"}}}}}}
```

### Insert documents

```
curl -X PUT -H 'Content-Type: application/json' http://localhost:9200/shop_index/dvd_type/1 -d '
{
    "title": "The Godfather",
    "director": "Francis Ford Coppola",
    "year": 1972,
    "genres": ["Crime", "Drama"]
}
'

{"_index":"shop_index","_type":"dvd_type","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
```

As you can see, if you send a request with a no existing index and so with no type, elasticsearch will create them for adding the new document.

Check what elasticsearch has done:
1.0 The settings
```
curl -X GET http://localhost:9200/shop_index/_settings

{"shop_index":{"settings":{"index":{"creation_date":"1531399579609","number_of_shards":"5","number_of_replicas":"1","uuid":"Jc6b4Zl_QAyWS_BTqyJeCw","version":{"created":"6030199"},"provided_name":"shop_index"}}}}
# by default elasticsearch put 5 shards and 1 replica
```
2.0 The mappings
```
curl -X GET http://localhost:9200/shop_index/_mappings

{"shop_index":{"mappings":{"dvd_type":{"properties":{"director":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"genres":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"title":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"year":{"type":"long"}}}}}}
```

- - -

Chapter IV: Appendices
----------------------

### Markdown syntax
The official format is available here: https://help.github.com/articles/basic-writing-and-formatting-syntax/

### Listing of the links present within this page

- Official: https://www.elastic.co/fr/
- Settings: https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html
- Indices:  https://www.elastic.co/guide/en/elasticsearch/reference/current/_list_all_indices.html
            https://www.elastic.co/guide/en/elasticsearch/reference/6.3/indices-create-index.html
- Aliases:  https://www.elastic.co/guide/en/elasticsearch/reference/6.3/indices-aliases.html

