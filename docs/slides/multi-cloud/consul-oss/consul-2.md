name: Chapter-2
class: title
# Chapter 2
## HashiCorp Consul Architecture

---
name: Introduction-to-Consul
Introduction to Consul
-------------------------
.center[![:scale 45%](images/multi-datacenter-federation.png)]

???
Consul is a complex system that has many different moving parts. To help users and developers of Consul form a mental model of how it works, this page documents the system architecture.

In the next sections, we will dive deeper into how  Consul works.

---
name: Introduction-to-Consul-Overview
class: img-right
Introduction to Consul - Overview
-------------------------
.center[![:scale 100%](images/multi-datacenter-federation.png)]

* A consul cluster is refered to as a datacenter
* A consul datacenter is made up of server nodes and client nodes
* 3 or 5 server nodes per datacenter
* 100s - 10,000s of client nodes

???
Within each datacenter, we have a mixture of clients and servers. It is expected that there be between three to five servers. This strikes a balance between availability in the case of failure and performance, as consensus gets progressively slower as more machines are added. However, there is no limit to the number of clients, and they can easily scale into the thousands or tens of thousands.

---
name: Introduction-to-Consul-Gossip
class: img-right
Introduction to Consul - Gossip
-------------------------
.center[![:scale 100%](images/multi-datacenter-federation.png)]

* All agent communication is done via the Gossip Protocol
* Automatic configuration and datacenter discovery for Consul agents
* Agent failures is done at the collective agent level not at the server level
* Using Gossip this allows high scalability vs. traditional heartbeat schemes
* Node failure can be inferred by an agent failure

---
name: Introduction-to-Consul-Consensus
class: img-right
Introduction to Consul - Consensus
-------------------------
.center[![:scale 100%](images/multi-datacenter-federation.png)]

* Every Consul datacenter has a group of server nodes that work together to manage connected agents
* Using Raft the server nodes elect a leader
* A leader is responsible for processing all queries and has write authority to the KV store
* It is also responsible for transaction replication
* All requests to the server nodes are routed to the leader

???
The servers in each datacenter are all part of a single Raft peer set. This means that they work together to elect a single leader, a selected server which has extra duties. The leader is responsible for processing all queries and transactions. Transactions must also be replicated to all peers as part of the consensus protocol. Because of this requirement, when a non-leader server receives an RPC request, it forwards it to the cluster leader.

---
name: Introduction-to-Consul-Multi-DC
class: img-right
Introduction to Consul - Multi-DC
-------------------------
.center[![:scale 100%](images/multi-datacenter-federation.png)]

* Gossip over a WAN connection is also possible
* Allows for request from one datacenter to be forwarded to another
* This allows for service level DR
* This allows for geographical service request handling

???
The server agents also operate as part of a WAN gossip pool. This pool is different from the LAN pool as it is optimized for the higher latency of the internet and is expected to contain only other Consul server agents. The purpose of this pool is to allow datacenters to discover each other in a low-touch manner. When a server receives a request for a different datacenter, it forwards it to a random server in the correct datacenter. That server may then forward to the local leader, so cross-datacenter requests are relatively fast and reliable.

---
name: Introduction-to-Consul-Protocols
Introduction to Consul - Protocols
-------------------------
Now you have a high level understanding of Consul's two primary Protocols:

* Consensus
* Gossip

If you want to learn more these protocols, check out the appendix.

---
name: Introduction-to-Gossip-Skeptical
Introduction to Consul - Skeptical ?
-------------------------
.center[![:scale 60%](images/mitchell_tweet.png)]
