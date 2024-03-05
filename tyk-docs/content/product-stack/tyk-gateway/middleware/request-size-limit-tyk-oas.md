---
title: Using the Request Size Limit middleware with Tyk OAS APIs
date: 2024-03-04
description: "Using the Request Size Limit middleware with Tyk OAS APIs"
tags: ["request size limit", "size limit", "security", "middleware", "per-endpoint", "per-API", "Tyk OAS", "Tyk OAS API"]
---

The [request size limit]({{< ref "basic-config-and-security/control-limit-traffic/request-size-limits" >}}) middleware enables you to apply limits to the size of requests made to your HTTP APIs. You might use this feature to protect your Tyk Gateway or upstream services from avoid excessive memory usage or brute force attacks.

The middleware is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}). You can do this via the Tyk Dashboard API or in the API Designer.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/request-size-limit-tyk-classic" >}}) page.

## Configuring the middleware in the Tyk OAS API Definition
There are three different levels of granularity that can be used when configuring a request size limit.
 - [system-wide]({{< ref "basic-config-and-security/control-limit-traffic/request-size-limits#applying-a-system-wide-size-limit" >}}): affecting all APIs deployed on the gateway
 - [API-level]({{< ref "product-stack/tyk-gateway/middleware/request-size-limit-tyk-oas#applying-a-size-limit-for-a-specific-api" >}}): affecting all endpoints for an API
 - [endpoint-level]({{< ref "product-stack/tyk-gateway/middleware/request-size-limit-tyk-oas#applying-a-size-limit-for-a-specific-endpoint" >}}): affecting a single API endpoint

### Applying a size limit for a specific API
The API-level rate limit has not yet been implemented for Tyk OAS APIs.

You can work around this by implementing a combination of endpoint-level rate limits and [allow]({{< ref "advanced-configuration/transform-traffic/endpoint-designer#allowlist" >}}) or [block]({{< ref "advanced-configuration/transform-traffic/endpoint-designer#blocklist" >}}) lists.

### Applying a size limit for a specific endpoint
The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the middleware should be added.

The virtual endpoint middleware (`requestSizeLimit`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

The `requestSizeLimit` object has the following configuration:
 - `enabled`: enable the middleware for the endpoint
 - `value`: the maximum size permitted for a request to the endpoint (in bytes) 

For example:
```.json {hl_lines=["39-44"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-request-size-limit",
        "version": "1.0.0"
    },
    "openapi": "3.0.3",
    "paths": {
        "/anything": {
            "post": {
                "operationId": "anythingpost",
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
            "name": "example-request-size-limit",
            "state": {
                "active": true,
                "internal": false
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },          
        "server": {
            "listenPath": {
                "value": "/example-request-size-limit/",                
                "strip": true
            }
        },      
        "middleware": {
            "operations": {
                "anythingpost": {
                    "requestSizeLimit": {
                        "enabled": true,
                        "value": 100
                    }
                }
            }
        }
    }
}
```

In this example the endpoint-level Request Size Limit middleware has been configured for HTTP `POST` requests to the `/anything` endpoint. For any call made to this endpoint, Tyk will check the size of the payload (Request body) and, if it is larger than 100 bytes, will reject the request, returning `HTTP 400 Request is too large`.

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the virtual endpoint middleware.

## Configuring the middleware in the API Designer
Adding a Virtual Endpoint to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >
