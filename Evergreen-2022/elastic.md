# Living the Life Elastic

2022 Evergreen Online Conference

Bill Erickson

Software Development Engineer

King County Library System

https://github.com/berick/Presentations/tree/master/Evergreen-2022

---

# Project Goal

* Improve Evergreen catalog search speed for staff.

---

# A Brief History

* Evergreen Begins
* Rise of [Solr](https://solr.apache.org/) for discovery layers
* KCLS adopts EG, soon migrates to 3rd-party catalog
* Jeff G presents on Elasticsearch-driven mobile catalog (TODO link?)
* Bill starts Elasticsearch proof-of-concept implementation
* Blake GH opens [LP1844418](https://bugs.launchpad.net/evergreen/+bug/1844418)
* Angular Catalog development proceeds
* Angular Catalog + Elasticsearch limited staff use at KCLS 2020
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
* Ease of use
* Broad feature set
* Excellent documentation
* I liked the API
* Industry use outside the library world
* Clustering / Replication
* Open source w/ vendor support/additions

---

# Other Benefits

* Indexing speed
    * KCLS 1.1M records; 3.6M items
    * 4 parallel: 1 hour 45 mins
* Searches report total result count / no estimates
* Takes heavy search query load off primary PG Database
* Opportunities for new types of searches w/o backend development.
* Parallel, Interchangeable Datasets

---

# What's Implemented?

### Angular Staff Catalog Only

* Keyword, Title, Author, etc. search
* Some Numeric Searches (e.g. not Item Barcode)
* MARC search

---

# Query String Examples

### Query String supported added to Keyword field

* Give me everything: 
    * \*:\*
* Give me the new stuff:
    * pubdate:2022
* Boolean Grouping
    * (kw:dogs AND (pubdate:2021 OR pubdate:2022)) OR (ti:cats AND NOT pubdate:2022)

---

# Local Additions & Modifications

* 'contains exact'  match opt
* MARC match option selector
* MARC regex search
    * .{24}[^6]{3}.{13}
    * [Graphic Novels](https://evgstaging.kcls.org/eg2/en-US/staff/catalog/search?org=1&limit=10&marcTag=008&marcTag=655&marcSubfield=&marcSubfield=a&marcValue=.%7B24%7D%5B%5E6%5D%7B3%7D.%7B13%7D&marcValue=graphic%20novels&matchOp=regexp&matchOp=phrase)


---

# Pending Features

* "Did You Mean" (in progress)
* Search Results Highlight Support
* Sort by Populatrity

## Script-Based Sorting

    !js
    sort: {
        _script: {
            type: "number",
            script: {
                lang: "painless",
                source: "Integer.parseInt(doc['popularity'].value) * _score"
            },
            order: "asc"
        }
    }


---

# Installation

    !sh
    $ sudo apt install openjdk-11-jre-headless

    $ wget 'https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.8.11.deb'

    $ sudo dpkg -i elasticsearch-6.8.11.deb

    $ sudo systemctl start elasticsearch

    $ sudo systemctl enable elasticsearch

    $ sudo cpan Search::Elasticsearch::Client::6_0

---

# Plugin - International Components for Unicode

[https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-icu.html](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-icu.html)

    !sh
    $ cd /usr/share/elasticsearch/

    $ sudo bin/elasticsearch-plugin install analysis-icu

    $ curl -X GET "localhost:9200/bib-search/_analyze?pretty" \
        -H 'Content-Type: application/json' -d'
    {
      "analyzer" : "icu_folding",
      "text" : "En̲ iruḷ vān̲il oḷi nilavāy nī"
    }
    ' | jq -C . | less -R



---

# KCLS Production Setup

* Two dedicated VMs with ~100G disk and 24G RAM
* Load-balanced with one write node, one replica node.
* A full index uses about 36G disk
* Apply firewall (iptables) to limit port 9200 access
* 2 Indexer Scripts

---

# Sysadmin Tools

    !sh

    curl -s http://localhost:9200/bib-search/_doc/891066 | jq -C . | less -R

    curl -XGET 'localhost:9200/_cat/shards?h=index,shard,prirep,state,unassigned.reason'

    curl -XGET 'localhost:9200/bib-search/_count?pretty' 

    curl -XGET 'localhost:9200/_cluster/health?pretty'

    curl -XGET 'localhost:9200/bib-search/_search?pretty&q=dogs' | jq -C . | less -R

    #

---

# 

