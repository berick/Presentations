# Redis (Valkey?) And Beyond

2024 Evergreen Conference

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/main/Evergreen-2024](
    https://github.com/berick/Presentations/tree/main/Evergreen-2024)

---

# State of the SUNION*

[https://bugs.launchpad.net/opensrf/+bug/2017941](Bug 2017941)

* OpenSRF branch is pending merge (4.0?)
* Evergreen components merged to 3.12

[\*https://redis.io/docs/latest/commands/sunion/](https://redis.io/docs/latest/commands/sunion/)

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

* /openils/conf/redis-accounts.txt
    * Permissions
* /openils/conf/opensrf\_core.xml
* /home/opensrf/.srfsh.xml

---

# Changing Redis Passwords

* Stop Redis
* Modify redis-accounts.txt, opensrf\_core.xml
* Start Redis
* Restart Evergreen

---

# Migrating from XMPP to Redis

[https://evergreen-ils.org/documentation/release/RELEASE_NOTES_3_12.html#\_upgrading_to_evergreen_opensrf_redis](
    https://evergreen-ils.org/documentation/release/RELEASE_NOTES_3_12.html#_upgrading_to_evergreen_opensrf_redis)
   
Note new gateway account.

---

# Sysadmin and Debugging Tools

```sh
REDISCLI_AUTH=<PASSWORD> redis-cli monitor

REDISCLI_AUTH=<PASSWORD> redis-cli
127.0.0.1:6379> scan 0 match opensrf:*
```

---

# Redis Addresses

```
prefix : purpose : name : domain [: extra...]
```

* opensrf:router:$username:$domain
    * $username == router name
* opensrf:service:$username:$domain:$service
    * $username == service name
* opensrf:client:$username:$domain:$hostname:$pid:$random
    * $username == 'opensrf' (typically)
    * $hostname:$pid:$random are for randomness and debugging

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

https://docs.google.com/drawings/d/1TL1scUsQ5yKWk0THs2RvHEmvyiltsaizfDxFLIjf\_XU/edit

---

# Mesh Live

```
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

Work up a patch. What all uses memcache now?

https://git.evergreen-ils.org/?p=working/OpenSRF.git;a=shortlog;h=refs/heads/user/berick/lpxxx-redis-for-cache

Persist config. (optionally persist by database?)

Other stuff we can cache?

---

# Rust Stuff

---

# Why Rust?

Memory Safety
Performance
Thread Safety
Cross-Platform
Community
Build System
Doc Tests

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

-- Albus Dumbledore, 2009

---

# KCLS Rust Project

https://github.com/kcls/evergreen-universe-rs

---

# Evergreen Example

[https://github.com/kcls/evergreen-universe-rs/blob/main/evergreen/README.md](
    https://github.com/kcls/evergreen-universe-rs/blob/main/evergreen/README.md)

---

## Rust Router on Valkeys

```
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

### Websockets

Parallel request throttling

---

### gateway

https://valkey01.demo.kclseg.org/eg-http-gateway?service=open-ils.pcrud&method=open-ils.pcrud.search.cmrcfld&param=%22ANONYMOUS%22&param={%22id%22:{%22%3C%3E%22:0}}&format=hashfull

Unit Tests

DOCS: https://34.148.120.89/rust-docs/evergreen/index.html

```
cargo watch -x "build --all"
```

https://developers.slashdot.org/story/24/02/28/1529238/white-house-urges-devs-to-switch-to-memory-safe-programming-languages

editor
-- show a simple usage example / compare to C?

egsh
-- general purpose diagnostic / debugging tool
-- scriptable like srfsh
-- requath
-- flat fields by default.

echo -e "sip localhost:6001 login sip-user sip-pass\nsip localhost:6001 item-information CONC40000583" | egsh

cstore search au {"id":{"<":5}}

egsh# sip localhost:6001 login sip-user sip-pass
egsh# sip localhost:6001 item-information CONC91000491
egsh# db idl search aou name ~\* "branch"
egsh# db idl search aou name ilike "%branch%"

opensrf service 

introspect-summary open-ils.rs-circ

jsonquery

exclude feature:

```
{"select":{"bre":{"exclude":["marc","vis_attr_vector"]}},"from":"bre"}
```

redis cache

https://crates.io/crates/sip2

https://github.com/berick/sip2-mediator-rs

---

# Evergreen Services

* Service implement the `evergreen::osrf::app::Application` trait.
    * Compared to Perl/C which dynamically load modules.

```sh
cargo run --package evergreen --bin eg-service-rs-circ
# OR 
sudo systemctl restart eg-service-rs-circ
```

* Doc Summary
* introspect open-ils.rs-circ
* introspect-summary open-ils.rs-circ
* ParamCounts
    * req open-ils.rs-circ opensrf.system.method.all 1 2 3



