# OpenSRF-Over-Redis

### Exploring Replacing Ejabberd/XMPP For OpenSRF

2022 Evergreen Online Conference

Bill Erickson

Software Development Engineer

King County Library System

https://github.com/berick/Presentations/tree/master/Evergreen-2022

---

# Why Replace Ejabberd / XMPP?

Ejabberd install and setup has traditionally been one of the clunkiest parts 
of the Evergreen/OpenSRF installation.

Authentication changes https://bugs.launchpad.net/opensrf/+bug/1703411 

---

# Redis

[redis.io](https://redis.io/)

> The open source, in-memory data store used by millions of developers as a 
> database, cache, streaming engine, and message broker.

[Stack Overflow Developer Survey](https://insights.stackoverflow.com/survey/2021#section-most-loved-dreaded-and-wanted-databases)

> Redis is in its fifth year as the most loved database in Stack Overflow's 
> developer survey.

---

# RediSRF in Action

---

# Install

## Prereqs

    % sudo apt install redis-server libredis-perl libhiredis-dev

## Branches

* TODO
* TODO

---

# Timing

TODO timer script / demo

---

# Fundamental Differences

* No More OpenSRF Routers
* Bus messages are JSON
* Clients pull messages instead of receiving them.
* Redis accounts/authentication optional

---

# Benefits

* Speed
* Resource Usage (TODO show top)
* Ease of Installation and Configuration
* Slimmer Bus Messages / Less Packing & Unpacking
* Intuitive Flow of Data
* Stats collection
* Say Goodbye to Ejabberd

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
* OpenSRF request "backlog" not required.

---

# Limitations

* No cross-domain (i.e. cross-brick) routing.
    * Affects some Dojo/translator UI's
    * NOTE: Bricks that share a Redis instance could still cross-communicate
* Requests sent to a service that is not running will linger unanswered
  instead of resulting in a not-found response.

---

# Pending Work

* Securing Private Services (e.g. Internal API Key, ACL's (v6))
    * ACL SETUSER public on \>demo123 -@all +lpop +blpop +lpush +llen ~openils:actor
* In-Bus Registry of Running Services (If Needed).
    * Circ, for example, queries the router to see if Booking is running.
      Could be addressed with configuration (e.g. global flag)
* Docs

---

# How Does That Make You Feel?

