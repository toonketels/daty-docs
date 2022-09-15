---
title: "Service No Shadow Repo"
description: ""
lead: ""
date: 2022-09-15T23:44:14+03:00
lastmod: 2022-09-15T23:44:14+03:00
draft: true
images: []
menu:
  docs:
    parent: ""
    identifier: "service-no-shadow-repo-2ced4f81fd353de1d1a5d8a122298793"
weight: 110
toc: true
---

{{< alert icon="💡" text="Services should not shadow read methods on repositories for other services" />}}	
	

## Why
- bloat the codebase by services shadowing repositories
- services import too many other services (better use repositories)
- better distinction between write behavior (services) and reading data (repositories)

## Patterns
- services use repositories to read data
- services can use other services for writing data
- controllers could use service to read data

## Anti-patterns
- services shadowing read operations on its main repositories, not adding any functionality


## Example anti patterns

```scala

@Singleton
class UserService @Inject() (
    val userRepository: UserRepository
)(implicit val executionContext: ExecutionContext, val tracingService: TracingService) {


  def findRootUser: ReadDBIO[Option[UserWithMetaData]] =
    userRepository.findRootUser

  def findUserByEmail(userEmail: String): ReadDBIO[Option[UserWithMetadataAndRoles]] =
    userRepository.findUserByEmail(userEmail)

}


```