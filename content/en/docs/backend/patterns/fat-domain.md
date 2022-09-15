---
title: "Fat Domain"
description: ""
lead: ""
date: 2022-09-15T17:30:57+02:00
lastmod: 2022-09-15T17:30:57+02:00
draft: true
images: []
menu:
  docs:
    parent: "backend"
weight: 100
toc: true
---

{{< alert icon="💡" text="Move business logic into domain as close to the models as possible" />}}	
	

## Why
- the lowest possible place we can push behavior
- ensures that services always execute the right behavior
- make the core business logic easy to perceive"		

## Anti patterns
- models contain data only
- services implement behavior"	