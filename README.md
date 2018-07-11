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


# Chapter V: Appendices

## Markdown syntax
The official format is available here: https://help.github.com/articles/basic-writing-and-formatting-syntax/

