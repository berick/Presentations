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

# A Brief History

* Evergreen Begins
* Rise of [Solr](https://solr.apache.org/) for discovery layers
* KCLS adopts EG, soon migrates to 3rd-party catalog
* Jeff G presents on Elasticsearch-driven mobile catalog (TODO link?)
* Bill starts proof-of-concept implementation
* Blake GH opens [LP1844418](https://bugs.launchpad.net/evergreen/+bug/1844418)
* KCLS limited staff use late 2020
* KCLS general use late 2021

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
* Jeff G's elastic talk (find link)
* I like the API
* Industry use outside the library world
* Robust, Simple Clustering
* Open source w/ vendor support/additions

---

# What's Implemented?

### Angular Staff Catalog Only

* Keyword, Title, Author, etc. searches
* Some Numeric Searches (e.g. not Item Barcode)
* MARC searches

---

# Query String Examples

### Query String supported added to Keyword field

* Give me everything: 
    * \*:\*
* Give me the new stuff:
    * pubdate:2022
* Boolean Grouping
    * (kw:dogs AND pubdate:2022) OR (ti:cats AND NOT pubdate:2022)

---

# Local Modifications

* Collapsing Facets
* 'contains exact'  match opt
* MARC match option selector
* MARC regex search
    * .{24}[^6]{3}.{13}
    * [Graphic Novels](https://evgstaging.kcls.org/eg2/en-US/staff/catalog/search?org=1&limit=10&marcTag=008&marcTag=655&marcSubfield=&marcSubfield=a&marcValue=.%7B24%7D%5B%5E6%5D%7B3%7D.%7B13%7D&marcValue=graphic%20novels&matchOp=regexp&matchOp=phrase)

---

# Other Stuff

* Reindex speed
    * KCLS 1135434 records; 3607758 items
    * 4 parallel: 1 hour 45 mins
* Debugging Indexes
    * curl -s http://localhost:9200/bib-search/_doc/891066 | jq -C . | less -R
* Create and run side-by-side datasets
* Full search results / no estimates
* Takes heavy search query load off primary PG Database

---


# Missing (Future?) Features

* Search Results Highlight Support
* Sort by Populatrity
* "Did You Mean" (in progress)

---

# Installation

    !sh

    % sudo apt install openjdk-11-jre-headless
    % wget 'https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.8.11.deb'
    % sudo dpkg -i elasticsearch-6.8.11.deb
    % sudo systemctl start elasticsearch
    % sudo systemctl enable elasticsearch


---

# Production Setup

* Two dedicated VMs with ~100G disk and 24G RAM
* Load-balanced with one write node, one replica node.
* A full index uses about 36G disk
* Apply firewall (iptables) to limit port 9200 access

---

# Sysadmin Tools

    !sh

    curl -XGET 'localhost:9200/_cat/shards?h=index,shard,prirep,state,unassigned.reason'
    curl -XGET 'localhost:9200/bib-search/_count?pretty' 
    curl -XGET 'localhost:9200/_cluster/health?pretty'
    curl -XGET 'localhost:9200/bib-search/_search?pretty&q=dogs'

---


# Plugin - International Components for Unicode

[https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-icu.html](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-icu.html)

    !sh
    % cd /usr/share/elasticsearch/
    % sudo bin/elasticsearch-plugin install analysis-icu

    % curl -X GET "localhost:9200/bib-search/_analyze?pretty" -H 'Content-Type: application/json' -d'
    {
      "analyzer" : "icu_folding",
      "text" : "En̲ iruḷ vān̲il oḷi nilavāy nī"
    }
    ' | jq -C . | less -R

---

# NOTES

* pre-insert normazliation: isbn / issn / pubdate / sorters
* ascii folding example
* Grid of supported features?
* Regular indexing, 1 minute all modified, 2 seconds new recs only.
* record / copy counts
* config files
* Analysis example
* Ease of administration
* Cluster setup
* Fixes https://bugs.launchpad.net/evergreen/+bug/1748814
* https://www.elastic.co/guide/en/elasticsearch/reference/6.8/indices-analyze.html
  * See analysis output
* JQ is cool / diagnosing a stopwords issue / doing "'" stripping
  * It's a wonderful life, I'm not tired

    !sh

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
