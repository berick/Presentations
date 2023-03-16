# XMPP/Ejabberd to Redis Update

2023 Evergreen Conference

Worcester, Massachusetts

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/master/Evergreen-2023](https://github.com/berick/Presentations/tree/master/Evergreen-2023)

---

# XMPP Replacement?

<a href="https://upload.wikimedia.org/wikipedia/commons/d/d6/NeXTcube_motherboard.jpg">
    <img style="height:450px"
        src="https://upload.wikimedia.org/wikipedia/commons/d/d6/NeXTcube_motherboard.jpg"/>
</a>

*https://en.wikipedia.org/wiki/File:NeXTcube_motherboard.jpg*

---

# Evergreen 2022

[https://github.com/berick/Presentations/blob/master/Evergreen-2022/osrf-redis.md](https://github.com/berick/Presentations/blob/master/Evergreen-2022/osrf-redis.md)

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

* Recover Cross-Domain Routing
* Redis Message Streams
* Minimal upgrade requirements

---

# Multi-Domain Routing

Long Live the OpenSRF Router!

* High-Availability
* Additional Layer of Security (domain segmentation)
* Backwards Compatible with the OpenSRF Translator / Dojo UI's
* [Gateway Public Service](https://redis.demo.kclseg.org/osrf-gateway-v1?service=open-ils.actor&method=opensrf.system.echo&param=%221%22&param=%222%22)
* [Gateway Private Service](https://redis.demo.kclseg.org/osrf-gateway-v1?service=open-ils.cstore&method=opensrf.system.echo&param=%221%22&param=%222%22)

---

# High Availability

![Router](./media/router-multi.png)

---

# Routing / Addresses

## Structure

    !sh
    [prefix]:[purpose]:[domain|service-name]:[hostname]:[service-name?]:[pid]:[random]

## Examples

* opensrf:service:open-ils.cstore
* opensrf:router:private.localhost
* opensrf:client:private.localhost:eg22:848908:330137
* opensrf:client:private.localhost:eg22:open-ils.cstore:848908:330137

---

# Router

[Router v1](https://github.com/kcls/evergreen-universe-rs/blob/main/opensrf/src/bin/router.rs)
[ / Rust (!)](https://www.rust-lang.org/)

    !sh
    srfsh# request router opensrf.router.info.class.list

    Received Data: [
      "opensrf.settings",
      "open-ils.cat",
      "opensrf.math",
      "open-ils.search",
      "open-ils.booking",
      "open-ils.supercat",
      "open-ils.acq",
      "opensrf.dbmath",
      "open-ils.actor",
      "open-ils.justintime",
      "open-ils.reporter",
      "open-ils.collections",
      "open-ils.auth_proxy",
      "open-ils.auth",
      "open-ils.trigger",
      "open-ils.url_verify",
      "open-ils.vandelay",
      "open-ils.permacrud",
      ...
--- 

# Router / Summarize

    !json
    {
      "listen_address": "opensrf:router:private.localhost",
      "primary_domain": {
        "domain": "private.localhost",
        "route_count": 1484,
        "services": [ {
          "name": "opensrf.settings",
          "route_count": 10,
          "controllers": [ {
              "address": "opensrf:client:private.localhost:eg22.lxd::opensrf.settings:1771487:9698073",
              "register_time": "2023-03-15T12:35:15.852677162-04:00"
          } ] 
        }, {
          "name": "open-ils.cat",
          "route_count": 1,
          "controllers": [ {
              "address": "opensrf:client:private.localhost:eg22.lxd::open-ils.cat:1771516:8781771",
              "register_time": "2023-03-15T12:35:16.402692131-04:00"
            } ]
        },
        ...
---

# Message Streams

[https://redis.io/docs/data-types/streams-tutorial/](https://redis.io/docs/data-types/streams-tutorial/)

---

# Why Not Message Streams?

Streams work, are a bit more complicated overall, but mainly:

> All the messages are appended in the stream indefinitely (unless the user 
> explicitly asks to delete entries): different consumers will know what 
> is a new message from its point of view by remembering the ID of the 
> last message received.

*MAXLEN option applies to pending messages only!*

---

# Minimal Upgrade Requirements

* New config file: [/openils/conf/redis-accounts.txt[.example]](https://github.com/berick/OpenSRF/blob/user/berick/lpxxx-opensrf-over-redis-v2/examples/redis-accounts.example.txt)
    * `osrf_control --reset-message-bus`
    * TODO: Generate random passwords at build time

---

# Rust

A we really doing this?

[KCLS Rust Evergreen Workspace](https://github.com/kcls/evergreen-universe-rs/)

---

# Also, with Websockets

* Remove websocketd dependency
* Implemented max-parallel throttling 
    * Evergreen -> user/berick/websocket-parallel-tester

---

# Kicking the Tires

* [OpenSRF Github Branch](https://github.com/berick/OpenSRF/tree/user/berick/lpxxx-opensrf-over-redis-v2)
* [Evergreen Github Branch](https://github.com/berick/Evergreen/tree/user/berick/lpxxx-opensrf-over-redis-v1)
* [Ansible Installer](https://github.com/berick/evergreen-ansible-installer/tree/working/ubuntu-22.04-redis)
* [Demo Site](https://redis.demo.kclseg.org/eg2/staff/splash)

---

# Future Development

* Direct-to-drone request delivery
    * Avoid listener chokepoints
    * Perl code exists for this

---

# OK, what now?

* Decide on a path for Router and Websockets
* Generate bus passwords at install time
* Migrate code to community repositories
* Finalize install documentation


