# OpenSRF -> RediSRF ?

### 2022 Evergreen Online Conference

Bill Erickson

Software Development Engineer

King County Library System

https://github.com/berick/Presentations/tree/master/Evergreen-2022

---

# Why Replace Ejabberd?

It's a bear.

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
* Ease of Installation and Configuration
* Slimmer Bus Messages / Less Packing & Unpacking
* Natvie Debugging Tools & Stats Collection
* Say Goodbye to Ejabberd :fireworks:

---

# How Does It Work?

* Clients push requests to the request queue for a service
    * LPUSH open_ils:actor osrf_msg_json
* Listeners wait for requests to enter the list
    * BLPOP open_ils:actor
* Listeners pass requests to an available child
* Workers post responses to the calling client's bus address
    * LPUSH websocket:9c32cea1a260 osrf_msg_json
* Client sends follow-up requests directly to worker's bus address
    * LPUSH client:0a00bac476a9 osrf_msg_json

---

# Fundamental Differences

* No More OpenSRF Routers
* Bus messages are JSON
* Clients pull messages instead of receiving them.

---

# RediSRF in Action

---

# Install

* Setup Redis Apt Repository
    * [Redis Apt Repository](https://redis.io/docs/getting-started/installation/install-redis-on-linux/)
    * Not needed with Ubuntu 22.04 and up.
* Install Prereqs
    * sudo apt install redis-server libredis-perl libhiredis-dev   
* Install Branches
    * [OpenSRF Working Branch](https://github.com/berick/OpenSRF/tree/user/berick/lpxxx-opensrf-via-redis-v4)
    * [Evergreen Working Branch](https://github.com/berick/Evergreen/commits/user/berick/lpxxx-osrf-redis)

---

# Timing

TODO timer script / UI demo

---



# Debugging Tools:

    % redis-cli monitor
    % redis-cli memory stats
    % redis-cli client help
    % redis-cli keys open*
      1) "opensrf.settings-f67a1bb2188e"
      2) "opensrf.settings-c9e470abf2c3"

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
* NOTE: Bricks that share a Redis instance could still cross-communicate
* Routing potentially code-able if required.

---

# Pending Work

### Securing Private Services (e.g. ACL's)

    ACL SETUSER opensrf@public.localhost on >demo123 -@all
    ACL SETUSER opensrf@public.localhost +set +get +lpop +blpop +lpush +llen
    ACL SETUSER opensrf@public.localhost ~client:*
    ACL SETUSER opensrf@public.localhost ~open_ils:actor
    ACL SETUSER opensrf@public.localhost ~open_ils:circ
    ACL SETUSER opensrf@public.localhost ~open_ils:cat

### In-Bus Registry of Running Services (If Needed).

Circ, for example, queries the router to see if Booking is running.  
Could be addressed with configuration (e.g. global flag)

### Code Cleanup

### Docs

---

# Questions and Comments

