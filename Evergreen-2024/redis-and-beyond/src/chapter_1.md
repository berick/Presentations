# ~~Redis~~ Valkey And Beyond

2024 Evergreen Conference

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/main/Evergreen-2024](
    https://github.com/berick/Presentations/tree/main/Evergreen-2024)

# State of the SUNION

https://bugs.launchpad.net/opensrf/+bug/2017941

* OpenSRF branch is pending merge (4.0?)
* Evergreen components merged to 3.12

# Flight of the Valkey! (*Obviously*)

https://redis.io/blog/redis-adopts-dual-source-available-licensing/

https://github.com/valkey-io/valkey

# Dev VM How-To

## Ansible

[https://github.com/berick/evergreen-ansible-installer/tree/working/ubuntu-22.04-redis](
    https://github.com/berick/evergreen-ansible-installer/tree/working/ubuntu-22.04-redis)

## Docker

[https://github.com/mcoia/eg-docker](https://github.com/mcoia/eg-docker)

# Config files

redis-accounts.txt
passwords in opensrf_core.xml and ~/.srfsh.xml

How to change passwords and reset everything

# Migration How-to

disable persist, etc.

# Sysadmin and Debugging Tools

redis passwords
- stored
- generated
- command line auth

address structure

redis monitor
restarting redis is your friend.

log stuff?

# Multi-domain / high-availability / mesh

* hosts file (ditto ejabberd)
* opensrf_core.xml changes + passwords (new)
* copy redis-accounts.txt to all hosts. (new)
* TODO: had to reset message bus on each host cuz of limitations in osrf_control

* Listen on routable IP address
* Same username/password requirement for all domains
* how it works: drawing
    * listener connects to remote redis instances to register w/ remote routers
    * router opens port to remote domain when it needs to route an API request
      to a listener that's not on its domain.
    * worker opens connection to remote domain bus to send replies
      to clients whose API calls were cross-domain routed.

# Mesh

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

# Redis replace memcache / persistent auth tokens

Work up a patch. What all uses memcache now?

https://git.evergreen-ils.org/?p=working/OpenSRF.git;a=shortlog;h=refs/heads/user/berick/lpxxx-redis-for-cache

Persist config. (optionally persist by database?)

Other stuff we can cache?

# Rust Stuff

https://www.theregister.com/2024/03/31/rust_google_c/

Migrating C++ and Go to Rust.

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

egsh# sip localhost:6001 login sip-user sip-pass
egsh# sip localhost:6001 item-information CONC91000491

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

