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

# Additional Design Considerations

* Multi-Domain Routing
* Redis Message Streams
* Minimal upgrade requirements

---

# Multi-Domain Routing

Long Live the OpenSRF Router!

* High-Availability
* Additional Layer of Security (domain segmentation)
* Backwards Compatible with the OpenSRF Translator / Dojo UI's

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

Streams work, are a bit more complicated overall, but mainly:

> All the messages are appended in the stream indefinitely (unless the user 
> explicitly asks to delete entries): different consumers will know what 
> is a new message from its point of view by remembering the ID of the 
> last message received.

---

# Minimal Upgrade Requirements

* Avoid config file overhaul
* Drop-in Replacment as much as possible

---

# Rust, Really?

[KCLS Rust Evergreen Workspace](https://github.com/kcls/evergreen-universe-rs/)

# Also, with Websockets

* Remove websocketd dependency
* Implemented max-parallel throttling (user/berick/websocket-parallel-tester)

---

# Kicking the Tires

* [Ansible Installer](https://github.com/berick/evergreen-ansible-installer/tree/working/ubuntu-22.04-redis)
* [Demo Site](https://redis.demo.kclseg.org/eg2/staff/splash)
* [Public Service](https://redis.demo.kclseg.org/osrf-gateway-v1?service=open-ils.actor&method=opensrf.system.echo&param=%221%22&param=%222%22)
* [Private Service](https://redis.demo.kclseg.org/osrf-gateway-v1?service=open-ils.cstore&method=opensrf.system.echo&param=%221%22&param=%222%22)

---


# Development TODO Items
* Generate a random redis password at build time.
  * https://github.com/berick/OpenSRF/blob/user/berick/lpxxx-opensrf-over-redis-v2/examples/redis-accounts.example.txt
* Implement direct-to-drone request delivery
  * Avoid listener chokepoint.
  * Perl code exists for this.

---

# NOTES

* TODO move to git.evergreen-ils.org
* https://github.com/berick/OpenSRF/tree/user/berick/lpxxx-opensrf-over-redis-v2
* https://github.com/berick/Evergreen/tree/user/berick/lpxxx-opensrf-over-redis-v1
* buswatch / expire lingering


