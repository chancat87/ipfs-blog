---
title: "IPFS is moving beyond the sponsored gateways"
description: "How IPFS is shifting from centralized public gateways to distributed infrastructure and client-side retrieval."
date: 2026-08-25
permalink: '/2026-08-beyond-sponsored-gateways/'
header_image: '/beyond-sponsored-gateways-header.png'
tags:
  - ipfs
  - gateways
  - infrastructure
---

# **IPFS is moving beyond the sponsored gateways** 

Launched in 2017 with the goal of building an uncensorable, resilient web, IPFS has grown into infrastructure that handles roughly 250 million requests per day. It powers censorship-resistant publishing, web3 infrastructure, scientific data archives, developer tooling, and long-lived content across the open web.

A lot has changed since the early days. While IPFS started with two canonical implementations and free public gateways, there are now more than a dozen active implementations and community-driven libraries: Javascript nodes that run in a browser tab, modular libraries that make content addressing work with a wider variety of network topologies, service worker gateways that fetch and verify content client-side, mature self-hosted gateways, and paid pinning and retrieval providers (including x402-style pay-per-request for agent traffic).

Today, we're making a few changes, starting with how the ipfs.io and dweb.link gateways work. Rather than acting as central servers that fetch content on your behalf, they'll hand the browser a [service worker](https://github.com/ipfs/service-worker-gateway) that makes direct peer-to-peer connections and verifies the content itself. We're also making related changes to how we fund core maintainership. Read on to learn about what this means, why we're doing it, and how this might affect you or your users.

## **Gateway changes for ipfs.io and dweb.link**

IPFS gateways were always meant to be a stepping stone, a way to reach the network over plain HTTP while we built toward native IPFS support in browsers, without requiring a dedicated app or browser extension. Over time, hardcoded gateway URLs spread across the web and traffic re-centralized around a small number of public gateways.

We've spent the last two years laying the groundwork for this next chapter. The service worker gateway in late 2024, and its performance is now on par with traditional gateways. Many larger users are now running dedicated gateways for their own content. We also have more options for users and providers: Javascript IPFS nodes (ex [helia](https://helia.io/)) that can run in a browser tab, a service worker gateway that can be used to fetch IPFS content client-side ([inbrowser.link](http://inbrowser.link)), self-hosted gateway implementations (ex [rainbow](https://github.com/ipfs/rainbow)), and various x402 & MPP [paid service providers](https://docs.ipfs.tech/quickstart/pin/#self-hosting-option) for IPFS pinning & retrieval ([Pinata](https://pinata.cloud/), [Filebase](https://filebase.com/ipfs-pinning/), [Lighthouse](https://lighthouse.storage/) & more). 

In parallel, implementations have diversified and AI-assisted coding has only accelerated that change. Together with client-side retrieval, this brings the network closer to the direct peer-to-peer connections IPFS was always meant to have. The scarce thing now is interoperability, not code: clear specs, real conformance tests, empowered community builders, and interoperable primitives. This is where we need to route our community resources today instead of subsidizing retrieval bandwidth. 

## **Transition Plan**

* **Browser users get direct browser connections, seamlessly.** Users who navigate to ipfs.io and dweb.link are now redirected to the service worker gateway at inbrowser.link. This maintains the same user experience and ease of development while enabling local verification, offline access, and better security.  
* **Apps & Hotlinks → @helia/verified-fetch.** Apps that previously hotlinked images, JSON, metadata, or scripts through the public gateways should move to `@helia/verified-fetch` or the [Drop-In Service Worker](https://github.com/ipshipyard/drop-in-service-worker). For those looking for more verification and resiliency, Helia's [Verified Fetch](https://www.npmjs.com/package/@helia/verified-fetch) offers trustless content retrieval and automatic fallback to multiple providers.  
* **Backend Services → Self-Hosted Gateways.** For backend and automated clients, we recommend running dedicated IPFS nodes or gateways using tools like Rainbow, Someguy, or Kubo (if you also need to publish). These services shouldn't rely on the public-good gateway at all. We've created a [guide to help you transition painlessly](https://docs.ipfs.tech/how-to/replace-public-gateways-with-self-hosted-ipfs/).  
* **Rate limiting** has begun on ipfs.io and dweb.link for hotlinked and backend traffic, and will be increasing. Limited responses will point at the migration paths above to help route users to the appropriate channel.

## **Maintainership changes**

We are also changing how core IPFS maintainership is organized. [Shipyard](http://ipshipyard.com/) has played an important role stewarding much of the implementation work around Kubo, Boxo, Helia, Rainbow, public gateways, and related infrastructure. At the same time, we saw a new dynamic emerging in the IPFS developer community. During 2024-2025, nearly a dozen small, focused implementations like atcute, libipld, and more emerged, many from the fast-moving atproto community.

Value in IPFS is already spread across a lot of repos, languages, and businesses. Some teams use Kubo, some Helia, some Rainbow, some build their own implementations against the specs. A lot of content-addressed software (ex through Filecoin, AT Protocol, DASL, IPLD, CAR, or plain CIDs) doesn't look like "an IPFS app" from the outside, but that's exactly what it is. As the IP Shipyard team winds down their work, IPFS is shifting toward what keeps an open protocol healthy: specs, conformance tests, implementation diversity, grants to maintainers and builders, migration guides, and more agile support for the most-used libraries and tools.

## **Where we're going next**

The IPFS mission was never to run a big gateway. It was to change how the web addresses, verifies, and moves data, so knowledge doesn't depend on any single point of control.

What's not changing is that IPFS is still IPFS: open protocols for storing, verifying, routing, and sharing data across distributed networks. You should still be able to verify data yourself, move between providers, and use IPFS building blocks to create systems that don't fall over when one server or one company goes away.

We're still funding public goods, just the ones that go furthest. Rather than permanently subsidizing a single endpoint, we're investing in shared foundations and interoperability: specs, tests, routing standards, browser integration, and migration paths.

As AI makes code cheap and endless, the scarce thing is no longer software but agreement: the protocols that let independently built systems work together, and the people who coordinate and apply them to solve real-world problems. Protocols encode who holds power, which is why we'll continue work at that layer. The next wave of systems should be locked open by design, where owning data, verifying it, and moving it between providers are the path of least resistance, not a hard road you take on principle.

Public gateways got us started. Now it is time to move beyond them.
