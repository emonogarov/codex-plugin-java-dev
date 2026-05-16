---
name: api-endpoint-standards
description: Canonical HTTP API endpoint and OpenAPI standards for services, gateway routing, code, and documentation across the system. Use when designing, implementing, reviewing, or documenting routes, controllers, API Gateway mappings, access types, versioning, or OpenAPI security schemes.
---

# API Endpoint Standards

## Scope

- Treat this skill as the canonical API endpoint standard for the system.
- Apply these rules to documentation, code, gateway routing, API contracts, and reviews.
- Do not assume existing implementations or existing docs already follow these standards.
- If an existing artifact conflicts with these standards, call out the mismatch explicitly and advise the standard-compliant form for new or changed work.
- If the task is to document current implemented behavior, keep the documented current state accurate and separately note the standard deviation when relevant.

## Access types

- `customer`
- `admin`
- `service`
- `physician`
- `coach`
- `partner`
- `public`
- `accessType` represents the intended caller class and exposure boundary for the endpoint.
- `accessType` does not represent a fine-grained RBAC model.
- The same business capability may be exposed under different access types when the caller boundary or contract differs.

## Service and gateway structure

- Treat services and microservices as APIs exposed through the API Gateway.
- Use the API path structure so the gateway can route requests by the stable path prefix before `accessType`.
- The routing prefix may be `/{domain}`, `/{service}/{domain}`, or `/{service}` when a monolith is routed by service at the gateway layer.

## Endpoint path standard

- For single-domain services, standardize endpoint paths as `/{domain}/{accessType}/{apiVersion}/{endpointPath}`.
- For multi-domain services or monolith-style APIs, standardize endpoint paths as `/{service}/{domain}/{accessType}/{apiVersion}/{endpointPath}`.

### Path parts

- `service`: service name. Omit this segment when the API exposes a single domain and the service name would just repeat the domain name.
- `domain`: API domain name.
- Use a single domain name when the service exposes one domain, such as `/blood`, `/orders`, `/wearables`, or `/notifications`.
- Use a service-plus-domain prefix when one service exposes multiple API domains, such as `/myplan/plan` or `/myplan/pro-tips`.
- `accessType`: one of `customer`, `admin`, `service`, `physician`, `coach`, `partner`, or `public`.
- Use `public` in the path segment the same way as other access types when the endpoint is unauthenticated.
- `apiVersion`: version segment such as `v1`, `v2`, or `v3`.

### API versioning rules

#### Scope

- `apiVersion` is a mandatory major version segment in the path, such as `v1`, `v2`, or `v3`.
- `apiVersion` versions the whole API surface under the same API prefix, not an individual endpoint.
- Do not mix endpoints with incompatible contracts under the same API major version.

#### Versioning trigger

- Introduce a new API major version when a breaking change is introduced to any endpoint within the API.
- Increment the API major version when a breaking change is introduced.
- Do not version only the changed route when the API contract becomes incompatible.

#### Lifecycle

- Keep all endpoints within the same API version backward compatible for clients using that version.
- Multiple API major versions may coexist during migration.
- When a new API major version is introduced, keep the deprecated previous API version available for a defined backward-compatibility period.
- Define and communicate the backward-compatibility period explicitly before removing a deprecated API version.
- New development should target the latest supported API major version.
- Deprecate an API version before removal whenever feasible.

### Compatibility rules

#### Non-breaking changes

- Adding optional request fields.
- Adding optional query parameters.
- Adding optional response fields.
- Adding new endpoints under an existing API version.
- Applying bug fixes or internal implementation changes that do not change the external API contract.

#### Breaking changes

- Removing fields.
- Renaming fields.
- Changing field types or formats incompatibly.
- Making an optional request field or parameter required.
- Removing response fields.
- Changing response shape incompatibly.
- Changing the meaning of an existing field.
- Changing status code behavior or error-response semantics incompatibly.
- Changing the required access type or authentication requirements for an existing endpoint.
- Changing path structure, path parameter meaning, or endpoint semantics incompatibly.
- Changing an endpoint from synchronous behavior to asynchronous contract, or vice versa, when clients must adapt.
- Adding new enum values unless clients are explicitly expected to tolerate unknown values.

### Endpoint path

- `endpointPath`: the remaining domain-specific resource path.
- Prefer REST-style resource-oriented paths.
- Use query parameters for standard filtering, sorting, and pagination on resource collections.
- For non-standard CRUD operations, use a sub-resource pattern such as `/products/search` when the operation needs a non-trivial request body or does not fit simple query semantics.

Examples:

- Resource-oriented: `/orders/customer/v1/orders/{orderId}`
- Resource-oriented: `/myplan/plan/customer/v1/plans/{planId}/recommendations`
- Resource-oriented: `/myplan/plan/customer/v1/plans`
- Sub-resource pattern: `/store/service/v1/products/search`
- Sub-resource pattern: `/blood/customer/v1/results/compare`

## OpenAPI standard

- Each service must provide its own OpenAPI specification.
- Define every security scheme used by at least one endpoint in the OpenAPI specification.
- Do not include unused security schemes.
- Name each security scheme exactly after the access type it represents, such as `customer`, `admin`, `service`, `physician`, `coach`, or `partner`.
- Include all available endpoints and their parameters in the specification.
- Document the relevant success and error responses for each endpoint.
- Include the required security schemes on each endpoint.
- For `public` endpoints, do not define or apply an authentication security scheme.

## Working rules

- For new APIs and changed APIs, follow the standardized path structure directly.
- For reviews, flag deviations in path shape, routing prefix, access type, version segment, or security scheme naming.
- For documentation of existing APIs, preserve the implemented endpoint structure unless the user explicitly asks for redesign or normalization.
- If an existing implementation does not follow this standard, document the implemented current state accurately and separately advise the standard-compliant path or OpenAPI shape when relevant.
