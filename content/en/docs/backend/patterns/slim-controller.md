---
title: "Slim Controller"
description: ""
lead: ""
date: 2022-09-15T23:47:02+03:00
lastmod: 2022-09-15T23:47:02+03:00
draft: true
images: []
menu:
  docs:
    parent: ""
    identifier: "slim-controller-d43847063279099ff593b7381dee1068"
weight: 100
toc: true
---

{{< alert icon="💡" text="Controllers should call a method on a service or repository and translate the response." />}}	

		

## Example

```scala


class DeviceAuthController @Inject() (
    service: DeviceAuthService
)(implicit executionContext: ExecutionContext, tracingService: TracingService)
    extends DefaultController with TokenGeneration {

  def code(): Action[RequestCode] = actions.UnsecuredAction.async(parseRequestCode) { implicit request =>

    val span = request.spanOrElse("DeviceAuthController")
    val requestCode = request.body

    inTransaction(span) {
      service.requestCode(requestCode)
    }.map {
      case Right(RequestCodeOk(code)) => Ok(Json.toJson(code)(formatCode))
      case Left(InvalidClientId)      => Unauthorized(toJsonError("invalid_client"))
      case Left(InvalidScope)         => Unauthorized(toJsonError("invalid_scope"))
    }
  }

```