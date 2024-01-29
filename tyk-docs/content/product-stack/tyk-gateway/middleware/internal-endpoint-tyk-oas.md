---
title: Using the Internal Endpoint middleware with Tyk OAS APIs
date: 2024-01-26
description: "Using the Internal Endpoint middleware with Tyk OAS APIs"
tags: ["internal endpoint", "internal", "middleware", "per-endpoint", "Tyk OAS"]
---

The [Internal Endpoint]({{< ref "product-stack/tyk-gateway/middleware/internal-endpoint-middleware" >}}) middleware instructs Tyk Gateway not to process external requests to the endpoint (which is a combination of HTTP method and path). Internal requests from other APIs will be processed.

When working with Tyk OAS APIs the middleware is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}) either manually within the `.json` file or from the API Designer in the Tyk Dashboard.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/internal-endpoint-tyk-classic" >}}) page.

## Configuring the middleware in the Tyk Classic API Definition
The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the middleware should be added.

The internal endpoint middleware (`internal`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

The `internal` object has the following configuration:
 - `enabled`: enable the middleware for the endpoint

For example:
```.json {hl_lines=["49-50"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-internal-endpoint",
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
        },
        "/redirect": {
            "get": {
                "operationId": "redirectget",
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
            "name": "example-internal-endpoint",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-internal-endpoint/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "anythingget": {
                    "internal": {
                        "enabled": true
                    }
                },
                "redirectget": {
                    "urlRewrite": {
                        "enabled": true,
                        "pattern": ".*",
                        "rewriteTo": "tyk://self/anything"
                    }
                }
            }
        }
    }
}
```

In this example, two endpoints have been defined:
 - the internal endpoint middleware has been configured for HTTP `GET` requests to the `/anything` endpoint
 - the [URL rewrite]({{< ref "transform-traffic/url-rewriting" >}}) middleware has been configured for HTTP `GET` requests to the `/redirect` endpoint
 
Any calls made directly to `GET /example-internal-endpoint/anything` will be rejected, with Tyk returning `HTTP 403 Forbidden`.

Any calls made to `GET /example-internal-endpoint/redirect` will be redirected to `/example-internal-endpoint/anything`. These will be proxied to the upstream because they originate from within Tyk Gateway (i.e. they are internal requests) - so the response from `GET http://httpbin.org/anything` will be returned.

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the API-level response header transform.

## Configuring the middleware in the API Designer
Adding the Internal Endpoint middleware to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >