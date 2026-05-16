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

## Service and gateway structure

- Treat services and microservices as APIs exposed through the API Gateway.
- Use the API path structure so the gateway can route requests by `apiName`.

## Endpoint path standard

- Standardize endpoint paths as `/{apiName}/{accessType}/{apiVersion}/{apiSpecificOperations}`.

### Path parts

- `apiName`: API name for the service or domain.
- Use a single domain name when the service exposes one domain, such as `/blood`, `/orders`, `/wearables`, or `/notifications`.
- Use a service-plus-domain prefix when one service exposes multiple API domains, such as `/myplan/plan` or `/myplan/pro-tips`.
- `accessType`: one of `customer`, `admin`, `service`, `physician`, `coach`, `partner`, or `public`.
- `apiVersion`: version segment such as `v1`, `v2`, or `v3`.
- `apiSpecificOperations`: the remaining domain-specific resource or operation path.

## OpenAPI standard

- Each service must provide its own OpenAPI specification.
- Include all available security schemes in the specification.
- Name each security scheme exactly after the access type it represents, such as `customer`, `admin`, `service`, `physician`, `coach`, `partner`, or `public`.
- Include all available endpoints and their parameters in the specification.
- Include the required security schemes on each endpoint.

## Working rules

- For new APIs and changed APIs, follow the standardized path structure directly.
- For reviews, flag deviations in path shape, `apiName`, access type, version segment, or security scheme naming.
- For documentation of existing APIs, preserve the implemented endpoint structure unless the user explicitly asks for redesign or normalization.
- If an existing implementation does not follow this standard, document the implemented current state accurately and separately advise the standard-compliant path or OpenAPI shape when relevant.
