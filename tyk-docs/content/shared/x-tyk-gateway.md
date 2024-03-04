## Tyk OAS API Definition Object

XTykAPIGateway contains custom Tyk API extensions for the OAS definition.

**Field: `info` ([Info](#info))**
Info contains the main metadata about the API definition.

**Field: `upstream` ([Upstream](#upstream))**
Upstream contains the configurations related to the upstream.

**Field: `server` ([Server](#server))**
Server contains the configurations related to the server.

**Field: `middleware` ([Middleware](#middleware))**
Middleware contains the configurations related to the proxy middleware.

### **Info**

Info contains the main metadata about the API definition.

**Field: `id` (`string`)**
ID is the unique ID of the API.

Tyk classic API definition: `api_id`.

**Field: `dbId` (`string`)**
DBID is the unique database ID of the API.

Tyk classic API definition: `id`.

**Field: `orgId` (`string`)**
OrgID is the ID of the organisation which the API belongs to.

Tyk classic API definition: `org_id`.

**Field: `name` (`string`)**
Name is the name of the API.

Tyk classic API definition: `name`.

**Field: `expiration` (`string`)**
Expiration date.

**Field: `state` ([State](#state))**
State holds configuration about API definition states (active, internal).

**Field: `versioning` ([Versioning](#versioning))**
Versioning holds configuration for API versioning.

### **Upstream**

Upstream holds configuration for an upstream server.

**Field: `url` (`string`)**
URL defines the target URL that the request should be proxied to.

Tyk classic API definition: `proxy.target_url`.

**Field: `serviceDiscovery` ([ServiceDiscovery](#servicediscovery))**
ServiceDiscovery contains the configuration related to Service Discovery.

Tyk classic API definition: `proxy.service_discovery`.

**Field: `test` ([Test](#test))**
Test contains the configuration related to uptime tests.

**Field: `mutualTLS` ([MutualTLS](#mutualtls))**
MutualTLS contains the configuration related to upstream mutual TLS.

**Field: `certificatePinning` ([CertificatePinning](#certificatepinning))**
CertificatePinning contains the configuration related to certificate pinning.

### **Server**

Server contains the configuration related to the OAS API definition.

**Field: `listenPath` ([ListenPath](#listenpath))**
ListenPath represents the path to listen on. Any requests coming into the host, on the port that Tyk is configured to run on,
that match this path will have the rules defined in the API definition applied.

**Field: `slug` (`string`)**
Slug is the Tyk Cloud equivalent of listen path.

Tyk classic API definition: `slug`.

**Field: `authentication` ([Authentication](#authentication))**
Authentication contains the configurations related to authentication to the API.

**Field: `clientCertificates` ([ClientCertificates](#clientcertificates))**
ClientCertificates contains the configurations related to static mTLS.

**Field: `gatewayTags` ([GatewayTags](#gatewaytags))**
GatewayTags contains segment tags to configure which GWs your APIs connect to.

**Field: `customDomain` ([Domain](#domain))**
CustomDomain is the domain to bind this API to.


Tyk classic API definition: `domain`.

### **Middleware**

Middleware holds configuration for middleware.

**Field: `global` ([Global](#global))**
Global contains the configurations related to the global middleware.

**Field: `operations` ([Operations](#operations))**
Operations configuration.

### **State**

State holds configuration about API definition states (active, internal).

**Field: `active` (`boolean`)**
Active enables the API.

Tyk classic API definition: `active`.

**Field: `internal` (`boolean`)**
Internal makes the API accessible only internally.

Tyk classic API definition: `internal`.

### **Versioning**

Versioning holds configuration for API versioning.

Tyk classic API definition: `version_data`.

**Field: `enabled` (`boolean`)**
Enabled is a boolean flag, if set to `true` it will enable versioning of an API.

**Field: `name` (`string`)**
Name contains the name of the version as entered by the user ("v1" or similar).

**Field: `default` (`string`)**
Default contains the default version name if a request is issued without a version.

**Field: `location` (`string`)**
Location contains versioning location information. It can be one of the following:

- `header`,
- `url-param`,
- `url`.

**Field: `key` (`string`)**
Key contains the name of the key to check for versioning information.

**Field: `versions` ([[]VersionToID](#versiontoid))**
Versions contains a list of versions that map to individual API IDs.

**Field: `stripVersioningData` (`boolean`)**
StripVersioningData is a boolean flag, if set to `true`, the API responses will be stripped of versioning data.

### **ServiceDiscovery**

ServiceDiscovery holds configuration required for service discovery.

**Field: `enabled` (`boolean`)**
Enabled enables Service Discovery.


Tyk classic API definition: `service_discovery.use_discovery_service`.

**Field: `queryEndpoint` (`string`)**
QueryEndpoint is the endpoint to call, this would usually be Consul, etcd or Eureka K/V store.

Tyk classic API definition: `service_discovery.query_endpoint`.

**Field: `dataPath` (`string`)**
DataPath is the namespace of the data path - where exactly in your service response the namespace can be found.
For example, if your service responds with:

```
{
 "action": "get",
 "node": {
   "key": "/services/single",
   "value": "http://httpbin.org:6000",
   "modifiedIndex": 6,
   "createdIndex": 6
 }
}
```

then your namespace would be `node.value`.


Tyk classic API definition: `service_discovery.data_path`.

**Field: `useNestedQuery` (`boolean`)**
UseNestedQuery enables using a combination of `dataPath` and `parentDataPath`.
It is necessary when the data lives within this string-encoded JSON object.

```
{
 "action": "get",
 "node": {
   "key": "/services/single",
   "value": "{"hostname": "http://httpbin.org", "port": "80"}",
   "modifiedIndex": 6,
   "createdIndex": 6
 }
}
```


Tyk classic API definition: `service_discovery.use_nested_query`.

**Field: `parentDataPath` (`string`)**
ParentDataPath is the namespace of the where to find the nested
value, if `useNestedQuery` is `true`. In the above example, it
would be `node.value`. You would change the `dataPath` setting
to be `hostname`, since this is where the host name data
resides in the JSON string. Tyk automatically assumes that
`dataPath` in this case is in a string-encoded JSON object and
will try to deserialize it.


Tyk classic API definition: `service_discovery.parent_data_path`.

**Field: `portDataPath` (`string`)**
PortDataPath is the port of the data path. In the above nested example, we can see that there is a separate `port` value
for the service in the nested JSON. In this case, you can set the `portDataPath` value and Tyk will treat `dataPath` as
the hostname and zip them together (this assumes that the hostname element does not end in a slash or resource identifier
such as `/widgets/`). In the above example, the `portDataPath` would be `port`.


Tyk classic API definition: `service_discovery.port_data_path`.

**Field: `useTargetList` (`boolean`)**
UseTargetList should be set to `true`, if you are using load balancing. Tyk will treat the data path as a list and
inject it into the target list of your API definition.


Tyk classic API definition: `service_discovery.use_target_list`.

**Field: `cacheTimeout` (`int64`)**
CacheTimeout is the timeout of a cache value when a new data is loaded from a discovery service.
Setting it too low will cause Tyk to call the SD service too often, setting it too high could mean that
failures are not recovered from quickly enough.

Deprecated: The field is deprecated, usage needs to be updated to configure caching.


Tyk classic API definition: `service_discovery.cache_timeout`.

**Field: `cache` ([ServiceDiscoveryCache](#servicediscoverycache))**
Cache holds cache related flags.


Tyk classic API definition:.
- `service_discovery.cache_disabled`
- `service_discovery.cache_timeout`

**Field: `targetPath` (`string`)**
TargetPath is to set a target path to append to the discovered endpoint, since many SD services
only provide host and port data. It is important to be able to target a specific resource on that host.
Setting this value will enable that.


Tyk classic API definition: `service_discovery.target_path`.

**Field: `endpointReturnsList` (`boolean`)**
EndpointReturnsList is set `true` when the response type is a list instead of an object.


Tyk classic API definition: `service_discovery.endpoint_returns_list`.

### **Test**

Test holds the test configuration for service discovery.

**Field: `serviceDiscovery` ([ServiceDiscovery](#servicediscovery))**
ServiceDiscovery contains the configuration related to test Service Discovery.

Tyk classic API definition: `proxy.service_discovery`.

### **MutualTLS**

MutualTLS holds configuration related to mTLS on APIs, domain to certificate mappings.

**Field: `enabled` (`boolean`)**
Enabled enables/disables upstream mutual TLS auth for the API.

Tyk classic API definition: `upstream_certificates_disabled`.

**Field: `domainToCertificateMapping` ([[]DomainToCertificate](#domaintocertificate))**
DomainToCertificates maintains the mapping of domain to certificate.

Tyk classic API definition: `upstream_certificates`.

### **CertificatePinning**

CertificatePinning holds the configuration about mapping of domains to pinned public keys.

**Field: `enabled` (`boolean`)**
Enabled is a boolean flag, if set to `true`, it enables certificate pinning for the API.


Tyk classic API definition: `certificate_pinning_disabled`.

**Field: `domainToPublicKeysMapping` ([PinnedPublicKeys](#pinnedpublickeys))**
DomainToPublicKeysMapping maintains the mapping of domain to pinned public keys.


Tyk classic API definition: `pinned_public_keys`.

### **ListenPath**

ListenPath represents the path the server should listen on.

**Field: `value` (`string`)**
Value is the value of the listen path e.g. `/api/` or `/` or `/httpbin/`.

Tyk classic API definition: `proxy.listen_path`.

**Field: `strip` (`boolean`)**
Strip removes the inbound listen path in the outgoing request. e.g. `http://acme.com/httpbin/get` where `httpbin`
is the listen path. The `httpbin` listen path which is used to identify the API loaded in Tyk is removed,
and the outbound request would be `http://httpbin.org/get`.

Tyk classic API definition: `proxy.strip_listen_path`.

### **Authentication**

Authentication types contains configuration about the authentication methods and security policies applied to requests.

**Field: `enabled` (`boolean`)**
Enabled makes the API protected when one of the authentication modes is enabled.


Tyk classic API definition: `!use_keyless`.

**Field: `stripAuthorizationData` (`boolean`)**
StripAuthorizationData ensures that any security tokens used for accessing APIs are stripped and not leaked to the upstream.


Tyk classic API definition: `strip_auth_data`.

**Field: `baseIdentityProvider` (`string`)**
BaseIdentityProvider enables multi authentication mechanism and provides the session object that determines rate limits, ACL rules and quotas.
It should be set to one of the following:

- `auth_token`
- `hmac_key`
- `basic_auth_user`
- `jwt_claim`
- `oidc_user`
- `oauth_key`
- `custom_auth`


Tyk classic API definition: `base_identity_provided_by`.

**Field: `hmac` ([HMAC](#hmac))**
HMAC contains the configurations related to HMAC authentication mode.


Tyk classic API definition: `auth_configs["hmac"]`.

**Field: `oidc` ([OIDC](#oidc))**
OIDC contains the configurations related to OIDC authentication mode.


Tyk classic API definition: `auth_configs["oidc"]`.

**Field: `custom` ([CustomPluginAuthentication](#custompluginauthentication))**
Custom contains the configurations related to Custom authentication mode.


Tyk classic API definition: `auth_configs["coprocess"]`.

**Field: `securitySchemes` ([SecuritySchemes](#securityschemes))**
SecuritySchemes contains security schemes definitions.

### **ClientCertificates**

ClientCertificates holds a list of client certificates which are allowed to make requests against the server.

**Field: `enabled` (`boolean`)**
Enabled enables static mTLS for the API.

**Field: `allowlist` (`[]string`)**
Allowlist is the list of client certificates which are allowed.

### **GatewayTags**

GatewayTags holds a list of segment tags that should apply for a gateway.

**Field: `enabled` (`boolean`)**
Enabled enables use of segment tags.

**Field: `tags` (`[]string`)**
Tags is a list of segment tags

### **Domain**

Domain holds the configuration of the domain name the server should listen on.

**Field: `enabled` (`boolean`)**
Enabled allow/disallow the usage of the domain.

**Field: `name` (`string`)**
Name is the name of the domain.

### **Global**

Global holds configuration applies globally: CORS and caching.

**Field: `pluginConfig` ([PluginConfig](#pluginconfig))**
PluginConfig contains the configuration related custom plugin bundles/driver.

**Field: `cors` ([CORS](#cors))**
CORS contains the configuration related to cross origin resource sharing.

Tyk classic API definition: `CORS`.

**Field: `prePlugin` ([PrePlugin](#preplugin))**
PrePlugin contains configuration related to custom pre-authentication plugin.

Tyk classic API definition: `custom_middleware.pre`.

**Field: `postAuthenticationPlugin` ([PostAuthenticationPlugin](#postauthenticationplugin))**
PostAuthenticationPlugin contains configuration related to custom post authentication plugin.

Tyk classic API definition: `custom_middleware.post_key_auth`.

**Field: `postPlugin` ([PostPlugin](#postplugin))**
PostPlugin contains configuration related to custom post plugin.

Tyk classic API definition: `custom_middleware.post`.

**Field: `responsePlugin` ([ResponsePlugin](#responseplugin))**
ResponsePlugin contains configuration related to custom post plugin.

Tyk classic API definition: `custom_middleware.response`.

**Field: `cache` ([Cache](#cache))**
Cache contains the configurations related to caching.

Tyk classic API definition: `cache_options`.

### **Operations**

Operations holds Operation definitions.

Type defined as object of `Operation` values, see [Operation](#operation) definition.

### **VersionToID**

VersionToID contains a single mapping from a version name into an API ID.

**Field: `name` (`string`)**
Name contains the user chosen version name, e.g. `v1` or similar.

**Field: `id` (`string`)**
ID is the API ID for the version set in Name.

### **ServiceDiscoveryCache**

ServiceDiscoveryCache holds configuration for caching ServiceDiscovery data.

**Field: `enabled` (`boolean`)**
Enabled turns service discovery cache on or off.


Tyk classic API definition: `service_discovery.cache_disabled`.

**Field: `timeout` (`int64`)**
Timeout is the TTL for a cached object in seconds.


Tyk classic API definition: `service_discovery.cache_timeout`.

### **DomainToCertificate**

DomainToCertificate holds a single mapping of domain name into a certificate.

**Field: `domain` (`string`)**
Domain contains the domain name.

**Field: `certificate` (`string`)**
Certificate contains the certificate mapped to the domain.

### **PinnedPublicKeys**

PinnedPublicKeys is a list of domains and pinned public keys for them.

Type defined as array of `PinnedPublicKey` values, see [PinnedPublicKey](#pinnedpublickey) definition.

### **HMAC**

HMAC holds the configuration for the HMAC authentication mode.

**Field: `enabled` (`boolean`)**
Enabled enables the HMAC authentication mode.

Tyk classic API definition: `enable_signature_checking`.

**Field: `allowedAlgorithms` (`[]string`)**
AllowedAlgorithms is the array of HMAC algorithms which are allowed. Tyk supports the following HMAC algorithms:

- `hmac-sha1`
- `hmac-sha256`
- `hmac-sha384`
- `hmac-sha512`

and reads the value from algorithm header.


Tyk classic API definition: `hmac_allowed_algorithms`.

**Field: `allowedClockSkew` (`float64`)**
AllowedClockSkew is the amount of milliseconds that will be tolerated for clock skew. It is used against replay attacks.
The default value is `0`, which deactivates clock skew checks.

Tyk classic API definition: `hmac_allowed_clock_skew`.

### **OIDC**

OIDC contains configuration for the OIDC authentication mode.

**Field: `enabled` (`boolean`)**
Enabled enables the OIDC authentication mode.


Tyk classic API definition: `use_openid`.

**Field: `segregateByClientId` (`boolean`)**
SegregateByClientId is a boolean flag. If set to `true, the policies will be applied to a combination of Client ID and User ID.


Tyk classic API definition: `openid_options.segregate_by_client`.

**Field: `providers` ([[]Provider](#provider))**
Providers contains a list of authorised providers and their Client IDs, and matched policies.


Tyk classic API definition: `openid_options.providers`.

**Field: `scopes` ([Scopes](#scopes))**
Scopes contains the defined scope claims.

### **CustomPluginAuthentication**

CustomPluginAuthentication holds configuration for custom plugins.

**Field: `enabled` (`boolean`)**
Enabled enables the CustomPluginAuthentication authentication mode.


Tyk classic API definition: `enable_coprocess_auth`/`use_go_plugin_auth`.

**Field: `config` ([AuthenticationPlugin](#authenticationplugin))**
Config contains configuration related to custom authentication plugin.

Tyk classic API definition: `custom_middleware.auth_check`.

### **SecuritySchemes**

SecuritySchemes holds security scheme values, filled with Import().

### **PluginConfig**

PluginConfig holds configuration for custom plugins.

**Field: `driver` (`string`)**
Driver configures which custom plugin to be used.
It's value should be set to one of the following:

- `otto`,
- `python`,
- `lua`,
- `grpc`,
- `goplugin`.


Tyk classic API definition: `custom_middleware.driver`.

**Field: `bundle` ([PluginBundle](#pluginbundle))**
Bundle configures custom plugin bundles.

**Field: `data` ([PluginConfigData](#pluginconfigdata))**
Data configures custom plugin data.

### **CORS**

CORS holds configuration for cross-origin resource sharing.

**Field: `enabled` (`boolean`)**
Enabled is a boolean flag, if set to `true`, this option enables CORS processing.


Tyk classic API definition: `CORS.enable`.

**Field: `maxAge` (`int`)**
MaxAge indicates how long (in seconds) the results of a preflight request can be cached. The default is 0 which stands for no max age.


Tyk classic API definition: `CORS.max_age`.

**Field: `allowCredentials` (`boolean`)**
AllowCredentials indicates whether the request can include user credentials like cookies,
HTTP authentication or client side SSL certificates.


Tyk classic API definition: `CORS.allow_credentials`.

**Field: `exposedHeaders` (`[]string`)**
ExposedHeaders indicates which headers are safe to expose to the API of a CORS API specification.


Tyk classic API definition: `CORS.exposed_headers`.

**Field: `allowedHeaders` (`[]string`)**
AllowedHeaders holds a list of non simple headers the client is allowed to use with cross-domain requests.


Tyk classic API definition: `CORS.allowed_headers`.

**Field: `optionsPassthrough` (`boolean`)**
OptionsPassthrough is a boolean flag. If set to `true`, it will proxy the CORS OPTIONS pre-flight
request directly to upstream, without authentication and any CORS checks. This means that pre-flight
requests generated by web-clients such as SwaggerUI or the Tyk Portal documentation system
will be able to test the API using trial keys.

If your service handles CORS natively, then enable this option.


Tyk classic API definition: `CORS.options_passthrough`.

**Field: `debug` (`boolean`)**
Debug is a boolean flag, If set to `true`, this option produces log files for the CORS middleware.


Tyk classic API definition: `CORS.debug`.

**Field: `allowedOrigins` (`[]string`)**
AllowedOrigins holds a list of origin domains to allow access from. Wildcards are also supported, e.g. `http://*.foo.com`


Tyk classic API definition: `CORS.allowed_origins`.

**Field: `allowedMethods` (`[]string`)**
AllowedMethods holds a list of methods to allow access via.


Tyk classic API definition: `CORS.allowed_methods`.

### **PrePlugin**

PrePlugin configures pre stage plugins.

**Field: `plugins` ([CustomPlugins](#customplugins))**
Plugins configures custom plugins to be run on pre authentication stage.
The plugins would be executed in the order of configuration in the list.

### **PostAuthenticationPlugin**

PostAuthenticationPlugin configures post authentication plugins.

**Field: `plugins` ([CustomPlugins](#customplugins))**
Plugins configures custom plugins to be run on pre authentication stage.
The plugins would be executed in the order of configuration in the list.

### **PostPlugin**

PostPlugin configures post plugins.

**Field: `plugins` ([CustomPlugins](#customplugins))**
Plugins configures custom plugins to be run on post stage.
The plugins would be executed in the order of configuration in the list.

### **ResponsePlugin**

ResponsePlugin configures response plugins.

**Field: `plugins` ([CustomPlugins](#customplugins))**
Plugins configures custom plugins to be run on post stage.
The plugins would be executed in the order of configuration in the list.

### **Cache**

Cache holds configuration for caching the requests.

**Field: `enabled` (`boolean`)**
Enabled turns global cache middleware on or off. It is still possible to enable caching on a per-path basis
by explicitly setting the endpoint cache middleware.


Tyk classic API definition: `cache_options.enable_cache`.

**Field: `timeout` (`int64`)**
Timeout is the TTL for a cached object in seconds.


Tyk classic API definition: `cache_options.cache_timeout`.

**Field: `cacheAllSafeRequests` (`boolean`)**
CacheAllSafeRequests caches responses to (`GET`, `HEAD`, `OPTIONS`) requests overrides per-path cache settings in versions,
applies across versions.


Tyk classic API definition: `cache_options.cache_all_safe_requests`.

**Field: `cacheResponseCodes` (`[]int`)**
CacheResponseCodes is an array of response codes which are safe to cache e.g. `404`.


Tyk classic API definition: `cache_options.cache_response_codes`.

**Field: `cacheByHeaders` (`[]string`)**
CacheByHeaders allows header values to be used as part of the cache key.


Tyk classic API definition: `cache_options.cache_by_headers`.

**Field: `enableUpstreamCacheControl` (`boolean`)**
EnableUpstreamCacheControl instructs Tyk Cache to respect upstream cache control headers.


Tyk classic API definition: `cache_options.enable_upstream_cache_control`.

**Field: `controlTTLHeaderName` (`string`)**
ControlTTLHeaderName is the response header which tells Tyk how long it is safe to cache the response for.


Tyk classic API definition: `cache_options.cache_control_ttl_header`.

### **PinnedPublicKey**

PinnedPublicKey contains a mapping from the domain name into a list of public keys.

**Field: `domain` (`string`)**
Domain contains the domain name.

**Field: `publicKeys` (`[]string`)**
PublicKeys contains a list of the public keys pinned to the domain name.

### **Provider**

Provider defines an issuer to validate and the Client ID to Policy ID mappings.

**Field: `issuer` (`string`)**
Issuer contains a validation value for the issuer claim, usually a domain name e.g. `accounts.google.com` or similar.

**Field: `clientToPolicyMapping` ([[]ClientToPolicy](#clienttopolicy))**
ClientToPolicyMapping contains mappings of Client IDs to Policy IDs.

### **Scopes**

Scopes holds the scope to policy mappings for a claim name.

**Field: `claimName` (`string`)**
ClaimName contains the claim name.

**Field: `scopeToPolicyMapping` ([[]ScopeToPolicy](#scopetopolicy))**
ScopeToPolicyMapping contains the mappings of scopes to policy IDs.

### **AuthenticationPlugin**

AuthenticationPlugin holds the configuration for custom authentication plugin.

**Field: `enabled` (`boolean`)**
Enabled enables custom authentication plugin.

**Field: `functionName` (`string`)**
FunctionName is the name of authentication method.

**Field: `path` (`string`)**
Path is the path to shared object file in case of gopluign mode or path to js code in case of otto auth plugin.

**Field: `rawBodyOnly` (`boolean`)**
RawBodyOnly if set to true, do not fill body in request or response object.

**Field: `idExtractor` ([IDExtractor](#idextractor))**
IDExtractor configures ID extractor with coprocess custom authentication.

### **PluginBundle**

PluginBundle holds configuration for custom plugins.

**Field: `enabled` (`boolean`)**
Enabled enables the custom plugin bundles.


Tyk classic API definition: `custom_middleware_bundle_disabled`.

**Field: `path` (`string`)**
Path is the path suffix to construct the URL to fetch plugin bundle from.
Path will be suffixed to `bundle_base_url` in gateway config.

### **PluginConfigData**

PluginConfigData configures config data for custom plugins.

**Field: `enabled` (`boolean`)**
Enabled enables custom plugin config data.

**Field: `value` (`any`)**
Value is the value of custom plugin config data.

### **CustomPlugins**

CustomPlugins is a list of CustomPlugin.

Type defined as array of `CustomPlugin` values, see [CustomPlugin](#customplugin) definition.

### **ClientToPolicy**

ClientToPolicy contains a 1-1 mapping between Client ID and Policy ID.

**Field: `clientId` (`string`)**
ClientID contains a Client ID.

**Field: `policyId` (`string`)**
PolicyID contains a Policy ID.

### **ScopeToPolicy**

ScopeToPolicy contains a single scope to policy ID mapping.

**Field: `scope` (`string`)**
Scope contains the scope name.

**Field: `policyId` (`string`)**
PolicyID contains the Policy ID.

### **IDExtractor**

IDExtractor configures ID Extractor.

**Field: `enabled` (`boolean`)**
Enabled enables ID extractor with coprocess authentication.

**Field: `source` (`string`)**
Source is the source from which ID to be extracted from.

**Field: `with` (`string`)**
With is the type of ID extractor to be used.

**Field: `config` ([IDExtractorConfig](#idextractorconfig))**
Config holds the configuration specific to ID extractor type mentioned via With.

### **CustomPlugin**

CustomPlugin configures custom plugin.

**Field: `enabled` (`boolean`)**
Enabled enables the custom pre plugin.

**Field: `functionName` (`string`)**
FunctionName is the name of authentication method.

**Field: `path` (`string`)**
Path is the path to shared object file in case of gopluign mode or path to js code in case of otto auth plugin.

**Field: `rawBodyOnly` (`boolean`)**
RawBodyOnly if set to true, do not fill body in request or response object.

**Field: `requireSession` (`boolean`)**
RequireSession if set to true passes down the session information for plugins after authentication.
RequireSession is used only with JSVM custom middleware.

### **IDExtractorConfig**

IDExtractorConfig specifies the configuration for ID extractor.

**Field: `headerName` (`string`)**
HeaderName is the header name to extract ID from.

**Field: `formParamName` (`string`)**
FormParamName is the form parameter name to extract ID from.

**Field: `regexp` (`string`)**
Regexp is the regular expression to match ID.

**Field: `regexpMatchIndex` (`int`)**
RegexpMatchIndex is the index from which ID to be extracted after a match.
Default value is 0, ie if regexpMatchIndex is not provided ID is matched from index 0.

**Field: `xPathExp` (`string`)**
XPathExp is the xpath expression to match ID.

### **EndpointPostPlugins**

Type defined as array of `EndpointPostPlugin` values, see [EndpointPostPlugin](#endpointpostplugin) definition.

### **OAuthProvider**

**Field: `jwt` ([JWTValidation](#jwtvalidation))**


**Field: `introspection` ([Introspection](#introspection))**


### **JWTValidation**

**Field: `enabled` (`boolean`)**
Enabled enables OAuth access token validation by introspection to a third party.

**Field: `signingMethod` (`string`)**
SigningMethod to verify signing method used in jwt - allowed values HMAC/RSA/ECDSA.

**Field: `source` (`string`)**
Source is the secret to verify signature, it could be one among:
- a base64 encoded static secret,
- a valid JWK url in plain text,
- a valid JWK url in base64 encoded format.

**Field: `identityBaseField` (`string`)**
IdentityBaseField is the identity claim name.

**Field: `issuedAtValidationSkew` (`uint64`)**
IssuedAtValidationSkew is the clock skew to be considered while validating iat claim.

**Field: `notBeforeValidationSkew` (`uint64`)**
NotBeforeValidationSkew is the clock skew to be considered while validating nbf claim.

**Field: `expiresAtValidationSkew` (`uint64`)**
ExpiresAtValidationSkew is the clock skew to be considered while validating exp claim.

### **Introspection**

**Field: `enabled` (`boolean`)**
Enabled enables OAuth access token validation by introspection to a third party.

**Field: `url` (`string`)**
URL is the URL of the third party provider's introspection endpoint.

**Field: `clientId` (`string`)**
ClientID is the public identifier for the client, acquired from the third party.

**Field: `clientSecret` (`string`)**
ClientSecret is a secret known only to the client and the authorization server, acquired from the third party.

**Field: `identityBaseField` (`string`)**
IdentityBaseField is the key showing where to find the user id in the claims. If it is empty, the `sub` key is looked at.

**Field: `cache` ([IntrospectionCache](#introspectioncache))**
Cache is the caching mechanism for introspection responses.

### **IntrospectionCache**

**Field: `enabled` (`boolean`)**
Enabled enables the caching mechanism for introspection responses.

**Field: `timeout` (`int64`)**
Timeout is the duration in seconds of how long the cached value stays.
For introspection caching, it is suggested to use a short interval.

### **ExternalOAuth**

**Field: `enabled` (`boolean`)**


**Field: `providers` ([[]OAuthProvider](#oauthprovider))**


### **Allowance**

Allowance describes allowance actions and behaviour.

**Field: `enabled` (`boolean`)**
Enabled is a boolean flag, if set to `true`, then individual allowances (allow, block, ignore) will be enforced.

**Field: `ignoreCase` (`boolean`)**
IgnoreCase is a boolean flag, If set to `true`, checks for requests allowance will be case insensitive.

### **AllowanceType**

AllowanceType holds the valid allowance types values.

### **AuthSource**

AuthSource defines an authentication source.

**Field: `enabled` (`boolean`)**
Enabled enables the auth source.

Tyk classic API definition: `auth_configs[X].use_param/use_cookie`.

**Field: `name` (`string`)**
Name is the name of the auth source.

Tyk classic API definition: `auth_configs[X].param_name/cookie_name`.

### **AuthSources**

AuthSources defines authentication source configuration: headers, cookies and query parameters.

Tyk classic API definition: `auth_configs{}`.

**Field: `header` ([AuthSource](#authsource))**
Header contains configurations for the header value auth source, it is enabled by default.


Tyk classic API definition: `auth_configs[x].header`.

**Field: `cookie` ([AuthSource](#authsource))**
Cookie contains configurations for the cookie value auth source.


Tyk classic API definition: `auth_configs[x].cookie`.

**Field: `query` ([AuthSource](#authsource))**
Query contains configurations for the query parameters auth source.


Tyk classic API definition: `auth_configs[x].query`.

### **Basic**

Basic type holds configuration values related to http basic authentication.

**Field: `enabled` (`boolean`)**
Enabled enables the basic authentication mode.

Tyk classic API definition: `use_basic_auth`.

**Field: `disableCaching` (`boolean`)**
DisableCaching disables the caching of basic authentication key.

Tyk classic API definition: `basic_auth.disable_caching`.

**Field: `cacheTTL` (`int`)**
CacheTTL is the TTL for a cached basic authentication key in seconds.

Tyk classic API definition: `basic_auth.cache_ttl`.

**Field: `extractCredentialsFromBody` ([ExtractCredentialsFromBody](#extractcredentialsfrombody))**
ExtractCredentialsFromBody helps to extract username and password from body. In some cases, like dealing with SOAP,
user credentials can be passed via request body.

### **CachePlugin**

CachePlugin holds the configuration for the cache plugins.

**Field: `enabled` (`boolean`)**
Enabled is a boolean flag. If set to `true`, the advanced caching plugin will be enabled.

**Field: `cacheByRegex` (`string`)**
CacheByRegex defines a regular expression used against the request body to produce a cache key.

Example value: `\"id\":[^,]*` (quoted json value).

**Field: `cacheResponseCodes` (`[]int`)**
CacheResponseCodes contains a list of valid response codes for responses that are okay to add to the cache.

### **EndpointPostPlugin**

EndpointPostPlugin contains endpoint level post plugin configuration.

**Field: `enabled` (`boolean`)**
Enabled enables post plugin.

**Field: `name` (`string`)**
Name is the name of plugin function to be executed.

**Field: `path` (`string`)**
Path is the path to plugin.

### **EnforceTimeout**

EnforceTimeout holds the configuration for enforcing request timeouts.

**Field: `enabled` (`boolean`)**
Enabled is a boolean flag. If set to `true`, requests will enforce a configured timeout.

**Field: `value` (`int`)**
Value is the configured timeout in seconds.

### **ExtractCredentialsFromBody**

ExtractCredentialsFromBody configures extracting credentials from the request body.

**Field: `enabled` (`boolean`)**
Enabled enables extracting credentials from body.

Tyk classic API definition: `basic_auth.extract_from_body`.

**Field: `userRegexp` (`string`)**
UserRegexp is the regex for username e.g. `<User>(.*)</User>`.

Tyk classic API definition: `basic_auth.userRegexp`.

**Field: `passwordRegexp` (`string`)**
PasswordRegexp is the regex for password e.g. `<Password>(.*)</Password>`.

Tyk classic API definition: `basic_auth.passwordRegexp`.

### **FieldDocError**

FieldDocError holds a list of errors.

### **FieldInfo**

FieldInfo holds details about a field.

**Field: `doc` (`string`)**
Doc is field docs. comments that are not part of docs are excluded.

**Field: `json_name` (`string`)**
JSONName is the corresponding json name of the field.

**Field: `json_type` (`string`)**
JSONType valid json type if it was found

**Field: `go_path` (`string`)**
GoPath is the go path of this field starting from root object

**Field: `map_key` (`string`)**
MapKey is the map key type, if this field is a map

**Field: `is_array` (`boolean`)**
IsArray reports if this field is an array.

### **FromOASExamples**

FromOASExamples configures mock responses should be returned from OAS example responses.

**Field: `enabled` (`boolean`)**
Enabled enables getting a mock response from OAS examples or schemas documented in OAS.

**Field: `code` (`int`)**
Code is the default HTTP response code that the gateway reads from the path responses documented in OAS.

**Field: `contentType` (`string`)**
ContentType is the default HTTP response body type that the gateway reads from the path responses documented in OAS.

**Field: `exampleName` (`string`)**
ExampleName is the default example name among multiple path response examples documented in OAS.

### **Header**

Header holds a header name and value pair.

**Field: `name` (`string`)**
Name is the name of the header.

**Field: `value` (`string`)**
Value is the value of the header.

### **JWT**

JWT holds the configuration for the JWT middleware.

**Field: `enabled` (`boolean`)**


**Field: `source` (`string`)**


**Field: `signingMethod` (`string`)**


**Field: `identityBaseField` (`string`)**


**Field: `skipKid` (`boolean`)**


**Field: `policyFieldName` (`string`)**


**Field: `clientBaseField` (`string`)**


**Field: `scopes` ([Scopes](#scopes))**


**Field: `defaultPolicies` (`[]string`)**


**Field: `issuedAtValidationSkew` (`uint64`)**


**Field: `notBeforeValidationSkew` (`uint64`)**


**Field: `expiresAtValidationSkew` (`uint64`)**


**Field: `idpClientIdMappingDisabled` (`boolean`)**
IDPClientIDMappingDisabled prevents Tyk from automatically detecting the use of certain IDPs based on standard claims
that they include in the JWT: `client_id`, `cid`, `clientId`. Setting this flag to `true` disables the mapping and avoids
accidentally misidentifying the use of one of these IDPs if one of their standard values is configured in your JWT.

### **MockResponse**

MockResponse configures the mock responses.

**Field: `enabled` (`boolean`)**
Enabled enables the mock response middleware.

**Field: `code` (`int`)**
Code is the HTTP response code that will be returned.

**Field: `body` (`string`)**
Body is the HTTP response body that will be returned.

**Field: `headers` ([[]Header](#header))**
Headers are the HTTP response headers that will be returned.

**Field: `fromOASExamples` ([FromOASExamples](#fromoasexamples))**
FromOASExamples is the configuration to extract a mock response from OAS documentation.

### **Notifications**

Notifications holds configuration for updates to keys.

**Field: `sharedSecret` (`string`)**
SharedSecret is the shared secret used in the notification request.

**Field: `onKeyChangeUrl` (`string`)**
OnKeyChangeURL is the URL a request will be triggered against.

### **OAuth**

OAuth configures the OAuth middleware.

**Field: `enabled` (`boolean`)**


**Field: `allowedAuthorizeTypes` (`[]string`)**


**Field: `refreshToken` (`boolean`)**


**Field: `authLoginRedirect` (`string`)**


**Field: `notifications` ([Notifications](#notifications))**


### **Operation**

Operation holds a request operation configuration, allowances, tranformations, caching, timeouts and validation.

**Field: `allow` ([Allowance](#allowance))**
Allow request by allowance.

**Field: `block` ([Allowance](#allowance))**
Block request by allowance.

**Field: `ignoreAuthentication` ([Allowance](#allowance))**
IgnoreAuthentication ignores authentication on request by allowance.

**Field: `transformRequestMethod` ([TransformRequestMethod](#transformrequestmethod))**
TransformRequestMethod allows you to transform the method of a request.

**Field: `transformRequestBody` ([TransformRequestBody](#transformrequestbody))**
TransformRequestBody allows you to transform request body.
When both `path` and `body` are provided, body would take precedence.

**Field: `cache` ([CachePlugin](#cacheplugin))**
Cache contains the caching plugin configuration.

**Field: `enforceTimeout` ([EnforceTimeout](#enforcetimeout))**
EnforceTimeout contains the request timeout configuration.

**Field: `validateRequest` ([ValidateRequest](#validaterequest))**
ValidateRequest contains the request validation configuration.

**Field: `mockResponse` ([MockResponse](#mockresponse))**
MockResponse contains the mock response configuration.

**Field: `virtualEndpoint` ([VirtualEndpoint](#virtualendpoint))**
VirtualEndpoint contains virtual endpoint configuration.

**Field: `postPlugins` ([EndpointPostPlugins](#endpointpostplugins))**
PostPlugins contains endpoint level post plugins configuration.

### **Path**

Path holds plugin configurations for HTTP method verbs.

**Field: `DELETE` ([Plugins](#plugins))**


**Field: `GET` ([Plugins](#plugins))**


**Field: `HEAD` ([Plugins](#plugins))**


**Field: `OPTIONS` ([Plugins](#plugins))**


**Field: `PATCH` ([Plugins](#plugins))**


**Field: `POST` ([Plugins](#plugins))**


**Field: `PUT` ([Plugins](#plugins))**


**Field: `TRACE` ([Plugins](#plugins))**


**Field: `CONNECT` ([Plugins](#plugins))**


### **Paths**

Paths is a mapping of API endpoints to Path plugin configurations.

Type defined as object of `Path` values, see [Path](#path) definition.

### **Plugins**

Plugins configures common settings for each plugin, allowances, transforms, caching and timeouts.

**Field: `allow` ([Allowance](#allowance))**
Allow request by allowance.

**Field: `block` ([Allowance](#allowance))**
Block request by allowance.

**Field: `ignoreAuthentication` ([Allowance](#allowance))**
Ignore authentication on request by allowance.

**Field: `transformRequestMethod` ([TransformRequestMethod](#transformrequestmethod))**
TransformRequestMethod allows you to transform the method of a request.

**Field: `cache` ([CachePlugin](#cacheplugin))**
Cache allows you to cache the server side response.

**Field: `enforcedTimeout` ([EnforceTimeout](#enforcetimeout))**
EnforceTimeout allows you to configure a request timeout.

### **SecurityScheme**

SecurityScheme defines an Importer interface for security schemes.

### **Signature**

Signature holds the configuration for signature validation.

**Field: `enabled` (`boolean`)**


**Field: `algorithm` (`string`)**


**Field: `header` (`string`)**


**Field: `query` ([AuthSource](#authsource))**


**Field: `secret` (`string`)**


**Field: `allowedClockSkew` (`int64`)**


**Field: `errorCode` (`int`)**


**Field: `errorMessage` (`string`)**


### **StructInfo**

StructInfo holds ast field information for the docs generator.

**Field: `Name` (`string`)**
Name is struct go name.

**Field: `fields` ([[]*FieldInfo](#fieldinfo))**
Fields holds information of the fields, if this object is a struct.

### **Token**

Token holds the values related to authentication tokens.

**Field: `enabled` (`boolean`)**
Enabled enables the token based authentication mode.


Tyk classic API definition: `auth_configs["authToken"].use_standard_auth`.

**Field: `enableClientCertificate` (`boolean`)**
EnableClientCertificate allows to create dynamic keys based on certificates.


Tyk classic API definition: `auth_configs["authToken"].use_certificate`.

**Field: `signatureValidation` ([Signature](#signature))**
Signature holds the configuration for verifying the signature of the token.


Tyk classic API definition: `auth_configs["authToken"].use_certificate`.

### **TransformRequestBody**

TransformRequestBody holds configuration about body request transformations.

**Field: `enabled` (`boolean`)**
Enabled enables transform request body middleware.

**Field: `format` (`string`)**
Format of the request body, xml or json.

**Field: `path` (`string`)**
Path file path for the template.

**Field: `body` (`string`)**
Body base64 encoded representation of the template.

### **TransformRequestMethod**

TransformRequestMethod holds configuration for rewriting request methods.

**Field: `enabled` (`boolean`)**
Enabled enables Method Transform for the given path and method.

**Field: `toMethod` (`string`)**
ToMethod is the http method value to which the method of an incoming request will be transformed.

### **TykExtensionConfigParams**

TykExtensionConfigParams holds the essential configuration required for the Tyk Extension schema.

**Field: `UpstreamURL` (`string`)**


**Field: `ListenPath` (`string`)**


**Field: `CustomDomain` (`string`)**


**Field: `ApiID` (`string`)**


**Field: `Authentication` (`boolean`)**


**Field: `AllowList` (`boolean`)**


**Field: `ValidateRequest` (`boolean`)**


**Field: `MockResponse` (`boolean`)**


### **ValidateRequest**

ValidateRequest holds configuration required for validating requests.

**Field: `enabled` (`boolean`)**
Enabled is a boolean flag, if set to `true`, it enables request validation.

**Field: `errorResponseCode` (`int`)**
ErrorResponseCode is the error code emitted when the request fails validation.
If unset or zero, the response will returned with http status 422 Unprocessable Entity.

### **VirtualEndpoint**

VirtualEndpoint contains virtual endpoint configuration.

**Field: `enabled` (`boolean`)**
Enabled enables virtual endpoint.

**Field: `name` (`string`)**
Name is the name of js function.

**Field: `path` (`string`)**
Path is the path to js file.

**Field: `body` (`string`)**
Body is the js function to execute encoded in base64 format.

**Field: `proxyOnError` (`boolean`)**
ProxyOnError proxies if virtual endpoint errors out.

**Field: `requireSession` (`boolean`)**
RequireSession if enabled passes session to virtual endpoint.

### **XTykDoc**

XTykDoc is a list of information for exported struct type info,
starting from the root struct declaration(XTykGateway).

Type defined as array of `StructInfo` values, see [StructInfo](#structinfo) definition.

