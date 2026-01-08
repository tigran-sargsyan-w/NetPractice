# NetPractice – IP Addressing & Routing Fundamentals 🌐🧠

✅ **Status**: Completed *(10/10 levels exported)*
🏫 **School**: 42 – NetPractice
🏅 **Score**: <score>/100

> *A hands-on networking sandbox: subnetting, gateways, and routing — with instant feedback.*

---

## 📚 Table of Contents

* [📝 Description](#-description)
* [🎯 What You Learn](#-what-you-learn)
* [🚀 Quick Start](#-quick-start)
* [🧭 How NetPractice Thinks (Mental Model)](#-how-netpractice-thinks-mental-model)

  * [Layer 2 vs Layer 3](#layer-2-vs-layer-3-super-important)
  * [Interfaces = Subnet borders](#interfaces--subnet-borders)
  * [“Same subnet?” rule](#same-subnet-rule)
* [🧩 Subnetting Cheat Sheet](#-subnetting-cheat-sheet)

  * [Subnetting from Bits (Calculator‑friendly)](#-subnetting-from-bits-calculatorfriendly)
  * [CIDR → Mask (the bit view)](#1-cidr--mask-the-bit-view)
  * [Mask → CIDR (fast)](#2-mask--cidr-fast)
  * [“Step / Block size”](#3-step--block-size-what-we-called-шаг)
  * [Why network boundary IPs appear in routes](#4-why-network-boundary-ips-appear-in-routes-like-128)
  * [IP AND MASK = NETWORK](#5-the-real-math-ip-and-mask--network)
  * [Network / Broadcast: quick boundary rules](#6-network--broadcast-quick-boundary-rules)
  * [Fast host validity checklist](#7-fast-host-validity-checklist)
  * [CIDR → Mask (Fast Table)](#cidr--mask-fast-table)
  * [Block Size Method (Fastest Way)](#block-size-method-fastest-way)
  * [Network / Broadcast / Host Range](#network--broadcast--host-range)
  * [Common CIDR Patterns in NetPractice](#common-cidr-patterns-in-netpractice)
* [📏 How Many Addresses a Mask Gives You](#-how-many-addresses-a-mask-gives-you)

  * [CIDR `/n` → total addresses and usable hosts](#1-cidr-n--total-addresses-and-usable-hosts)
  * [Dotted mask → how many addresses?](#2-dotted-mask-eg-2552552550--how-many-addresses)
  * [`/30` explained](#3-30-explained-why-only-2-usable-ips)
* [🧷 Reserved IPv4 Ranges](#-reserved-ipv4-ranges-private-local-special)
* [🛣️ Routing Cheat Sheet](#️-routing-cheat-sheet)

  * [Default Gateway](#default-gateway)
  * [Static Routes](#static-routes)
  * [Longest Prefix Match](#longest-prefix-match-route-choice)
  * [Route Summarization](#route-summarization-supernetting)
* [🧰 A Practical Workflow to Solve Any Level](#-a-practical-workflow-to-solve-any-level)
* [🧯 Debugging & Common Mistakes](#-debugging--common-mistakes)

  * [Interpreting the simulator logs](#-interpreting-the-simulator-logs-what-they-usually-mean)
  * [Why “loop detected” happens](#-deep-dive-why-loop-detected-happens-and-how-to-fix-it-fast)
* [📦 Submission Files](#-submission-files)
* [📂 Repository Layout](#-repository-layout)
* [🔗 Resources](#-resources)
* [🧾 42 Notes (Defense Tips)](#-42-notes-defense-tips)

---

## 📝 Description

**NetPractice** is an interactive networking project where you fix 10 broken network topologies by configuring:

* IPv4 addresses
* Subnet masks (CIDR)
* Default gateways
* Router routes (static routing)

Each level provides **goals** and a live **packet simulation log**, so you can validate your changes instantly.

---

## 🎯 What You Learn

By the end, you’ll be comfortable with:

* ✅ Understanding **subnets** and **CIDR** (`/24`, `/26`, `/30`, …)
* ✅ Calculating **network address**, **broadcast**, and **host ranges**
* ✅ Recognizing what belongs to **Layer 2** vs **Layer 3**
* ✅ Setting correct **default gateways**
* ✅ Adding **static routes** to reach non-direct networks
* ✅ Reading logs and reasoning about **where packets break**

---

## 🚀 Quick Start

1. Extract the NetPractice archive.
2. Open `index.html` in a browser *(Chrome/Chromium recommended).*
3. Solve levels 1 → 10.
4. For each level, click **Get my config** and export your file.
5. Put **all 10 exported files** into the **root** of this repository.

---

## 🧭 How NetPractice Thinks (Mental Model)

### Layer 2 vs Layer 3 (super important)

* **Switch (L2)**: forwards frames inside a LAN. No routing logic.
* **Router (L3)**: forwards packets between networks. Chooses next hop by routing table.

### Interfaces = Subnet borders

A router interface belongs to exactly **one** subnet.
So whenever you see a link from `R1` to a host/switch segment:

* the router interface IP **must be inside that segment’s subnet**
* hosts in that segment must also be inside that subnet

### “Same subnet?” rule

Two devices can communicate directly **only if**:

* their IPs are in the **same subnet** (based on the mask)
* and the link is physically connected

If not, you need a **router** (gateway) in between.

---

## 🧩 Subnetting Cheat Sheet

### 🧠 Subnetting from Bits (Calculator‑friendly)

When you only have a basic calculator, think in **bits → bytes → decimals**.

#### 1) CIDR → Mask (the bit view)

A CIDR `/n` means: **n network bits**, then the rest are host bits.

* Split `n` into full octets + remainder:

  * `full = n / 8`
  * `rem  = n % 8`
* The first `full` octets are `255`.
* The next octet is built from the first `rem` bits set to 1.

Remainder → octet value table:

| rem bits | binary   | value |
| -------: | -------- | ----: |
|        0 | 00000000 |     0 |
|        1 | 10000000 |   128 |
|        2 | 11000000 |   192 |
|        3 | 11100000 |   224 |
|        4 | 11110000 |   240 |
|        5 | 11111000 |   248 |
|        6 | 11111100 |   252 |
|        7 | 11111110 |   254 |
|        8 | 11111111 |   255 |

Example: `/26`

* `26 = 3×8 + 2` → `255.255.255.(192)` → **255.255.255.192**

#### 2) Mask → CIDR (fast)

* Count `255` octets: each `255` = **8 bits**
* Then add bits from the table above for the first non‑255 octet

Example: `255.255.255.224`

* three `255` → 24 bits
* `224` corresponds to `rem=3` → total **/27**

#### 3) “Step / Block size” (what we called «шаг»)

If subnetting happens inside an octet, compute:

* `block = 256 − mask_octet`

Then subnet starts in that octet are:

* `0, block, 2×block, 3×block, ...`

This is the fastest method you and I used across levels.

Example: `/26` → last octet mask is `192` → `block = 64` → starts: `0, 64, 128, 192`.

#### 4) Why “network boundary” IPs appear in routes (like `.128`)

A route destination is a **network**, not a host.
So writing `129.128.239.128/25` is valid even if no device has `.128`.
It simply means “the subnet that starts at `.128`”.

#### 5) The real math: `IP AND MASK = NETWORK`

This is the bit-level truth behind everything.

* Convert IP and mask to binary (mentally you usually only need the **changing octet**).
* Apply **AND**:

  * `1 AND 1 = 1`
  * `1 AND 0 = 0`
  * `0 AND 1 = 0`
  * `0 AND 0 = 0`

Example: `192.168.10.130/26`

* changing octet: `130`
* `/26` → mask last octet = `192`

Binary:

* `130` = `10000010`
* `192` = `11000000`

AND:

* `10000010`
* `11000000`
  = `10000000` = `128`

So the **network** is `192.168.10.128`.

> Why we still use “block size”: it’s the same idea, just faster than doing AND every time.

#### 6) Network / Broadcast: quick boundary rules

Once you know the block size and subnet start:

* **Network** = subnet start
* **Broadcast** = next subnet start − 1
* **Valid hosts** = between them (excluding both ends)

Example: `...128/26`

* next subnet start is `...192`
* broadcast is `...191`
* hosts: `...129 → ...190`

#### 7) Fast host validity checklist

Before you even look at routing, verify:

* ✅ IP is inside the subnet range
* ✅ IP is **not** the network address
* ✅ IP is **not** the broadcast address
* ✅ Gateway (if needed) is in the **same subnet**

---

### CIDR → Mask (Fast Table)

| CIDR | Mask            | Hosts (usable) | Typical use         |
| ---- | --------------- | -------------- | ------------------- |
| /24  | 255.255.255.0   | 254            | classic LAN         |
| /25  | 255.255.255.128 | 126            | split /24 in 2      |
| /26  | 255.255.255.192 | 62             | split /24 in 4      |
| /27  | 255.255.255.224 | 30             | small LAN           |
| /28  | 255.255.255.240 | 14             | very small LAN      |
| /29  | 255.255.255.248 | 6              | tiny LAN            |
| /30  | 255.255.255.252 | 2              | point-to-point link |

> Quick host count formula: **usable hosts = 2^(32−CIDR) − 2**

---

### Block Size Method (Fastest Way)

When the mask breaks inside an octet, compute **block size**:

* `block = 256 − mask_octet`

Examples:

* `/26` → mask `255.255.255.192` → block `256 − 192 = 64`
* `/27` → mask `255.255.255.224` → block `256 − 224 = 32`
* `/30` → mask `255.255.255.252` → block `256 − 252 = 4`

This block is the step between subnet starts in the changing octet.

---

### Network / Broadcast / Host Range

If your changing octet is, say, the last one:

* subnet starts at: `0, block, 2×block, 3×block, ...`
* **Network** = nearest subnet start ≤ IP
* **Broadcast** = next subnet start − 1
* **Host range** = `(network + 1) ... (broadcast − 1)`

#### Example: `192.168.10.130/26`

* `/26` → block size `64` → subnet starts: `0, 64, 128, 192`
* `130` is inside `128–191`
* Network: `192.168.10.128`
* Broadcast: `192.168.10.191`
* Hosts: `192.168.10.129 – 192.168.10.190`

---

### Common CIDR Patterns in NetPractice

#### `/30` (router-to-router / point-to-point)

* Block size `4` in last octet
* Each subnet has exactly:

  * `network`
  * **2 usable IPs**
  * `broadcast`

Example subnet: `10.0.0.8/30`

* network: `10.0.0.8`
* usable: `10.0.0.9` and `10.0.0.10`
* broadcast: `10.0.0.11`

**Typical /30 mistakes**

* ❌ Using `network` or `broadcast` as an interface IP
* ❌ Trying to place **3+ devices** into a /30 (it only supports 2 usable IPs)
* ❌ Setting next-hop to an IP that is not one of the two usable addresses

#### `/26` (subnetting practice)

* One `/24` becomes **4 subnets**:

  * `.0/26`, `.64/26`, `.128/26`, `.192/26`

This is exactly why you often see addresses like `.128` or `.192`: they are **subnet boundaries**, not necessarily host IPs.

---

## 📏 How Many Addresses a Mask Gives You

This is one of the fastest ways to sanity-check your configs.

### 1) CIDR `/n` → total addresses and usable hosts

* **Host bits**: `h = 32 − n`
* **Total addresses** in the subnet: `2^h`
* **Usable hosts** (classic subnet): `2^h − 2`

  * one is the **network address** (all host bits = 0)
  * one is the **broadcast address** (all host bits = 1)

Quick examples:

| CIDR | Host bits `h` | Total addresses | Usable hosts |
| ---- | ------------: | --------------: | -----------: |
| /24  |             8 |             256 |          254 |
| /26  |             6 |              64 |           62 |
| /30  |             2 |               4 |            2 |

> Note: Some real-world networks use `/31` for point-to-point (RFC 3021) and `/32` for a single host route.
> In NetPractice you’ll mostly see the classic “usable = total − 2” rule.

### 2) Dotted mask (e.g., `255.255.255.0`) → how many addresses?

* Each `255` means that octet has **0 host bits** (it’s fully network bits).
* The first non-255 octet tells you the **block size** and the number of addresses per subnet.

Examples:

* `255.255.255.0` is `/24`

  * host bits = 8 → total 256 → usable 254

* `255.255.255.252` is `/30`

  * host bits = 2 → total 4 → usable 2

### 3) `/30` explained (why only 2 usable IPs)

A `/30` has **4 addresses**:

* `network` (…0)
* `host` (…1)
* `host` (…2)
* `broadcast` (…3)

That’s why it’s perfect for router-to-router links: one IP per router interface.

---

## 🧷 Reserved IPv4 Ranges (Private, Local, Special)

These ranges are not “normal public internet” addresses.

### Private (RFC1918) — used inside LANs

* `10.0.0.0/8`
* `172.16.0.0/12` (172.16.0.0 → 172.31.255.255)
* `192.168.0.0/16`

NetPractice may warn you with messages like **“private subnets not routed over internet”** if you try to send these through an “Internet cloud”.

### Link-local (APIPA) — local segment only

* `169.254.0.0/16`

Used when a host can’t get DHCP; traffic stays on the local link (not routed).

### Loopback — the host itself

* `127.0.0.0/8` (commonly `127.0.0.1`)

### Carrier‑Grade NAT (CGNAT) — ISP internal space

* `100.64.0.0/10`

Not public end-user space; used by ISPs for large-scale NAT.

### Multicast

* `224.0.0.0/4` (224.0.0.0 → 239.255.255.255)

### Broadcast / “this network” special cases

* `255.255.255.255` — limited broadcast
* `0.0.0.0` — “unspecified / default route” (you’ll see `0.0.0.0/0` as the default route)

> In NetPractice, the big takeaway is simple:
>
> * Use **private ranges** for internal LAN segments unless the level forces public-looking IPs.
> * Don’t treat special ranges (loopback, link-local, multicast) as normal host LAN addressing.

---

## 🛣️ Routing Cheat Sheet

### Default Gateway

A host sends traffic like this:

1. If destination is in **same subnet** → send directly.
2. Otherwise → send to **default gateway**.

✅ The default gateway must be:

* in the **same subnet** as the host
* the IP of the **router interface** connected to that LAN

---

### Static Routes

A router knows only:

* networks directly connected to its interfaces
* routes you explicitly add (static routes)
* (no dynamic routing in NetPractice)

A static route conceptually says:

> “To reach **DEST/MASK**, forward to **NEXT_HOP**.”

✅ NEXT_HOP must be:

* reachable via one of the router’s directly-connected networks

---

### Longest Prefix Match (Route Choice)

Routers choose the **most specific** matching route:

* `/24` is more specific than `/16`
* `/26` is more specific than `/24`

So if both exist:

* `10.0.0.0/8` and `10.42.0.0/16`

Traffic to `10.42.5.10` will use `10.42.0.0/16`.

---

### Route Summarization (Supernetting)

Sometimes one route can cover multiple subnets.
Typical NetPractice trick:

* combine multiple contiguous subnets into a bigger prefix

Example:

* `192.168.10.128/26` and `192.168.10.192/26`
* can be summarized as `192.168.10.128/25`

That’s why you might write **a network boundary** address (like `.128`) even if no device has exactly that IP — it represents the subnet itself.

---

## 🧰 A Practical Workflow to Solve Any Level

Use this every time:

1. **Read goals first** (what exact connectivity is required).
2. Pick one goal pair (A → B) and trace the path.
3. For every link segment:

   * ensure endpoints are in the **same subnet**
4. For every host:

   * ensure **IP/mask** valid
   * if talking outside subnet → **default gateway** set and reachable
5. For every router:

   * interfaces must be in correct subnets
   * add routes for remote networks
6. Re-run **Check again** and read logs.

> Pro tip: fix one direction first (A→B), then check reverse (B→A).

---

## 🧯 Debugging & Common Mistakes

### 🧾 Interpreting the simulator logs (what they usually mean)

You can often solve a level just by reading the log like a story.

* **"destination does not match any interface"**

  * The device is a **host** and the destination isn’t in its subnet → it will try the routing table.
  * If it then uses `0.0.0.0/0`, your **default route/gateway** is in play.

* **"route match 0.0.0.0/0"**

  * No more specific route existed → device used **default route**.
  * If it fails afterward, your **gateway is wrong / unreachable**.

* **"send to gateway X through interface Y"**

  * Device decided to forward to next-hop `X` using interface `Y`.
  * If `X` is not in the same subnet as `Y`, it can’t ARP / can’t deliver.

* **"packet not for me" (on router)**

  * Router received it, checked destination, and will forward based on routes.

* **"loop detected"**

  * The packet bounced back to a device it already visited.

#### 🔁 Deep dive: Why “loop detected” happens (and how to fix it fast)

#### ✅ Important NetPractice note: a switch can *trigger* “loop detected” in logs

In NetPractice logs you may see sequences like:

* `on switch S: pass to all connections`
* `on switch S test link to A`
* `on A: loop detected`

This can happen because the simulator **floods / tests links** on a switch-like device, and a packet may appear to come back to a node that already saw it.

✅ If you still see **"destination IP reached"** and all goals are valid, this “loop detected” is often **a simulator artifact** rather than a real network loop.

Still, treat it as a quick signal to double-check:

* host gateways are in the same subnet
* return routes exist on routers
* default routes don’t point to each other

Think of a packet like this:

> **If a device doesn’t know the exact path, it sends to its default route.**
> If two devices “default” to each other (or a router sends back where it came from), the packet will ping‑pong → loop.

##### The 3 most common loop patterns in NetPractice

1. **Wrong host gateway**

* Host `A` wants to reach remote network.
* `A` uses gateway `G`, but `G` is not the router interface for that LAN (or not even reachable).
* The frame gets flooded / bounced, and the simulator detects a loop.

✅ Fix:

* Gateway must be the **router interface IP on the same LAN** as the host.

2. **Missing route on a router (return path problem)**

* Forward direction works (A → B), because router knows where B is.
* Reverse direction fails (B → A), because some router in the path has no route back.
* Packet goes to default route and comes back → loop.

✅ Fix:

* Always validate **both directions**.
* Add the missing static route(s) so every router knows how to reach the source subnet.

3. **Two routers defaulting to each other**

* Router `R1` has default route → `R2`
* Router `R2` has default route → `R1`
* Any unknown destination causes infinite ping‑pong.

✅ Fix:

* Avoid mutual default routes.
* Add **specific routes** (more specific prefix) or make only one side default to the other, and the other side has explicit routes.

##### Quick “Loop detected” checklist (30 seconds)

* ✅ Is the **gateway** on each host in the **same subnet**?
* ✅ Does each router have a route to the **destination subnet**?
* ✅ Does each router have a route back to the **source subnet**? *(return path!)*
* ✅ Are any two routers pointing `0.0.0.0/0` at each other?
* ✅ Is any next-hop IP **reachable** via a directly connected subnet?

> Pro tip: if logs show a device twice in the path (A → … → A), the loop is usually between the last two hops before it repeats.

* **"private subnets not routed over internet"**

  * You are trying to route RFC1918 private ranges (e.g. `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) “through the internet cloud”.
  * Fix: keep private traffic inside your controlled routers or use the public addressing in the exercise.

### Common mistakes

* ❌ Gateway is set but **not in the same subnet** as the host
* ❌ Router route points to a **next hop that is not reachable**
* ❌ Two devices look “close” but are in different subnets due to mask
* ❌ Using a **broadcast** or **network** address as a host IP
* ❌ Forgetting that `/30` has only **2 usable IPs**

### How to read logs

Logs usually tell you exactly where the packet died:

* accepted / not for me
* destination does not match any interface
* route match `0.0.0.0/0` (default route)
* loop detected

When you see **loop detected**:

* you probably have a wrong gateway or missing route causing packets to bounce.

---

## 📦 Submission Files

This repository contains:

* **10 exported config files** (one per level) in the **repository root**
* `README.md`

> Double-check exported filenames before pushing.

---

## 📂 Repository Layout

```text
.
├── README.md
├── level1.*
├── level2.*
├── level3.*
├── level4.*
├── level5.*
├── level6.*
├── level7.*
├── level8.*
├── level9.*
└── level10.*
```

*(Extensions depend on your export format.)*

---

## 🔗 Resources

* RFC 791 — IPv4
* RFC 4632 — CIDR
* “Subnetting” basics (any networking textbook)
* OSI vs TCP/IP model overviews

Optional tools for practice (outside defense):

* any subnet calculator website (just for training, not for evaluation)

---

## 🧾 42 Notes (Defense Tips)

* Expect to solve **random levels** during defense.
* External tools are generally not allowed; a simple calculator like `bc` may be allowed.
* Practice **mental subnetting**: `/26`, `/27`, `/30` should become instant.

---

If you want, I can also add a **mini “level cookbook”** section with typical patterns you’ll see (e.g., “2 LANs + 1 router”, “3 routers chain”, “summarize /26 blocks into /25”), based on your exported solutions — you can paste screenshots or describe a couple of your hardest levels.
