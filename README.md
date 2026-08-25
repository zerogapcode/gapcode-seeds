# Gapcode bootstrap peers

One file, [`bootstrap-peers.txt`](bootstrap-peers.txt): the addresses a freshly
installed [Gapcode](https://github.com/zerogapcode/gapcode) node tries first.

## Why this exists

Gapcode has no DNS seeds. A node on a new machine knows of nobody — zero peers, zero
outbound attempts, an empty address manager. It is not broken; it has nowhere to go.

One address is enough to fix that. Bitcoin's address gossip handles everything after
the first connection, and from then on the node remembers the network in `peers.dat`
and never needs this file again.

Every Gapcode app also carries a copy of this list inside its bundle. That copy is the
floor, not the whole story: it ages, and an aged-out list strands every new install
until the app is rebuilt and redistributed to everyone. This file is the live one.

## How it is maintained

Rewritten every six hours by a scheduled job in the main repository. It joins the
network like any other node, asks the peers it reaches for the addresses they know,
and **verifies every candidate by connecting to it** before publishing.

Nothing goes in that did not answer. A plausible-looking address that responds to
nothing is worse than no address at all: a new node spends its retry budget on it,
reaches nobody, and the failure looks like a bug in the app rather than a stale list.

## Why only onion addresses

Almost every machine running Gapcode is a laptop behind NAT — reachable today, gone
tomorrow, and usually not reachable at all from outside. Each node publishes a Tor
onion service instead, which works from anywhere with no router configuration and
whose address is permanent.

## Is publishing these addresses safe?

They are public by construction. Every node gossips them to everyone it connects to;
that is how the network works.

## Format

One `host:port` per line. `#` starts a comment. Blank lines are ignored.

```
abcdefghijklmnopqrstuvwxyz234567abcdefghijklmnopqrstuvwx.onion:8641
```
