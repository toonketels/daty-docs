---
title: "Test Api Client"
description: ""
lead: ""
date: 2022-09-15T23:45:32+03:00
lastmod: 2022-09-15T23:45:32+03:00
draft: true
images: []
menu:
  docs:
    parent: ""
    identifier: "test-api-client-dbb61619814b838f199aa5e9f85ebbd7"
weight: 800
toc: true
---

{{< alert icon="💡" text="Use high level api client in integration tests." />}}	

## Why
- removes repetition from integration tests
- makes the integration tests more succinct
- faster to add new tests cases
- faster and easier to refactor
- makes it clearer when an two different users are involved in one test, each will initialize its own client

## Pattern
- a high level api client that takes a token
- exposes methods to make api requests which return Either[Error, Resource]
- performs deserialization of success
- performs deserialization of error in specific errors

## Anti-pattern
- integration tests makes low level request, insepcts the status, deserializes the response

## Example pattern 


```scala 
// Define it
class DatylonApiClient(client: RequestSupport, token: Token) {

  def unassignLicenseKey(licenseKeyId: UUID, version: Int): Either[BadRequestException, UnassignedLicenseKey] = {

    val response = client.make(client.putRequest(
      s"/licenses/${licenseKeyId}/unassign",
      Json.toJson(UnassignLicenseKeyTO(version)),
      token))

    status(response) match {
      case 200 => Right(contentAsJson(response).as[UnassignedLicenseKey])
      case _ => Left(contentAsJson(response).as[BadRequestException])
    }
  }
}
```

```scala
// Using it
class UnassignLicenseKeySpec extends IntegrationSpec with CleanSlate
  with PlaySpec with UserSupport with ApiClientSupport with AIPluginSupport {

  behavior of "unassign"

  it should "allow unassigning a license key" in withAuthenticatedUser() { (user, token) =>

    val result = withApiClient(token).unassignLicenseKey(license.id, 2)

    result should matchPattern { case Right(UnassignedLicenseKey(`licenseId`, `subscriptionId`, 3)) => }
  }
}

```


## Example anti pattern

```scala

class UnassignLicenseKeySpec extends IntegrationSpec with CleanSlate
  with PlaySpec with UserSupport with ApiClientSupport with AIPluginSupport {

  behavior of "unassign"

  it should "allow unassigning a license key" in withAuthenticatedUser() { (user, token) =>

      val check = make(getRequest(s"/licenses?userId=${user.id}", token))
      val allLicenses = (contentAsJson(check) \ "items").as[Seq[License]]
      allLicenses should contain theSameElementsAs customLicense :: Nil
    }
  }
}
```