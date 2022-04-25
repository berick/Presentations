# Living the Life Elastic

2022 Evergreen Online Conference

Bill Erickson

Software Development Engineer

King County Library System

https://github.com/berick/Presentations/tree/master/Evergreen-2022

---

# Project Goal

* Improve search speed for staff while using the Evergreen catalog.

---

# What is Elasticsearch?

> Elasticsearch is a distributed, open source search and analytics
> engine for all types of data, including textual, numerical,
> geospatial, structured, and unstructured. Elasticsearch is built on
> Apache Lucene and was first released in 2010 by Elasticsearch N.V.
> (now known as Elastic). Known for its simple REST APIs, distributed
> nature, speed, and scalability...

Source: https://www.elastic.co/what-is/elasticsearch

---

# Why Elasticsearch?

* Similar use of Solr
* Jeff's elastic talk (find link)
* Robust, Simple Clustering
* I liked the API
* Industry use outside the library world
* Open source w/ vendor support/additions

---

# Local Modifications

* Collapsing indexes, e.g. no need for subject|geographic
* 'contains exact'  match opt
* MARC match option selector
    * Plus MARC regex search (TODO example)
---

# NOTES

* Grid of supported features?
* Highlighting support
* did-you-mean
* popularity ranking?
* give me everything keyword search *:*
* Regular indexing, 1 minute all modified, 2 seconds new recs only.
* normalizing / isbn/issn
* record / copy counts
* config files
* Analysis example

* Reindex speed
    * KCLS 1135434 records; 3607758 items
    * 4 parallel: 1 hour 45 mins

* Full reindex maybe 3 times in a year

* Ease of administration
* Cluster setup
* Show indexed document and how to access
* Fixes https://bugs.launchpad.net/evergreen/+bug/1748814
* Nested filters w/ booleans, etc.
* https://www.elastic.co/guide/en/elasticsearch/reference/6.8/indices-analyze.html
  * See analysis output
```sh
curl -X GET "localhost:9200/bib-search/_analyze?pretty" -H 'Content-Type: application/json' -d'
{
  "analyzer" : "icu_folding",
  "text" : "En̲ iruḷ vān̲il oḷi nilavāy nī"
}
'
```
* JQ is cool / diagnosing a stopwords issue / doing "'" stripping
  * It's a wonderful life, I'm not tired
```sh

$ cat search.json 
{
  "size": 10,
  "from": 0,
  "sort": [
    {
      "_score": "desc"
    }
  ],
  "query": {
    "bool": {
      "must": {
        "multi_match": {
          "query": "i'm not tired",
          "fields": [
            "title.*"
          ],
          "operator": "and",
          "type": "most_fields"
        }
      }
    }
  }
}

curl -s -X GET 'http://localhost:9200/bib-search/_search' \
	-H 'Content-Type: application/json' -d @search.json \
    | jq '.hits.hits[] | ._source."title|maintitle"'

"I'm really not tired /"
"I'm tired and other body feelings /"

```

