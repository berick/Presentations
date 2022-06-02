# OpenSRF -> RediSRF ?

### 2022 Evergreen Online Conference

Bill Erickson

Software Development Engineer

King County Library System

https://github.com/berick/Presentations/tree/master/Evergreen-2022

---

# Why Replace Ejabberd?

#### It's kind of a bear, but also...

* Complications with changing hostnames
* Authentication changes
* Apparmor interactions.
* Default install fails in LXD guests
* XMPP spec has changed multiple times causing breakage
* There may be better options

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
* Trivial to get started
* It does a few things very well
* Ease of Installation and Configuration
* Native Debugging Tools & Stats Collection
* Open-source, C-based, liberally licensed.

---

# How Does This Work?

    RPUSH service:open-ils.actor <OSRF-REQUEST-JSON>

    BLPOP service:open-ils.actor

    RPUSH client:515aad3bfbc2 <OSRF-RESPONSE-JSON>

    BLPOP client:515aad3bfbc2

    RPUSH client:515aad3bfbc2 <OSRF-RESPONSE-2-JSON>

    BLPOP client:515aad3bfbc2

    # ...
    # Connected clients send follow-up requests to ther worker address

    RPUSH client:open-ils.actor:d12252828cb2 <OSRF-REQUEST2-JSON>

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

[README_REDIS.md](https://github.com/berick/OpenSRF/blob/user/berick/lpxxx-opensrf-via-redis-v4-auth-exp-2/README_REDIS.md)

---

# Timing

* Demo timer script
* Demo Catalog with
* Demo Server Admin -> Search Facet Fields

---

# Debugging Tools:

    % redis-cli monitor

    % redis-cli memory stats

    % redis-cli client help

    % redis-cli client list # e.g. tot-mem

    % redis-cli keys client:* 

      1) "client:opensrf.settings:f67a1bb2188e"

      2) "client:opensrf.settings:c9e470abf2c3"

---

# Opportunities

* Direct-to-drone request delivery.
* Could replace memcache / optional key persistence.
* OpenSRF request "backlog" not required.
* Do we need chunking/bundling?

---

# Limitations

### No cross-domain (i.e. cross-brick) routing.

* Affects some Dojo/translator UI's
    * Bricks that share a Redis instance could still cross-communicate

### No support for max list entry size

* Could be enforced in the OpenSRF client libs.

### Key expiration for lists does not inspect list values

* Lists appear and disappear as values are entered and the list is emptied

---

# Securing Private Services

If we no longer have public and private XMPP domains...

* ACL's to prevent access to private services
    * See osrf_control --reset-message-bus
    * 3 accounts: 'default', 'opensrf@public', and 'opensrf@private'
* Gateway additionally verifies requests for public services as an added 
  security layer and to prevent requests going to nonexistent end points.

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

