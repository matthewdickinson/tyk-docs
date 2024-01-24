---
title: Using the Response Header Transform with Tyk OAS APIs
date: 2024-01-24
description: "Using the Response Header Transform middleware with Tyk OAS APIs"
tags: ["Response Header Transform", "middleware", "per-endpoint", "Tyk OAS"]
---

## Overview
Tyk's [response header transform]({{< ref "advanced-configuration/transform-traffic/response-headers" >}}) middleware enables you to append or delete headers on responses received from the upstream service before sending them to the client.

There are two options for this:
 - API-level modification that is applied to all responses for the API
 - endpoint-level modification that is applied only to responses from a specific endpoint

{{< note success >}}
**Note**  

If both API-level and endpoint-level middleware are configured, the endpoint-level transformation will be applied first.
{{< /note >}}

When working with Tyk OAS APIs the transformation is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}); this can be done manually within the `.json` file or from the API Designer in the Tyk Dashboard.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/response-header-tyk-classic" >}}) page.

## Configuring the Response Header Transform in the Tyk OAS API Definition
The API-level and endpoint-level response header transforms have a common configuration but are configured in different sections of the API definition.

#### API-level transform
To append headers to, or delete headers from, all responses from your API (i.e. for all endpoints) you must add a new `transformResponseHeaders` object to the `middleware.global` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition.

You only need to enable the middleware (set `enabled:true`) and then configure the details of headers to `add` and those to `remove`.

For example:
```.json {hl_lines=["38-57"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-response-header",
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
            "name": "example-response-header",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-response-header/",
                "strip": true
            }
        },
        "middleware": {
            "global": {
                "transformResponseHeaders": {
                    "enabled": true,
                    "remove": [
                        "X-Secret"
                    ],
                    "add": [
                        {
                            "name": "X-Static",
                            "value": "foobar"
                        },
                        {
                            "name": "X-Request-ID",
                            "value": "$tyk_context.request_id"
                        },
                        {
                            "name": "X-User-ID",
                            "value": "$tyk_meta.uid"
                        }
                    ]
                }
            }
        }
    }
}
```

This configuration will add three new headers to each response:
 - `X-Static` with the value `foobar`
 - `X-Request-ID` with a dynamic value taken from the `request_id` [context variable]({{< ref "context-variables" >}})
 - `X-User-ID` with a dynamic value taken from the `uid` field in the [session metadata]({{< ref "getting-started/key-concepts/session-meta-data" >}})

It will also delete one header (if present) from each response:
 - `X-Secret`

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the API-level response header transform.

#### Endpoint-level transform
The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the headers should be transformed.

The response header transform middleware (`transformResponseMethod`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

You only need to enable the middleware (set `enabled:true`) and then configure the details of headers to `add` and those to `remove`.

For example:
```.json {hl_lines=["39-50"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-response-method",
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
            "name": "example-response-method",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-response-method/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "status/200get": {
                    "transformResponseHeaders": {
                        "enabled": true,
                        "remove": [
                            "X-Static"
                        ],
                        "add": [
                            {
                                "name": "X-Secret",
                                "value": "the-secret-key-is-secret"
                            }
                        ]
                    }
                }
            }
        }
    }
}
```

In this example the Response Header Transform middleware has been configured for HTTP `GET` requests to the `/status/200` endpoint. Any response received from the upstream service following a request to that endpoint will have the `X-Static` header removed and the `X-Secret` and `X-New` headers added (with values set to `the-secret-key-is-secret` and `another-header`).

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the endpoint-level response header transform.

#### Combining API-level and Endpoint-level transforms
If the API-level transform in the previous [example]({{< ref "product-stack/tyk-gateway/middleware/response-header-tyk-oas#api-level-transform" >}}) is applied to the same API, then because the endpoint-level transformation is performed first, the `X-Secret` header will be added (by the endpoint-level transform) and then removed (by the API-level transform) such that the overall effect of the two transforms for a call to `GET /status/200` would be to add four headers:
 - `X-Request-ID`
 - `X-User-ID`
 - `X-Static`
 - `X-New`

## Configuring the Response Method Transform in the API Designer
Adding and configuring the transforms to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >
