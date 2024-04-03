# Redis And Beyond

2024 Evergreen Conference

Worcester, Massachusetts

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/main/Evergreen-2024](
    https://github.com/berick/Presentations/tree/main/Evergreen-2024)

# Redis Project State of Affairs

https://github.com/valkey-io/valkey

Valkey

big change; awaiting broader testing before merging to osrf main.

Am I using it yet?  Is anyone?

# Dev VM How-to

ansible

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

* hosts file
* opensrf_core.xml changes + passwords
* copy redis-accounts.txt to all hosts.
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


Unit Tests

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
opensrf.router.info.summarize (pvt vs. public)
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

