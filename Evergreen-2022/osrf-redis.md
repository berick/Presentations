# OpenSRF -> RediSRF ?

### 2022 Evergreen Online Conference

Bill Erickson

Software Development Engineer

King County Library System

https://github.com/berick/Presentations/tree/master/Evergreen-2022

---

# Why Replace Ejabberd?

#### Mainly, it's a bear, but also...

* Complications with changing hostnames
* Authentication changes
* Apparmor interactions.
* Default install fails in LXD guests
* XMPP spec has changed multiple times causing breakage

---

# Redis

[redis.io](https://redis.io/)

> The open source, in-memory data store used by millions of developers as a 
> database, cache, streaming engine, and message broker.

[Stack Overflow Developer Survey](https://insights.stackoverflow.com/survey/2021#section-most-loved-dreaded-and-wanted-databases)

> Redis is in its fifth year as the most loved database in Stack Overflow's 
> developer survey.

---

# Why Redis?

* Speed
* Resource Usage
* It does a few things very well
* Ease of Installation and Configuration
* Slimmer Bus Messages / Less Packing & Unpacking
* Native Debugging Tools & Stats Collection

---

# How Does This Implemtation Work?

* Clients push requests to the request queue for a service
    * RPUSH service:open-ils.actor OSRF-MESSAGE-AS-JSON
* Listeners wait for requests to enter the list
    * BLPOP service:open-ils.actor
* Listeners pass requests to an available child -- same as now
* Workers post responses to the calling client's bus address
    * RPUSH client:O515aad3bfbc21d2f OSRF-MESSAGE-AS-JSON
* Client receives the response
    * BLPOP client:515aad3bfbc21d2f
* Client sends follow-up requests directly to worker's bus address
    * RPUSH client:open-ils.actor:d12252828cb22a97 OSRF-MESSAGE-AS-JSON

---

# Fundamental Differences

* No More OpenSRF Routers
* Bus messages are JSON
* Changes to opensrf_core.xml

---

# What Does the OpenSRF Router Do?

* Routes requests to one or more Listeners
* Provides separate destinations for public vs. private services
* Knows what services are actively running for a domain.

---

# RediSRF in Action

---

# Install

* Setup Redis Apt Repository
    * [Redis Apt Repository](https://redis.io/docs/getting-started/installation/install-redis-on-linux/)
    * Apt Repo Not needed with Ubuntu 22.04 and up.
* Install Prereqs
    * sudo apt install redis-server libredis-perl libhiredis-dev   
* Install Branches
    * [OpenSRF Working Branch](https://github.com/berick/OpenSRF/tree/user/berick/lpxxx-opensrf-via-redis-v4)
    * [Evergreen Working Branch](https://github.com/berick/Evergreen/commits/user/berick/lpxxx-osrf-redis)
* Put new OpenSRF config file into place
    * mv /openils/conf/opensrf_core.xml /openils/conf/opensrf_core.xml.orig
    * cp OpenSRF/examples/opensrf_core.xml.example /openils/conf/opensrf_core.xml
* Setup opensrf accounts on the message bus
    * osrf_control -l --reset-message-bus

---

# Timing

TODO timer script / UI demo

* Catalog
* https://eg-osrf-redis.station/eg2/en-US/staff/admin/server/config/metabib_field
    * fleshing the xml transform

---


# Debugging Tools:

    % redis-cli monitor
    % redis-cli memory stats
    % redis-cli client help
    % redis-cli keys client:* 
      1) "client:opensrf.settings-f67a1bb2188e"
      2) "client:opensrf.settings-c9e470abf2c3"

---

# Opportunities

* Direct-to-drone request delivery.
* Sending broadcast/control messages to listeners.
* Could replace memcache / optional key persistence.
* OpenSRF request "backlog" not required.
* Do we need chunking/bundling?

---

# Limitations

### No cross-domain (i.e. cross-brick) routing.

* Affects some Dojo/translator UI's
    * Bricks that share a Redis instance could still cross-communicate

---

# Securing Private Services

If we no longer have public and private XMPP domains...

* ACL's to prevent access to private services
    * See osrf_control --reset-message-bus
    * 3 accounts: 'default', 'opensrf@public', and 'opensrf@private'
* Gateway additionally verifies requests for public services

---

# More TODO

### In-Bus Registry of Running Services (If Needed).

Circ, for example, queries the router to see if Booking is running.  
Could be addressed with configuration (e.g. global flag)

### Code Cleanup

### Docs

---

# Let's Talk

![Redis LOLWUT](media/redis-lolwut.png)

