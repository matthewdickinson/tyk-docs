---
title: Using the Circuit Breaker middleware with Tyk OAS APIs
date: 2024-01-19
description: "Using the Circuit Breaker with Tyk OAS APIs"
tags: ["circuit breaker", "middleware", "per-endpoint", "Tyk OAS"]
---

Tyk's [circuit breaker]({{< ref "planning-for-production/ensure-high-availability/circuit-breakers" >}}) middleware is configured at the endpoint level, where it monitors the rate of failure responses (HTTP 500 or higher) received from the upstream service. If that failure rate exceeds the configured threshold, the circuit breaker will trip and Tyk will block further requests to that endpoint (returning `HTTP 503 Service temporarily unavailable`) until the end of a recovery (cooldown) time period.

When working with Tyk OAS APIs the circuit breaker is configured in the [Tyk OAS API Definition]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc#operation" >}}). You can do this via the Tyk Dashboard API or in the API Designer.

If you're using the legacy Tyk Classic APIs, then check out the [Tyk Classic]({{< ref "product-stack/tyk-gateway/middleware/circuit-breaker-tyk-classic" >}}) page.

## Configuring the Circuit Breaker in the Tyk OAS API Definition
The circuit breaker middleware (`circuitBreaker`) can be added to the `operations` section of the Tyk OAS Extension (`x-tyk-api-gateway`) in your Tyk OAS API Definition for the appropriate `operationId` (as configured in the `paths` section of your OpenAPI Document).

You only need to enable the middleware (set `enabled:true`) and then configure the `threshold`, `sampleSize`, `coolDownPeriod` and `halfOpenStateEnabled`. The design of the Tyk OAS API Definition takes advantage of the `operationID` defined in the OpenAPI Document that declares both the path and method to which the circuit breaker should be deployed.

```.json {hl_lines=["39-44"],linenos=true, linenostart=1}
{
    "components": {},
    "info": {
        "title": "example-breaker",
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
            "name": "example-breaker",
            "state": {
                "active": true
            }
        },
        "upstream": {
            "url": "http://httpbin.org/"
        },
        "server": {
            "listenPath": {
                "value": "/example-breaker/",
                "strip": true
            }
        },
        "middleware": {
            "operations": {
                "status/200get": {
                    "circuitBreaker": {
                        "enabled": true,
                        "threshold": 0.5,
                        "sampleSize": 10,
                        "coolDownPeriod": 60,
                        "halfOpenStateEnabled": true
                    }
                }
            }
        }
    }
}
```

In this example Tyk OAS API Definition the circuit breaker has been configured to monitor HTTP `GET` requests to the `/status/200` endpoint. It will configure a sampling window (`sampleSize`) of 10 requests and calculate the ratio of failed requests (those returning HTTP 500 or above) within that window. If the ratio of failed requests exceeds 50% (`threshold = 0.5`) then the breaker will be tripped. After it has tripped, the circuit breaker will remain _open_ for 60 seconds (`coolDownPeriod`). The circuit breaker will operate in _half-open_ mode (`halfOpenStateEnabled = true`) so when _open_, Tyk will periodically poll the upstream service to test if it has become available again.

When the breaker has tripped, it will return `HTTP 503 Service temporarily unavailable` in response to any calls to `GET /status/200`.

The configuration above is a complete and valid Tyk OAS API Definition that you can import into Tyk to try out the circuit breaker.

## Configuring the Circuit Breaker in the API Designer
Adding and configuring the circuit breaker to your API endpoints is easy when using the API Designer in the Tyk Dashboard, simply follow the steps taken in this short video:

 < placeholder for video >