---
title: "Repo Save Update"
description: ""
lead: ""
date: 2022-09-15T23:41:00+03:00
lastmod: 2022-09-15T23:41:00+03:00
draft: true
images: []
menu:
  docs:
    parent: ""
    identifier: "repo-save-update-831bf34b3c2da03052a418d1bd51728e"
weight: 121
toc: true
---

{{< alert icon="💡" text="Repositories mainly implement save, not update" />}}	



## Why
- smaller interface to services
- richer abstraction, the caller no longer need to know if its an existing object (update) or a new one, which can simplify the code


## Patterns
- lock version in db starts with 1 (never 0)
- on each save, lock version incremented with 1 by repository
- a new object has lock version 0 when passed to repository
- repo can switch on lock version to either save or update


## Anti-patterns
- services checking lock version if they need to do a save or update
- not passing lock versions all the way to the frontend
- inserting items with version 0 in the db

## Depends on
- [repo-lock]({{< relref "repo-lock" >}})


## Example pattern 

```scala
@Singleton
class LicenseKeyRepositoryImpl @Inject() (
    protected val dbConfigProvider: DatabaseConfigProvider
)(implicit val ec: ExecutionContext)
    extends HasDatabaseConfigProvider[DatystorePostgresProfile] with LicenseKeyTable with LicenseKeyRepository
    with LicenseInfoRepository with SubscriptionPeriodTable {


  // As the repository always increments the version with 1 on every save,
  // a version 0 means it has not been saved yet
  public def save(record: LicenseKeyRecord): DBIO[LicenseKeyRecord] = record.version match {
    case 0 => insert(record)
    case _ => update(record)
  }

  // Auto increment version on save
  private def insert(record: LicenseKeyRecord): DBIO[LicenseKeyRecord] =
    licenseKeys returning licenseKeys += record.copy(version = record.version + 1)

  // Update auto checks the version before incrementing it
  private def update(r: LicenseKeyRecord): DBIO[LicenseKeyRecord] =
    licenseKeys
      .filter(_.id === r.id)
      .filter(_.version === r.version)
      .map(k => (k.version, k.licenseType, k.subscriptionId, k.userId, k.adobeId, k.assignedToUserAt, k.endsAt))
      .update((r.version + 1, r.licenseType, r.subscriptionId, r.userId, r.adobeId, r.assignedToUserAt, r.endsAt))
      .flatMap {
        case 1 => findRecordById(r.id).map { case Some(record) => record }
        case _ => DBIO.failed(ProbableOptimisticLockException())
      }
}

```