# XMPP/Ejabberd to Redis Project Update

2023 Evergreen Online Conference

Bill Erickson

Software Development Engineer, King County Library System

[Last Year's Talk](https://github.com/berick/Presentations/tree/master/Evergreen-2022)

[Slides as Markdown / HTML](https://github.com/berick/Presentations/tree/master/Evergreen-2023)

---

# Recap XMPP Replacement

https://github.com/berick/Presentations/blob/master/Evergreen-2022/osrf-redis.md

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

# Additional Design Proposals After Previous Talk

* Recover the OpenSRF Router and its multi-domain routing.
  * Cross-Domain Service Registration / High-Availability
  * Additional Layer of Security
  * Works with the OpenSRF Translator / Dojo UI's
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

https://redis.demo.kclseg.org/osrf-gateway-v1?service=open-ils.actor&method=opensrf.system.echo&param=%221%22&param=%222%22
https://redis.demo.kclseg.org/osrf-gateway-v1?service=open-ils.cstore&method=opensrf.system.echo&param=%221%22&param=%222%22

---

# Websockets

* Remove websocketd dependency
* Implemented max-parallel throttling (user/berick/websocket-parallel-tester)

---


# Development TODO Items
* Generate a random redis password at build time.
* Implement direct-to-drone request delivery
  * Avoid listener chokepoint.
  * Perl code exists for this.

---

# NOTES

* TODO move to git.evergreen-ils.org
* https://github.com/berick/OpenSRF/tree/user/berick/lpxxx-opensrf-over-redis-v2
* https://github.com/berick/Evergreen/tree/user/berick/lpxxx-opensrf-over-redis-v1
* Discuss accounts
  * https://github.com/berick/OpenSRF/blob/user/berick/lpxxx-opensrf-over-redis-v2/examples/redis-accounts.example.txt
  * generate passwords at compile time
* buswatch / expire lingering


