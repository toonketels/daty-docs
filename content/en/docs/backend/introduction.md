---
title: "Introduction"
description: ""
lead: ""
date: 2022-09-15T23:34:27+03:00
lastmod: 2022-09-15T23:34:27+03:00
draft: true
images: []
menu:
  docs:
    parent: "backend"
    identifier: "introduction-41f1b208444ec3389ce92d53eab804ba"
weight: 50
toc: true
---

Backend patterns.

## Layers

- [Slim Controller]({{< relref "patterns/slim-controller" >}})
- [Fat Domain]({{< relref "patterns/fat-domain" >}})

## Services

- [Services dont shadow repositories]({{< relref "patterns/service-no-shadow-repo" >}})

## Repositories

- [Repositories implement versioning and locking]({{< relref "patterns/repo-lock" >}})
- [Repositories implement save, not update]({{< relref "patterns/repo-save-update"  >}})

## Testing

- [Test with high level api client]({{< relref "patterns/test-api-client" >}})