---
tags:
  - go-ipfs
  - release notes
  - pinning
title: go-IPFS 0.8.0, and Remote Pinning, is here!
description: See what's new in go-ipfs 0.8.0 (spoiler - pins are easier)!
date: 2021-02-20
permalink: '/2021-02-19-go-ipfs-0-8-0/'
translationKey: ''
header_image: '/108407727-bac93800-71e9-11eb-93bd-89e25a8b4349.png'
author: Adin Schmahmann
---

## What's new in go-ipfs 0.8.0?

This release is focused on making it easier to work with pins! We have some snazzy new features around being able to ask remote services to pin data for you, and modifying large pin sets is much faster than ever before.

## 🔦 Highlights

### 🧷 Remote pinning services

There is now support for asking remote services to pin data for you.

This feature is a redesign of how we're thinking about pinning and includes some commonly requested features such as:

- Pins can have names (and coming soon metadata)
- Data can be pinned in either the foreground or background
- Pins can be searched for by name, CID, or status

Command-line users benefit from `ipfs pin remote` commands, simplifying remote pinning operations. The built-in pinning service API client also executes all necessary remote calls under the hood:

![go-ipfs 0.8.0 + pinning service flow diagram](../assets/blog-125-go-ipfs-0-8-0-diagram.png)

As long as a pinning service supports the vendor-agnostic [IPFS Pinning Service API](https://ipfs.github.io/pinning-services-api-spec/), it can be used directly in go-ipfs. (If you're a Pinata user, you can already [check out their docs](https://pinata.cloud/documentation#PinningServicesAPI) for how to set everything up.)

Examples include:

```sh
ipfs pin remote service add mysrv https://my-service.example.com/api-endpoint myAccessToken
ipfs pin remote service ls --stat # confirm service mysrv is available
```

```sh
ipfs pin remote add /ipfs/bafymydata --service=mysrv --name=myfile  # will block until status is pinned
ipfs pin remote ls --service=mysrv --name=myfile
ipfs pin remote rm --service=mysrv --name=myfile
```

```sh
ipfs pin remote add /ipfs/bafymydata2 --service=mysrv --name=myfile2 --background  # queue pin request and finish instantly
ipfs pin remote ls --service=mysrv --cid=bafymydata2 --status=queued,pinning,pinned,failed
ipfs pin remote rm --service=mysrv --cid=bafymydata2 --status=queued,pinning,pinned,failed
```

More examples can be found under `ipfs pin remote --help`.

A few notes:

- Remote pinning services work with recursive pins. This means commands like `ipfs pin remote ls` will not list indirectly pinned CIDs.
- By default, only finished, successful pins are listed. To list or remove pending/failed pins, pass explicit statuses: e.g. `--status=queued,pinning,pinned,failed`
- While pinning service data is stored in the configuration file it cannot be edited directly via the `ipfs config` commands due to the sensitive nature of pinning service API keys. The `ipfs pin remote service` commands can be used for interacting with remote service settings.
- An OpenAPI [ipfs-pinning-service.yaml](https://github.com/ipfs/pinning-services-api-spec/blob/main/ipfs-pinning-service.yaml) makes it easy to create or [generate](https://github.com/ipfs/pinning-services-api-spec#code-generation) a compatible client/server. Anyone can implement it and allow for pin management.
- Additionally, HTTP API users now have access to new commands under `/api/v0/pin/remote`.

## 🏠 Remote MFS pinning policy

Every service added via `ipfs pin remote service add` can be tasked to update a pin every time the MFS root changes:

```sh
$ ipfs config --json Pinning.RemoteServices.mysrv.Policies.MFS.Enable
```

To avoid flooding the remote service with many updates, go-ipfs will send updates at most once every five minutes.
Details about customizing behavior of this feature can be found in the [configuration docs](https://github.com/ipfs/go-ipfs/blob/master/docs/config.md#pinningremoteservices-policiesmfs).

### 📌 Faster local pinning and unpinning

The pinning subsystem has been redesigned to be much faster and more flexible in how it tracks pins. For users who are working with many pins this will lead to a big speed increase in listing and modifying the set of pinned items as well as decreased memory usage.

Part of the redesign was set up to account for being able to interact with local pins the same way we can now interact with remote pins (e.g. names, being allowed to pin the same CID multiple times, etc.). Keep posted for more improvements to pinning.

### 🔒 DNSLink names on https:// subdomains

Previously, DNSLink names would have trouble loading over subdomain gateways with HTTPS support since there is no way to get multi-level wildcard certificates (e.g. `en.wikipedia-on-ipfs.org.ipns.dweb.link` cannot be covered by `*.ipns.dweb.link`). Therefore, when trying to load DNSLink names over https:// subdomains in go-ipfs, we now forward to an encoded DNS name. Since DNS names cannot contain `.` in them they are escaped using `-`.

`/ipns/en.wikipedia-on-ipfs.org` →

`ipns://en.wikipedia-on-ipfs.org` →

`https://dweb.link/ipns/en.wikipedia-on-ipfs.org` →

`https://en-wikipedia--on--ipfs-org.ipns.dweb.link` 👈 a single DNS label, no TLS error

Note: The last redirect is specific to HTTPS, and is triggered only when `X-Forwarded-Proto: https` header is present.  
Recipes for setting up your own public gateway can be found in [configuration docs](https://github.com/ipfs/go-ipfs/blob/master/docs/config.md#gateway-recipes).

### 💨 QUIC update

QUIC support has received a number of upgrades, including the ability to take advantage of larger UDP receive buffers for increased performance.

Linux users may notice a logged error on daemon startup if your system needs extra configuration to allow IPFS to increase the buffer size. A helpful link for resolving this is in the log message as well as [here](https://github.com/lucas-clemente/quic-go/wiki/UDP-Receive-Buffer-Size).

### 👋 No more Darwin 386 builds

Go 1.15 (the latest version of Go) [no longer supports](https://github.com/golang/go/issues/34749) Darwin 386 and so we are dropping support as well.

### Changelog

For a full list of updates included in this release, you can review the changelog within this [release post](https://github.com/ipfs/go-ipfs/releases/tag/v0.8.0).

### Coming soon ...

If you're an [IPFS Desktop](https://github.com/ipfs-shipyard/ipfs-desktop) or [IPFS Web UI](https://github.com/ipfs-shipyard/ipfs-webui) fan, you're in luck. These pinning improvements will land soon in GUI form, too — upcoming releases of Desktop and Web UI will allow you to use any remote pinning service that supports the IPFS Pinning Service API.

### Thank you contributors!

A huge thank you to [everyone who contributed](https://github.com/ipfs/go-ipfs/releases/tag/v0.8.0) patches and improvements in this release, all 58 of you! We couldn’t have made this happen without your help and feedback. ❤

### Install, upgrade, and join us!

You can get started by [installing go-ipfs](https://dist.ipfs.tech/#go-ipfs) or upgrading to [go-ipfs 0.8](https://github.com/ipfs/go-ipfs/releases/tag/v0.8.0).

There are many ways to get involved with IPFS based on your skill set, interest, and availability. Please check out our [contribution page](https://github.com/ipfs/community/blob/master/CONTRIBUTING.md) on GitHub for guidance and next steps.

This is an exciting time for IPFS and the web in general. Join us!
