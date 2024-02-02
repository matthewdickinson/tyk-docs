---
title: Using the Enforced Timeout middleware with Tyk OAS APIs
date: 2024-01-19
description: "Using the Enforced Timeout with Tyk OAS APIs"
tags: ["Enforced Timeouts", "middleware", "per-endpoint", "Tyk OAS"]
---

Tyk's [enforced timeout]({{< ref "planning-for-production/ensure-high-availability/enforced-timeouts" >}}) middleware is configured at the endpoint level, where it sets a limit on the response time from the upstream service. If the upstream takes too long to respond to a request, Tyk will terminate the request and return `504 Gateway Timeout` to the client.

When working with Tyk OAS APIs the enforced timeout is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}). You can do this via the Tyk Dashboard API or in the API Designer.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/enforced-timeout-tyk-classic" >}}) page.

## Configuring an enforced timeout in the Tyk OAS API Definition
The enforced timeout middleware (`enforceTimeout`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

You only need to enable the middleware (set `enabled:true`) and then configure the `value` for the timeout. The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the timeout should be enforced.

For example:
```.json {hl_lines=["39-41"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-timeout",
        "version": "1.0.0"
    },
    "openapi": "3.0.3",
    "paths": {
        "/status/200": {
            "get": {
                "operationId": "status/200get",
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
            "name": "example-timeout",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-timeout/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "status/200get": {
                    "enforceTimeout": {
                        "enabled": true,
                        "value": 3
                    }
                }
            }
        }
    }
}
 ```

In this example Tyk OAS API definition, the enforced timeout has been configured to monitor HTTP `GET` requests to the `/status/200` endpoint. It will configure a timer that will expire (`timeout`) 3 seconds after the request is proxied to the upstream service. If the upstream response is not received before the expiry of the timer, that request will be terminated and Tyk will return `504 Gateway Timeout` to the client.

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the enforced timeout.

## Configuring an enforced timeout in the API Designer
Adding and configuring the enforced timeout to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >
