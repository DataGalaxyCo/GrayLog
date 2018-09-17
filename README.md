# GrayLog

a tutorial to how install and config GrayLog on Ubuntu.

## Prerequisites

Taking a minimal server setup as base will need this additional packages:

```
$ sudo apt-get update && sudo apt-get upgrade
$ sudo apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen
```


## MongoDB

The official MongoDB repository provides the most up-to-date version and is the recommended way of installing MongoDB for Graylog:

```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
$ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
$ sudo apt-get update
$ sudo apt-get install -y mongodb-org

```

The last step is to enable MongoDB during the operating system’s startup:

```
$ sudo systemctl daemon-reload
$ sudo systemctl enable mongod.service
$ sudo systemctl restart mongod.service
```
## Elasticsearch

Graylog 2.4.x should be used with Elasticsearch 5.x, please follow the installation instructions from the Elasticsearch installation guide:

```
$ wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
$ echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
$ sudo apt-get update && sudo apt-get install elasticsearch
```

Make sure to modify the Elasticsearch configuration file `(/etc/elasticsearch/elasticsearch.yml)` and set the cluster name to graylog additionally you need to uncomment (remove the # as first character) the line:

```
cluster.name: graylog
```

After you have modified the configuration, you can start Elasticsearch:

```
$ sudo systemctl daemon-reload
$ sudo systemctl enable elasticsearch.service
$ sudo systemctl restart elasticsearch.service

```

## Graylog

Now install the Graylog repository configuration and Graylog itself with the following commands:

```
$ wget https://packages.graylog2.org/repo/packages/graylog-2.4-repository_latest.deb
$ sudo dpkg -i graylog-2.4-repository_latest.deb
$ sudo apt-get update && sudo apt-get install graylog-server
```

Follow the instructions in your `/etc/graylog/server/server.conf` and add `password_secret` and `root_password_sha2`. These settings are mandatory and without them, Graylog will not start!

You need to use the following command to create your `root_password_sha2`:

```
echo -n yourpassword | sha256sum
```

and put hash password in `password_secret` and `root_password_sha2` .


To be able to connect to Graylog you should set `rest_listen_uri` and `web_listen_uri` to the public host name or a public IP address of the machine you can connect to. More information about these settings can be found in Configuring the web interface.

here is an example:

```
rest_listen_uri = http://192.168.100.100:12900/api/
web_listen_uri = http://192.168.100.100:9000/
```

and uncomment this line :

```
root_timezone = UTC
```

The last step is to enable Graylog during the operating system’s startup:

```
$ sudo systemctl daemon-reload
$ sudo systemctl enable graylog-server.service
$ sudo systemctl start graylog-server.service
```

The next step is to ingest messages into your Graylog and extract the messages with extractors or use the Pipelines to work with the messages.
