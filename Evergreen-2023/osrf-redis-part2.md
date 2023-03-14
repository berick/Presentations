# XMPP/Ejabberd to Redis Update

2023 Evergreen Online Conference

Bill Erickson

Software Development Engineer, King County Library System

[Slides as Markdown / HTML](https://github.com/berick/Presentations/tree/master/Evergreen-2023)

---

# XMPP Replacement?

[Evergreen 2022 Talk](https://github.com/berick/Presentations/blob/master/Evergreen-2022/osrf-redis.md)

---

# XMPP V. Redis

### Jabber

<div>
  <script src="https://asciinema.org/a/UA1p5dbF7KfB46ckDwtwlXaaN.js" 
    id="asciicast-UA1p5dbF7KfB46ckDwtwlXaaN" async 
    data-loop="true" data-rows="10" data-autoplay="true" data-size="big"></script>
</div>

### Redis

<div style="padding-left:2px;margin-left:2px">
  <script src="https://asciinema.org/a/sIbrmZ60vUm3v5m81ojhmxqdP.js" 
    id="asciicast-sIbrmZ60vUm3v5m81ojhmxqdP" async 
    data-loop="true" data-rows="10" data-autoplay="true" data-size="big"></script>
</div>

---

# Additional Design Proposals

* Multi-Domain Routing
* Redis Message Streams
* Minimal upgrade requirements

---

# Multi-Domain Routing

Recover the OpenSRF Router

* High-Availability
* Additional Layer of Security (domain segmentation)
* Works with the OpenSRF Translator / Dojo UI's

---

# Routing / Addresses

## Structure

    !sh
    [prefix]:[purpose]:[domain|service-name]:[hostname]:[pid]:[random]

## Examples

* opensrf:service:open-ils.cstore
* opensrf:router:private.localhost
* opensrf:client:private.localhost:eg22:848908:330137

---

# Routing Example

    !sh

    # Worker client sends to service via router
    Message-From: opensrf:client:private.localhost:eg22.lxd:1702978:9094
    Message-To:   opensrf:service:open-ils.cstore
    Delivered-To: opensrf:router:private.localhost

    # Router sends to service address
    Message-From: opensrf:client:private.localhost:eg22.lxd:1702978:9094 
    Message-To:   opensrf:service:open-ils.cstore

    # Worker replies directly to calling worker
    Message-From: opensrf:client:private.localhost:eg22.lxd:1702964:8234
    Message-To:   opensrf:client:private.localhost:eg22.lxd:1702978:9094

*Routes via domain instead of specific listener address*

---

# Router

[Router v1 / Rust](https://github.com/kcls/evergreen-universe-rs/blob/main/opensrf/src/bin/router.rs)

    !sh
    srfsh# request router opensrf.router.info.class.list

    Received Data: [
      "opensrf.settings",
      "open-ils.booking",
      "opensrf.math",
      "open-ils.supercat",
      "open-ils.cat",
      "open-ils.auth_proxy",
      ...

--- 

# Router / Summarize

    !json
    {
      "listen_address": "opensrf:router:private.localhost",
      "primary_domain": {
        "domain": "private.localhost",
        "route_count": 343,
        "services": [ {
          "name": "opensrf.settings",
          "controllers": [ {
            "address": "opensrf:client:private.localhost:eg22.lxd:1702978:9094",
            "register_time": "2023-03-13T17:14:13.903523503-04:00"
          } ] 
        }, {
          "name": "open-ils.booking",
          "controllers": [ {
            "address": "opensrf:client:private.localhost:eg22.lxd:1702999:5342",
            "register_time": "2023-03-13T17:14:14.372647614-04:00"
          } ] 
        },
        ...

---

# Message Streams

* Alternate form of message delivery in Redis vs. Queues.
* Support subscriptions

---

# Why Not Message Streams?

* Require stream / group creation and deletion
    * Queues are auto-created on first use.
    * Queues are auto-deleted when last message is popped
* Stream messages have to be explicitly deleted
    * Queue messages are popped and removed
* Swapping betweeen streams and queues is fairly trivial.

---

# Redis List / Queue

1. Push a value onto a list
1. Pop a value from the list (blocking/timeout optional)
    * This removes the message from Redis
    * List disappears once empty

---

# Redis Streams & Groups

1. Create a stream and a group
1. Add a message to the stream
1. Read a message from the stream (blocking/timeout optional)
    * OPTIONAL: ACK the message
1. Delete the read message from the stream
1. Delete the stream to cleanup

---

# Lists and Streams Swappable


---

# Minimal Upgrade Requirements

* Avoid config file overhaul
* Drop-in Replacment as much as possible

---

# Kicking the Tires

* https://github.com/berick/evergreen-ansible-installer/tree/working/ubuntu-22.04-redis
* Been runnning the redis branches on my dev machine since Jan 2023

https://redis.demo.kclseg.org/osrf-gateway-v1?service=open-ils.actor&method=opensrf.system.echo&param=%221%22&param=%222%22
https://redis.demo.kclseg.org/osrf-gateway-v1?service=open-ils.cstore&method=opensrf.system.echo&param=%221%22&param=%222%22

---

# Websockets

* Remove websocketd dependency
* Implemented max-parallel throttling (user/berick/websocket-parallel-tester)

---


# Development TODO Items
* Generate a random redis password at build time.
* Implement direct-to-drone request delivery
  * Avoid listener chokepoint.
  * Perl code exists for this.

---

# NOTES

* TODO move to git.evergreen-ils.org
* https://github.com/berick/OpenSRF/tree/user/berick/lpxxx-opensrf-over-redis-v2
* https://github.com/berick/Evergreen/tree/user/berick/lpxxx-opensrf-over-redis-v1
* Discuss accounts
  * https://github.com/berick/OpenSRF/blob/user/berick/lpxxx-opensrf-over-redis-v2/examples/redis-accounts.example.txt
  * generate passwords at compile time
* buswatch / expire lingering


