---
title: Using the Request Header Transform with Tyk OAS APIs
date: 2024-01-20
description: "Using the Request Header Transform middleware with Tyk OAS APIs"
tags: ["Request Header Transform", "middleware", "per-endpoint", "Tyk OAS"]
---

## Overview
Tyk's [request header transform]({{< ref "transform-traffic/request-headers" >}}) middleware enables you to append or delete headers on requests to your API endpoints before they are passed to your upstream service.

There are two options for this:
 - API-level modification that is applied to all requests to the API
 - endpoint-level modification that is applied only to requests to a specific endpoint

{{< note success >}}
**Note**  

If both API-level and endpoint-level middleware are configured, the API-level transformation will be applied first.
{{< /note >}}

When working with Tyk OAS APIs the transformation is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}); this can be done manually within the `.json` file or from the API Designer in the Tyk Dashboard.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/request-header-tyk-classic" >}}) page.

## Configuring the Request Header Transform in the Tyk OAS API Definition
The API-level and endpoint-level request header transforms are configured in different sections of the API definition, though have a common configuration.

#### API-level transform
To append headers to, or delete headers from, all requests to your API (i.e. for all endpoints) you must add a new `transformRequestHeaders` object to the `middleware.global` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition.

You only need to enable the middleware (set `enabled:true`) and then configure the details of headers to `add` and those to `remove`.

For example:
```.json {hl_lines=["38-56"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-request-header",
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
            "name": "example-request-header",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-request-header/",
                "strip": true
            }
        },
        "middleware": {
            "global": {
                "transformRequestHeaders": {
                    "enabled": true,
                    "remove": [
                        "Auth_Id"
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

This configuration will add three new headers to each request:
 - `X-Static` with the value `foobar`
 - `X-Request-ID` with a dynamic value taken from the `request_id` [context variables]({{< ref "context-variables" >}})
 - `X-User-ID` with a dynamic value taken from the `uid` field in the [session metadata]({{< ref "getting-started/key-concepts/session-meta-data" >}})

It will also delete one header (if present) from each request:
 - `Auth_Id`

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the API-level request header transform.

#### Endpoint-level transform
The request header transform middleware (`transformRequestHeaders`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

You only need to enable the middleware (set `enabled:true`) and then configure the details of headers to `add` and those to `remove`. The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method for which the headers should be transformed.

For example:
```.json {hl_lines=["39-50"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-request-header",
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
            "name": "example-request-header",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-request-header/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "status/200get": {
                    "transformRequestHeaders": {
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

In this example the Request Header Transform middleware has been configured for HTTP `GET` requests to the `/status/200` endpoint. Any request received to that endpoint will have the `X-Static` header removed and the `X-Secret` header added, with the value set to `the-secret-key-is-secret`.

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the endpoint-level request header transform.

#### Combining API-level and Endpoint-level transforms
If the API-level transform in the previous [example]({{< ref "product-stack/tyk-gateway/middleware/request-header-tyk-oas#api-level-transform" >}}) is applied to the same API, then because the API-level transformation is performed first, the `X-Static` header will be added (by the API-level transform) and then removed (by the endpoint-level transform) such that the overall effect of the two transforms for a call to `GET /status/200` would be to add three headers:
 - `X-Request-ID`
 - `X-User-ID`
 - `X-Secret`

and to remove one:
 - `Auth_Id` 

## Configuring the Request Header Transform in the API Designer
Adding and configuring the transforms to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >
