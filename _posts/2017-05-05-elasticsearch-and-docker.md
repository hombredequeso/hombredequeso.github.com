---
layout: post
title: Elasticsearch and Docker
date: "2017-05-05"
---

Installing and running [Elasticsearch](https://www.elastic.co/downloads/elasticsearch) on a dev machine is a relatively simple task. Yet once you throw in [Kibana](https://www.elastic.co/products/kibana), different Java versions on your dev machine, and you want to be able to start it all easily and stop it all easily on demand, while still in the realm of simple it starts to require some management. On my machine I have various versions of Elasticsearch that I can run up, and along with that a script that tends to look something like:

```
cd c:\Code\proto\elasticsearch5
set JAVA_HOME=C:\Program Files\Java\jre1.8.0_91

cd elasticsearch-5.2.2
start cmd /K .\bin\elasticsearch.bat

cd ..\kibana-5.2.2-windows-x86
start cmd /K .\bin\kibana.bat
```

However, even easier than all that is to simply use Docker, and the official Elasticsearch provided docker images. With that in mind I created a docker-compose file to put it all together to further simplify that task. See [elasticsearch-docker](https://github.com/hombredequeso/elasticsearch-docker).

