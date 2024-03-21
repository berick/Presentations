# Redis And Beyond

2024 Evergreen Conference

Worcester, Massachusetts

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/main/Evergreen-2024](
    https://github.com/berick/Presentations/tree/main/Evergreen-2024)

# Redis Project State of Affairs

KeyDB

https://docs.keydb.dev/

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

redis monitor
restarting redis is your friend.

log stuff?

# Multi-domain / high-availability / mesh

* Listen on routable IP address
* Same username/password requirement for all domains
* how it works:  drawing
    * listener connects to remote redis instances to register w/ remote routers
    * router opens port to remote domain when it needs to route an API request
      to a listener that's not on its domain.
    * worker opens connection to remote domain bus to send replies
      to clients whose API calls were cross-domain routed.

# Redis replace memcache / persistent auth tokens

Work up a patch. What all uses memcache now?

https://git.evergreen-ils.org/?p=working/OpenSRF.git;a=shortlog;h=refs/heads/user/berick/lpxxx-redis-for-cache

Persist config. (optionally persist by database?)

Other stuff we can cache?

# Rust Stuff

```
cargo watch -x "build --all"
```

https://developers.slashdot.org/story/24/02/28/1529238/white-house-urges-devs-to-switch-to-memory-safe-programming-languages

editor
-- show a simple usage example / compare to C?

egsh
-- requath
-- flat fields by default.
opensrf.router.info.summarize

opensrf service 

jsonquery

exclude feature:

```
{"select":{"bre":{"exclude":["marc","vis_attr_vector"]}},"from":"bre"}
```

redis cache


https://crates.io/crates/sip2

https://github.com/berick/sip2-mediator-rs

