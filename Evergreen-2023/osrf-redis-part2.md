# XMPP/Ejabberd to Redis Project Update

2023 Evergreen Online Conference

Bill Erickson

Software Development Engineer, King County Library System

[Last Year's Talk](https://github.com/berick/Presentations/tree/master/Evergreen-2022)

[Slides as Markdown / HTML](https://github.com/berick/Presentations/tree/master/Evergreen-2023)

---

# NOTES

* TODO move to 
* https://github.com/berick/OpenSRF/tree/user/berick/lpxxx-opensrf-over-redis-v2
* https://github.com/berick/Evergreen/tree/user/berick/lpxxx-opensrf-over-redis-v1
* Discuss accounts
  * https://github.com/berick/OpenSRF/blob/user/berick/lpxxx-opensrf-over-redis-v2/examples/redis-accounts.example.txt
* buswatch / expire lingering

# Recap XMPP Replacement

https://github.com/berick/Presentations/blob/master/Evergreen-2022/osrf-redis.md

---

# Additional Design Proposals

* Recover the OpenSRF Router and its multi-domain routing.
  * Cross-Domain Service Registration / High-Availability
  * Additional Layer of Security
  * Works with the OpenSRF Translator / Dojo UI's
  * Have not coded a non-router option
* Message Streams vs. Message Queues

---

# Why Not Message Streams?

* Require stream / group creation and deletion
  * Queues are auto-created on first use.
  * Queues are auto-deleted when last message is popped
* Stream messages have to be explicitly deleted
  * Queue messages are popped and removed
* Swapping betweeen streams and queues is fairly trivial.

---

# Anatomy of a Queue Addresses

* opensrf:service:open-ils.cstore
* opensrf:router:private.localhost
* opensrf:client:private.localhost:eg22:848908:330137
* Services also have their own opensrf:client:\* address

---

# Message Path Example

open-ils.actor service starts up on private.localhost
sends top-level request to opensrf:router:private.localhost for opensrf:service:open-ils.cstore
if registered, router relays request to opensrf:service:open-ils.cstore on its private.localhost bus connection
if not register, router relays request to opensrf:service:open-ils.cstore a different domain bus connection.
open-ils.cstore Listener waits for messages at opensrf:service:open-ils.cstore
 -- if multiple Listeners are running, they both listen at opensrf:service:open-ils.cstore
    and redis round-robin's which gets the message.

---

# Kicking the Tires

* https://github.com/berick/evergreen-ansible-installer/tree/working/ubuntu-22.04-redis
* Been runnning the redis branches on my dev machine since Jan 2023

---

# Development TODO Items
* Generate a random redis password at build time.
* Implement direct-to-drone request delivery
  * Avoid listener chokepoint.
  * Perl code exists for this.


