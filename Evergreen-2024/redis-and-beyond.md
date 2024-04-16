<!-- class: invert -->

# Redis (Valkey?) And Beyond

2024 Evergreen Conference

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/main/Evergreen-2024](
    https://github.com/berick/Presentations/tree/main/Evergreen-2024)

---

# State of the SUNION

[Bug 2017941](https://bugs.launchpad.net/opensrf/+bug/2017941)

* OpenSRF branch is pending merge (4.0?)
* Evergreen components merged to 3.12

---

# Flight of the Valkey! (*i mean...*)

[https://redis.io/blog/redis-adopts-dual-source-available-licensing/](
    https://redis.io/blog/redis-adopts-dual-source-available-licensing/)

[https://github.com/valkey-io/valkey](https://github.com/valkey-io/valkey)

---

# Dev VM How-To

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
* Modify redis-accounts.txt, opensrf\_core.xml, .srfsh.xml
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
REDISCLI_AUTH=<PASSWORD> redis-cli monitor

REDISCLI_AUTH=<PASSWORD> redis-cli

127.0.0.1:6379> scan 0 match opensrf:*
```

---

# Redis Addresses

    prefix : purpose : name : domain [: extra...]

* opensrf:router:\$username:$domain
    * \$username == router name
* opensrf:service:\$username:\$domain:\$service
    * \$username == 'opensrf' (typically)
    * \$service == service name
* opensrf:client:\$username:\$domain:\$hostname:\$pid:\$random
    * \$username == 'opensrf' (typically)
    * \$hostname:\$pid:\$random are for randomness and debugging

---

# High-Availability / Multi-Domain / Mesh

* Configure Redis to llisten on a routable IP address
* hosts file or DNS for cluster hosts
* opensrf\_core.xml changes + passwords (new)
* copy redis-accounts.txt to all hosts. (new)
* Same username/password requirement for all domains
* how it works: drawing
    * listener connects to remote redis instances to register w/ remote routers
    * router opens port to remote domain when it needs to route an API request
      to a listener that's not on its domain.
    * worker opens connection to remote domain bus to send replies
      to clients whose API calls were cross-domain routed.

TODO: image
https://docs.google.com/drawings/d/1TL1scUsQ5yKWk0THs2RvHEmvyiltsaizfDxFLIjf\_XU/edit

---

# Mesh Live

```sh
# Host 1 --
osrf_control -l --start --service router

# Host 2 --
osrf_control -l --start --service router

# Host 1 --
osrf_control -l --start-services

# Host 2 --
osrf_control -l --start-services

# Host 1 --
srfsh
srfsh# request router opensrf.router.info.class.list
srfsh# login admin demo123
# See router logs on Host 1

osrf_control -l --stop-services
srfsh
srfsh# request router opensrf.router.info.class.list
srfsh# login admin demo123
# See router logs on Host 2
```

---

# Redis replace memcache / persistent auth tokens

## Work in Progress

[user/berick/lpxxx-redis-for-cache](
    https://git.evergreen-ils.org/?p=working/OpenSRF.git;a=shortlog;h=refs/heads/user/berick/lpxxx-redis-for-cache)

## What else?

    * Auth sessions
    * SIP2Mediator sessions.

---

# Rust Stuff

---

# Why Rust?

* Memory Safety
* Thread Safety
* Performance
* Cross-Platform
* Build System
* Doc Tests
* Community

---

# On Google migrating C++ and Go to Rust

> A bit more than half of his developers say that Rust is easier to 
> review, according to Bergstrom.
  
> "When we sort of look into why that is," he said, "we get to sort of the
> most incredible question of the survey, the one that kind of blew all of
> us away, which is the confidence that people have in the correctness of
> the Rust code that they're looking at – so in comparison to code in
> other languages, how confident do you feel that your team's Rust code is
> correct?"

[https://www.theregister.com/2024/03/31/rust_google_c/](
    https://www.theregister.com/2024/03/31/rust_google_c/)

---

# On Google migrating C++ and Go to Rust

> The answer, Bergstrom said, was 85 percent.
  
> "That is a massive number," he said. "I could not get 85 percent of this
> room to agree that we like M&M's. Eight-five percent of people believe
> that their Rust code is more likely to be correct than the other code
> within their system. … I've been through more than one language survey
> in my life and I've never seen those kinds of numbers before."

[https://www.theregister.com/2024/03/31/rust_google_c/](
    https://www.theregister.com/2024/03/31/rust_google_c/)

---

# Why Not Rust?

> In order to gain passage, payment must be made, payment intended to weaken any intruder.
>
> -- Albus Dumbledore, 2009

---

# KCLS Rust Project

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

# egsh / Eggshell!

## General Purpose Diagnostic and Debugging Tool

### srfsh with a few additions.

---

# egsh / Reqauth and Formatting

    egsh# login admin demo123

    egsh# reqauth open-ils.pcrud open-ils.pcrud.retrieve.au 1

    egsh# pref set json_as_wire_protocal true

    egsh# reqauth open-ils.pcrud open-ils.pcrud.retrieve.au 1

---

# egsh / cstore

    egsh# cstore retrieve aou 1

    egsh# cstore search au {"id":{"<":5}}

    egsh# cstore json_query {"select":{"bre":["id"]},"from":"bre"}

---

# egsh / SIP

[https://crates.io/crates/sip2](https://crates.io/crates/sip2)

    egsh# sip localhost:6001 login sip-user sip-pass

    egsh# sip localhost:6001 item-information CONC91000491

    egsh# sip localhost:6001 patron-status 99999335545

    egsh# sip localhost:6001 patron-information 99999335545

---

# egsh / `--with-database`

    egsh# db idl search aou shortname = "BR1"

    egsh# db idl search aou name ~\* "branch"

    egsh# db idl search aou name ilike "%branch%"

---

# JSON Query & rs-store

    egsh# req open-ils.rs-store open-ils.rs-store.json_query {"select":{"bre":{"exclude":["marc","vis_attr_vector"]}},"from":"bre"}

---

# OpenSRF/Evergreen Services

Services implement the `evergreen::osrf::app::Application` Rust trait.

Compared to Perl/C which dynamically load modules.

    cargo run --package evergreen --bin eg-service-rs-circ

# OR 

    sudo systemctl restart eg-service-rs-circ

---

# OpenSRF/Evergreen Services

TODO: Threaded; Direct to drone delivery

    egsh# introspect-summary open-ils.rs-circ

    egsh# req open-ils.rs-circ opensrf.system.method.all 1 2 3

---

