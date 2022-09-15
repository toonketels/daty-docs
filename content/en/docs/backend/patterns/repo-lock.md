---
title: "Repo Lock"
description: ""
lead: ""
date: 2022-09-15T23:38:44+03:00
lastmod: 2022-09-15T23:38:44+03:00
draft: true
images: []
menu:
  docs:
    parent: "backend"
    identifier: "repo-lock-9d3d02142ec7185fb400319c15e03c7c"
weight: 120
toc: true
---


{{< alert icon="💡" text="Repositories are responsible for versioning of data and taking optimistic locks" />}}	


## Why
- clear separation of responsiblity between repository and services
- pushes some responsibility into the repository, making the services lighter (which are already too fat)
- pushes the responsiblity to locking as low as possible down the stack
- allows for smaller interface in repositories

## Patterns
- repo increment locks on save
- throws exception if no update registered


## Example


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