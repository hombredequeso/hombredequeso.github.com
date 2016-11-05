---
layout: post
title: Beginner's Introduction to Elasticsearch
date: "2016-11-05"
---

Having found myself working on a search project using the amazing [Elasticsearch](https://www.elastic.co/), I recently gave a beginner's introduction to Elasticsearch at work.
[A very brief overview](http://www.slideshare.net/MarkCheeseman/elasticsearch-introduction-68208299) was followed by a [live demo](https://github.com/hombredequeso/elasticsearch-intro-presentation/blob/master/kibana.txt).


# Data Source

As a data source I used 'Airplane Crashes since 1908' -- primarily chosen because it had the types of data I wanted to demo, but morbidly interesting in its own right!

Source data was obtained from [Airplane Crashes Since 1908 on Kaggle](https://www.kaggle.com/saurograndi/airplane-crashes-since-1908)

Csv converted to json using [nodejs csvtojson](https://www.npmjs.com/package/csvtojson)

Json reformatted and [bulk posted to elasticsearch](http://kevinmarsh.com/2014/10/23/using-jq-to-import-json-into-elasticsearch.html), using [jq](https://stedolan.github.io/jq/)
```
cat AirplaneCrashes.json | jq -c '.[] | {"index": {"_index": "airplanecrash", "_type": "type1"}}, .' | curl -XPOST localhost:9200/_bulk --data-binary @-
```

