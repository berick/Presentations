<!-- class: invert -->

<!-- Building: marp -w redis-and-beyond.md -->

# Redis And Beyond

2024 Evergreen Conference

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/main/Evergreen-2024](
    https://github.com/berick/Presentations/tree/main/Evergreen-2024)

---

# State of the SUNION

[Bug 2017941](https://bugs.launchpad.net/opensrf/+bug/2017941)

* Evergreen components merged to 3.12
* OpenSRF branch is pending merge (4.0?)

---

# Flight of the Valkey! (*i mean...*)

[https://redis.io/blog/redis-adopts-dual-source-available-licensing/](
    https://redis.io/blog/redis-adopts-dual-source-available-licensing/)

[https://github.com/valkey-io/valkey](https://github.com/valkey-io/valkey)

---

# Dev VMs

## Ansible

[https://github.com/berick/evergreen-ansible-installer/tree/working/ubuntu-22.04-redis](
    https://github.com/berick/evergreen-ansible-installer/tree/working/ubuntu-22.04-redis)

## Docker

[https://github.com/mcoia/eg-docker](https://github.com/mcoia/eg-docker)

---

# Redis-Related Config Files

<!-- Discuss permissions for /openils/conf/redis-accounts.txt -->

* /openils/conf/redis-accounts.txt
    * [Redis ACLs](https://redis.io/docs/latest/operate/oss_and_stack/management/security/acl/)
* /openils/conf/opensrf\_core.xml
* /home/opensrf/.srfsh.xml

---

# Changing Redis Passwords

* Stop Redis
* Modify redis-accounts.txt, opensrf\_core.xml, .srfsh.xml as needed
* Start Redis
* Restart Evergreen

---

# Migrating from XMPP to Redis

<!-- New 'gateway' account -->

[https://evergreen-ils.org/documentation/release/RELEASE_NOTES_3_12.html#\_upgrading_to_evergreen_opensrf_redis](
    https://evergreen-ils.org/documentation/release/RELEASE_NOTES_3_12.html#_upgrading_to_evergreen_opensrf_redis)
   
---

# Sysadmin and Debugging Tools

```sh
REDISCLI_AUTH=<PASSWORD> redis-cli

127.0.0.1:6379> scan 0 match opensrf:*

REDISCLI_AUTH=<PASSWORD> redis-cli monitor
```

---

# Redis Addresses

## General Structure

```
prefix : purpose : username : domain [: extra...]
```

## Routers

```
prefix : purpose : router-name : domain

'opensrf:router:router:public.localhost'
```

--- 

# Redis Addresses...
<!-- 
username for router/service allow like entities on the same domain
serving different populateions.

For clients it's mostly for consistency of address formatting
-->

## Services

```
prefix : purpose : username : domain : service

'opensrf:service:opensrf:private.localhost:open-ils.cstore'
```

## Clients

```
prefix : purpose : username : domain : hostname : pid : random

'opensrf:client:opensrf:private.localhost:eg22:78870:368956'
```

--- 

# Router Service Address
<!-- 
Redis doesn't have a message wrapper like XMPP, so the "router_class"
has to be encoded in the Transport Message.
-->

```
'opensrf:service:_:_:open-ils.cstore'
```

---

# High-Availability / Multi-Domain / Mesh

* Configure Redis to listen on a routable IP address
* hosts file or DNS for cluster hosts
* opensrf\_core.xml changes + passwords (new)
* copy redis-accounts.txt to all hosts. (new)
* Same username/password requirement for all domains

---

# Mesh Under the Covers

* listener connects to remote redis instances to register w/ remote routers
* router opens port to remote domain when it needs to route an API request
  to a listener that's not on its domain.
* worker opens connection to remote domain bus to send replies
  to clients whose API calls were cross-domain routed.

https://docs.google.com/drawings/d/1TL1scUsQ5yKWk0THs2RvHEmvyiltsaizfDxFLIjf\_XU/edit

---

# Mesh Live

```sh
# valkey01 / valkey02
osrf_control -l --start --service router

# valkey01 / valkey02
osrf_control -l --start-services

# valkey01
srfsh# request router opensrf.router.info.class.list
srfsh# login admin demo123

# See router logs on Host 1

# valkey01
osrf_control -l --stop-services

srfsh# request router opensrf.router.info.class.list
srfsh# login admin demo123

# See router logs on Host 2

```

---

# What's Next for Redis?

---

# Replacing Memcache

## Work in Progress

[user/berick/lpxxx-redis-for-cache](
    https://git.evergreen-ils.org/?p=working/OpenSRF.git;a=shortlog;h=refs/heads/user/berick/lpxxx-redis-for-cache)

## What else?

* Auth sessions
* SIP2Mediator sessions.

---

# Rust

![bg w:360](https://foundation.rust-lang.org/img/cargo.png)

---

# Why?

<!-- compiles to machine code -->

* Memory Safety (Security)
* Thread Safety
* Performance
* Cross-Platform
* Build System
* Doc Tests
* Community
* Web Assembly?

---

# On Google migrating C++ and Go to Rust

> ... in comparison to code in other languages, how confident do you
> feel that your team's Rust code is correct?"
>
> The answer, Bergstrom said, was 85 percent.
>  
> "That is a massive number," he said. "I could not get 85 percent of
> this room to agree that we like M&M's... I've been through more than
> one language survey in my life and I've never seen those kinds of
> numbers before."

[https://www.theregister.com/2024/03/31/rust_google_c/](
    https://www.theregister.com/2024/03/31/rust_google_c/)

---

# Why Not?

>
> In order to gain passage, payment must be made, payment intended to weaken any intruder.
>
> -- Albus Dumbledore, 2009
>

---

# Why Not?

* Learning Curve
* Youth of the Project

---

# KCLS Rust Project

<!-- 
    Makefile 
    systemd support
    /usr/local/bin/, etc.
-->

[https://github.com/kcls/evergreen-universe-rs](https://github.com/kcls/evergreen-universe-rs)

---

# Documentation

[https://valkey01.demo.kclseg.org/rust-docs/evergreen/index.html](
    https://valkey01.demo.kclseg.org/rust-docs/evergreen/index.html)

---

# Unit Tests

[https://valkey01.demo.kclseg.org/rust-docs/evergreen/util/fn.stringify_params.html](
    https://valkey01.demo.kclseg.org/rust-docs/evergreen/util/fn.stringify_params.html)

---

# Evergreen Example

[https://github.com/kcls/evergreen-universe-rs/blob/main/evergreen/README.md](
    https://github.com/kcls/evergreen-universe-rs/blob/main/evergreen/README.md)

---

# Websockets

## Parallel request throttling

[Parallel Requests Issue](https://bugs.launchpad.net/evergreen/+bugs?field.tag=parallel-requests)

```
2024-04-15 10:58:53 eg22 eg-websockets [WARN:126158:eg_websockets:464:1713193133884-00006] 
    Session (127.0.0.1:60972) MAX_ACTIVE_REQUESTS reached. 1 messages queued
```

---

# gateway

[Gateway Hash Format](https://valkey01.demo.kclseg.org/eg-http-gateway?service=open-ils.pcrud&method=open-ils.pcrud.search.cmrcfld&param=%22ANONYMOUS%22&param={%22id%22:{%22%3C%3E%22:0}}&format=hashfull)

---

## Rust Router on Valkeys

```sh
# valkey-01 and valkey-02
osrf_control -l --fast-shutdown-all

# valkey-01
sudo systemctl start eg-router

# valkey-02
osrf_control -l --start --service router

# valkey-01 and valkey-02
osrf_control -l --start-services

# valkey-01
egsh# req router opensrf.router.info.summarize
```

---

# Eggshell!

<!-- 
General Purpose Diagnostic and Debugging Tool

srfsh with a few additions 
-->

```
egsh# help
```

---

# egsh / Reqauth and Formatting

```
egsh# login admin demo123

egsh# reqauth open-ils.pcrud open-ils.pcrud.retrieve.au 1

egsh# pref set json_as_wire_protocal true

egsh# reqauth open-ils.pcrud open-ils.pcrud.retrieve.au 1
```

---

# egsh / cstore

```
egsh# cstore retrieve aou 1

egsh# cstore search au {"id":{"<":5}}

egsh# cstore json_query {"select":{"bre":["id"]},"from":"bre"}
```

---

# egsh / SIP

[https://crates.io/crates/sip2](https://crates.io/crates/sip2)

```
egsh# sip localhost:6001 login sip-user sip-pass

egsh# sip localhost:6001 item-information CONC91000491

egsh# sip localhost:6001 patron-status 99999335545

egsh# sip localhost:6001 patron-information 99999335545
```

---

# egsh / `--with-database`

```
egsh# db idl search aou shortname = "BR1"

egsh# db idl search aou name ~\* "branch"

egsh# db idl search aou name ilike "%branch%"
```

---

# JSON Query & rs-store

```
egsh# req open-ils.rs-store open-ils.rs-store.json_query {"select":{"bre":{"exclude":["marc","vis_attr_vector"]}},"from":"bre"}
```

---

# OpenSRF/Evergreen Services

<!--
Perl and C services dynamically load modules
-->

Services implement the `evergreen::osrf::app::Application` Rust trait.

```sh
cargo run --package evergreen --bin eg-service-rs-circ

# or

sudo systemctl restart eg-service-rs-circ
```

---

# OpenSRF/Evergreen Services

TODO: Threaded; Direct to drone delivery

```
egsh# introspect-summary open-ils.rs-circ

egsh# req open-ils.rs-circ opensrf.system.method.all 1 2 3
```

---

