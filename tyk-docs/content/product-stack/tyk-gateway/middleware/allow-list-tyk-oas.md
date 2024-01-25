---
title: Using the Allow List middleware with Tyk OAS APIs
date: 2024-01-24
description: "Using the Allow list middleware with Tyk OAS APIs"
tags: ["Allow list", "middleware", "per-endpoint", "Tyk OAS"]
---

## Overview
The [allow list]({{< ref "product-stack/tyk-gateway/middleware/allow-list-middleware" >}}) is a feature designed to restrict access to only specific API endpoints. It rejects requests to endpoints not specifically "allowed", returning `HTTP 403 Forbidden`. This enhances the security of the API by preventing unauthorised access to endpoints that are not explicitly permitted.

When working with Tyk OAS APIs the middleware is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}); this can be done manually within the `.json` file or from the API Designer in the Tyk Dashboard.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/allow-list-tyk-classic" >}}) page.

## Configuring the allow list in the Tyk OAS API Definition
The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the middleware should be added.

The allow list middleware (`allow`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

The `allow` object has the following configuration:
 - `enabled`: enable the middleware for the endpoint
 - `ignoreCase`: if set to `true` then the path matching will be case insensitive

For example:
```.json {hl_lines=["47-50", "53-56"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-allow-list",
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
            },
            "put": {
                "operationId": "status/200put",
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
            "name": "example-allow-list",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-allow-list/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "status/200get": {
                    "allow": {
                        "enabled": true,
                        "ignoreCase": true
                    }                
                },
                "status/200put": {
                    "allow": {
                        "enabled": true,
                        "ignoreCase": true
                    }                
                }
            }
        }
    }
}
```

In this example the allow list middleware has been configured for HTTP `GET` and `PUT` requests to the `/status/200` endpoint. Requests to any other endpoints will be rejected with `HTTP 403 Forbidden` unless they also have the allow list middleware enabled.
 - the allow list has been configured to be case insensitive, so calls to `GET /Status/200` will be allowed
 - the endpoint path has not been terminated with `$` so requests to, for example, `GET /status/200/foobar` will be allowed as the [regular expression pattern match]({{< ref "product-stack/tyk-gateway/middleware/allow-list-middleware#endpoint-parsing" >}}) will recognise this as `GET /status/200`

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the API-level response header transform.
## Configuring the allow list in the API Designer
Adding the allow list to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >
