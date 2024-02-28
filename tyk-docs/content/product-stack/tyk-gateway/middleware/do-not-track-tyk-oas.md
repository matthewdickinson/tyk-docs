---
title: Using the Do-Not-Track middleware with Tyk OAS APIs
date: 2024-01-24
description: "Using the Do-Not-Track middleware with Tyk OAS APIs"
tags: ["do-not-track", "endpoint tracking", "analytics", "transaction logging", "middleware", "per-endpoint", "Tyk OAS"]
---

The [Do-Not-Track]({{< ref "product-stack/tyk-gateway/middleware/do-not-track-middleware" >}}) middleware provides the facility to disable generation of transaction records (which are used to track requests to your APIs). When working with Tyk OAS APIs, you can currently disable tracking only at the endpoint-level.

When working with Tyk OAS APIs the middleware is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}) either manually within the `.json` file or from the API Designer in the Tyk Dashboard.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/do-not-track-tyk-classic" >}}) page.

## Configuring the middleware in the Tyk OAS API Definition
The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the middleware should be added.

The do-not-track middleware (`doNotTrackEndpoint`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

The `doNotTrackEndpoint` object has the following configuration:
 - `enabled`: enable the middleware for the endpoint

For example:
```.json {hl_lines=["39-41"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-do-not-track",
        "version": "1.0.0"
    },
    "openapi": "3.0.3",
    "paths": {
        "/anything": {
            "get": {
                "operationId": "anythingget",
                "responses": {
                    "200": {
                        "description": ""
                    }
                }
            }
        }
    },
    "x-tyk-api-gateway": {
        "info": {
            "name": "example-do-not-track",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-do-not-track/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "anythingget": {
                    "doNotTrackEndpoint": {
                        "enabled": true
                    }               
                }
            }
        }
    }
}
```

In this example the do-not-track middleware has been configured for HTTP `GET` requests to the `/anything` endpoint. Any such calls will not generate transaction records from the Gateway and so will not appear in the analytics.

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the do-not-track middleware.

## Configuring the middleware in the API Designer
Adding do-not-track to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >
