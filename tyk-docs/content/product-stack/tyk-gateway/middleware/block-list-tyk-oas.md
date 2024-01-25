---
title: Using the Block List middleware with Tyk OAS APIs
date: 2024-01-24
description: "Using the Block list middleware with Tyk OAS APIs"
tags: ["Block list", "middleware", "per-endpoint", "Tyk OAS"]
---

## Overview
The [block list]({{< ref "product-stack/tyk-gateway/middleware/block-list-middleware" >}}) is a feature designed to block access to specific API endpoints. Tyk Gateway rejects all requests made to endpoints with the block list enabled, returning `HTTP 403 Forbidden`. 

When working with Tyk OAS APIs the middleware is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}); this can be done manually within the `.json` file or from the API Designer in the Tyk Dashboard.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/block-list-tyk-classic" >}}) page.

## Configuring the block list in the Tyk OAS API Definition
The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the middleware should be added.

The block list middleware (`block`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

The `block` object has the following configuration:
 - `enabled`: enable the middleware for the endpoint
 - `ignoreCase`: if set to `true` then the path matching will be case insensitive

For example:
```.json {hl_lines=["47-50", "53-56"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-block-list",
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
            "name": "example-block-list",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-block-list/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "status/200get": {
                    "block": {
                        "enabled": true,
                        "ignoreCase": true
                    }                
                },
                "status/200put": {
                    "block": {
                        "enabled": true,
                        "ignoreCase": true
                    }                
                }
            }
        }
    }
}
```

In this example the block list middleware has been configured for HTTP `GET` and `PUT` requests to the `/status/200` endpoint. Requests to these endpoints will be rejected with `HTTP 403 Forbidden`.
 - the block list has been configured to be case insensitive, so calls to `GET /Status/200` will also be blocked
 - the endpoint path has not been terminated with `$` so requests to, for example, `GET /status/200/foobar` will be rejected as the [regular expression pattern match]({{< ref "product-stack/tyk-gateway/middleware/block-list-middleware#endpoint-parsing" >}}) will recognise this as `GET /status/200`

## Configuring the block list in the API Designer
Adding the block list to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >
