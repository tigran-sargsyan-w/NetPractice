*This project has been created as part of the 42 curriculum by tsargsya.*

# NetPractice

## Description

**NetPractice** is a practical networking exercise that introduces the fundamentals of computer networking through 10 interactive levels. The goal is to make each provided network diagram work by correctly configuring **IP addressing**, **subnet masks**, and **routing/gateway settings** across hosts, switches, and routers.

You will learn how devices communicate within a subnet, how traffic reaches other networks via a **default gateway**, and how routers forward packets based on destination networks.

## Project Structure

This repository contains:

* **10 exported configuration files** (one per level), stored at the **root** of the repository.
* This `README.md`

Example (names may vary depending on your exports):

```text
.
├── README.md
├── level1.json
├── level2.json
├── level3.json
├── level4.json
├── level5.json
├── level6.json
├── level7.json
├── level8.json
├── level9.json
└── level10.json
```

## Instructions

### 1) Run the training interface

1. Download the NetPractice archive from the project page and extract it anywhere.
2. Open `index.html` in a web browser.

   * **Recommended:** Google Chrome / Chromium-based browsers (some browsers like Firefox may block the tool).
3. Enter your **42 login** in the interface (important for correct export/evaluation workflow).

### 2) Solve levels

* There are **10 levels**. Each one shows a broken network diagram and a list of **goals** at the top.
* Edit only the **unshaded** fields until all goals pass.
* Use:

  * **Check again** to validate your configuration
  * **Get my config** to export your solution

### 3) Export and submit

* After completing each level, click **Get my config** and save the exported file.
* Repeat for all 10 levels.
* Place the **10 exported files at the root of this repository** (one per level).
* Push your repository and submit as usual.

## Tips

* The log panel at the bottom of the interface is very useful to understand errors like:

  * invalid IP/mask
  * missing gateway
  * wrong route / wrong destination network

## Resources

### Networking concepts practiced

* **TCP/IP addressing** (IPv4)
* **Subnet masks / CIDR notation**
* **Network vs host part** of an address
* **Default gateway**
* **Routers vs switches**
* **Routing basics** (packet forwarding based on destination network)
* **OSI model overview** (where routing vs switching fits)

### References

* RFC 791 — Internet Protocol (IPv4)
* RFC 4632 — CIDR (Classless Inter-Domain Routing)
* “Subnetting” explanations (any classic networking textbook / course notes)
* OSI model and TCP/IP model overviews (intro networking materials)

### How AI was used

* Used AI as a study assistant to:

  * explain subnetting/CIDR calculations and ranges
  * sanity-check routing logic (gateway selection, route specificity)
  * help draft and structure this README
    All final configurations and decisions were verified manually in the NetPractice interface.

## Notes about evaluation

During defense, you may be asked to solve random levels under time constraints, and external tools are not allowed (except a simple calculator like `bc`).
