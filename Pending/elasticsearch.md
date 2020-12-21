# Evergreen & Elasticsearch

2021 Evergreen Online Conference ??

Bill Erickson

Software Development Engineer

King County Library System

https://github.com/berick/Presentations/tree/master/Pending

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

# Why Elasticsearch?

Jeff's talk (find link)
Robust, Simple Clustering
I preferred the search API
Industry backing outside the library world
More Ecom than library
Open source w/ vendor support/additions

# Local Modifications

Collapsing indexes, e.g. no need for subject|geographic


# NOTES

* Fixes https://bugs.launchpad.net/evergreen/+bug/1748814
* Nested filters w/ booleans, etc.
* https://www.elastic.co/guide/en/elasticsearch/reference/6.8/indices-analyze.html
  * See analysis output
```sh
curl -X GET "localhost:9200/bib-search-bibcn-and-icu/_analyze?pretty" -H 'Content-Type: application/json' -d'
{
  "analyzer" : "icu_folding",
  "text" : "En̲ iruḷ vān̲il oḷi nilavāy nī"
}
'
```
* New 'contains', etc. options, including MARC search.

