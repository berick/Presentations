# Redis And Beyond

2024 Evergreen Conference

Worcester, Massachusetts

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/main/Evergreen-2024](
    https://github.com/berick/Presentations/tree/main/Evergreen-2024)

# Redis Project State of Affairs

big change; awaiting broader testing before merging to osrf main.

Am I using it yet?  Is anyone?

# Dev VM How-to

ansible

# Config files

redis-accounts.txt
passwords in opensrf_core.xml and ~/.srfsh.xml


# Migration How-to

disable persist, etc.

# Sysadmin and Debugging Tools

redis passwords
- stored
- generated
- command line auth

redis monitor

log stuff?

# Multi-domain / high-availability / mesh

* Listen on routable IP address
* Same username/password requirement for all domains
* how it works: 
    * listener connects to remote redis instances to register w/ remote routers
    * router opens port to remote domain when it needs to route an API request
      to a listener that's not on its domain.
    * worker opens connection to remote domain bus to send replies
      to clients whose API calls were cross-domain routed.

# Redis replace memcache / persistent auth tokens

Work up a patch. What all uses memcache now?

Persist config. (optionally persist by database?)

Other stuff we can cache?

# Rust Stuff

editor
-- show a simple usage example / compare to C?
egsh
opensrf service 
jsonquery

https://crates.io/crates/sip2



