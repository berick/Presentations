# OpenSRF-Over-Redis

### Exploring Replacing Ejabberd/XMPP For OpenSRF

2022 Evergreen Online Conference

Bill Erickson

Software Development Engineer

King County Library System

https://github.com/berick/Presentations/tree/master/Evergreen-2022

---

# Impetus

* Learning Rust
* Minimal XMPP Support

---

# Redis

[redis.io](https://redis.io/)

> The open source, in-memory data store used by millions of developers as a 
> database, cache, streaming engine, and message broker.

---

# Install

    % sudo apt install redis-server libredis-perl libhiredis-dev

---

# Working Branches

TODO

---

# Fundamental Differences

* No More OpenSRF Routing / Routers
  * Clients deliver requests directly to the service address.
* Bus messages are pure JSON
* Clients pull messages from the bus instead of having messages pushed to the client.
* Redis authentication is optional

---

# Debugging Tools

    redis-cli monitor

TODO screen cap of above.

---
