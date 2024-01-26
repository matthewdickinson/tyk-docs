---
title: Using the Request Method Transform with Tyk OAS APIs
date: 2024-01-20
description: "Using the Request Method Transform middleware with Tyk OAS APIs"
tags: ["Request Method Transform", "middleware", "per-endpoint", "Tyk OAS"]
---

Tyk's [request method transform]({{< ref "advanced-configuration/transform-traffic/request-method-transform" >}}) middleware is configured at the endpoint level, where it modifies the HTTP method used in the request to a configured value.

When working with Tyk OAS APIs the transformation is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}); this can be done manually within the `.json` file or from the API Designer in the Tyk Dashboard.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/request-method-tyk-classic" >}}) page.

## Configuring the Request Method Transform in the Tyk OAS API Definition
The request method transform middleware (`transformRequestMethod`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

You only need to enable the middleware (set `enabled:true`) and then configure `toMethod` as the new HTTP method to which the request should be transformed. The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the method should be transformed.

All standard HTTP methods are supported: `GET`, `PUT`, `POST`, `PATCH`, `DELETE`, `HEAD`, `OPTIONS`.

For example:
```.json {hl_lines=["39-41"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-request-method",
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
            "name": "example-request-method",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-request-method/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "status/200get": {
                    "transformRequestMethod": {
                        "enabled": true,
                        "toMethod": "POST"
                    }
                }
            }
        }
    }
}
```

In this example the Request Method Transform middleware has been configured for HTTP `GET` requests to the `/status/200` endpoint. Any request received to that endpoint will be modified to `POST /status/200`.

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the request method transform.

## Configuring the Request Method Transform in the API Designer
Adding and configuring the transform to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >
