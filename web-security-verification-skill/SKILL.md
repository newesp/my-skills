---
name: web-security-verification
description: Perform focused, non-destructive security verification of a web application or API by combining code inspection with targeted automated tests. Use only in local/test environments or against systems the user is authorized to assess.
---

# Web Security Verification Skill

## Purpose

Verify whether a web application or API is exposed to common web vulnerabilities.

This is not only a static code review. The workflow must:

1. Inspect the relevant code paths.
2. Identify exposed attack surfaces.
3. Add reproducible automated security tests where practical.
4. Execute those tests in a local/test environment.
5. Fix confirmed vulnerabilities.
6. Re-run the focused security tests.
7. Run the full regression suite.

Do not claim a vulnerability is fixed based only on code appearance. Prefer executable verification.

---

## Safety Boundary

Only perform security testing against:

- local development environments;
- isolated test/staging environments;
- mocked external services;
- systems the user explicitly owns or is authorized to assess.

Do not:

- perform destructive testing against production;
- alter or delete production data;
- brute-force real accounts;
- exfiltrate real secrets or personal data;
- attack unrelated third-party services;
- send malicious payloads to production Supabase, payment, email, OAuth, webhook, or other external services.

When production configuration must be reviewed, inspect configuration and code only. Execute attack tests against test equivalents.

---

## Workflow

### 1. Establish Attack Surface

Inspect the application and enumerate relevant entry points:

- web forms;
- query parameters;
- route/path parameters;
- API JSON bodies;
- authentication endpoints;
- admin endpoints;
- search/filter/sort parameters;
- file upload/download endpoints;
- webhook configuration;
- external URL inputs;
- redirects;
- rendered user-controlled content;
- database queries;
- background jobs;
- privileged actions.

Focus on externally controllable inputs and trust-boundary crossings.

---

## 2. SQL Injection

Inspect all database access paths.

Verify:

- ORM/query builder bindings are used;
- raw SQL does not concatenate untrusted input;
- dynamic `ORDER BY`, column names, table names, and filters use allowlists;
- search/filter parameters cannot modify query structure;
- route IDs and API values are bound as parameters.

Test representative inputs against:

- login fields;
- search;
- filters;
- IDs;
- route parameters;
- API JSON;
- report/export queries.

Add automated tests demonstrating that malicious SQL-like input is treated as data rather than executable query syntax.

Do not test against production data.

---

## 3. Cross-Site Scripting (XSS)

Inspect:

- reflected values;
- stored user-controlled fields;
- admin dashboards;
- product/customer/order fields;
- search results;
- error messages;
- templates/components;
- raw HTML rendering;
- JavaScript DOM insertion.

Verify:

- normal template output is escaped;
- raw HTML rendering is justified and sanitized;
- user-controlled strings are not inserted through unsafe HTML APIs;
- stored content cannot become executable script.

Add tests for reflected and stored XSS paths where applicable.

---

## 4. Authentication

Verify:

- protected pages reject unauthenticated users;
- invalid and expired tokens are rejected;
- logout invalidates the authenticated session;
- session IDs are regenerated after login;
- authentication failures do not reveal sensitive account details;
- demo/test authentication cannot accidentally become production authentication;
- OAuth/JWT validation checks required signature and claims.

For JWT-based authentication, verify the implementation validates the expected:

- signature;
- issuer;
- audience;
- expiration;
- subject/user identifier.

---

## 5. Authorization and IDOR

Authentication alone is insufficient.

Test whether users can:

- access admin routes without admin privileges;
- access another user's records by changing an ID;
- modify another user's resource;
- invoke privileged API routes directly;
- bypass UI restrictions by sending requests manually;
- perform destructive operations using a restricted/demo account.

Add explicit positive and negative authorization tests.

Authorization must be enforced server-side.

---

## 6. CSRF

For browser session-based state-changing operations, verify:

- POST/PUT/PATCH/DELETE requests require CSRF protection;
- logout and destructive operations are appropriately protected;
- exempt routes have a documented reason.

Add tests confirming invalid or missing CSRF protection is rejected where applicable.

Do not require CSRF for APIs that use a non-cookie bearer-token model unless the application architecture specifically requires it.

---

## 7. Mass Assignment / Over-posting

Inspect model binding and request handling.

Verify users cannot submit protected fields such as:

- `role`;
- privilege flags;
- ownership IDs;
- internal status fields;
- administrative flags;
- audit fields;
- security configuration.

Use explicit validation/DTO/form-request allowlists or safe model fillable configuration.

Add tests that send unauthorized extra fields and verify they are ignored or rejected.

---

## 8. Input Validation

Test malformed and boundary inputs:

- invalid UUIDs/IDs;
- invalid enum values;
- negative or overflowing numbers;
- excessively long strings;
- empty required values;
- unexpected arrays/objects;
- malformed JSON;
- invalid dates;
- duplicate values where uniqueness matters.

Confirm invalid input produces controlled validation responses rather than server errors or unsafe behavior.

---

## 9. SSRF

If the application accepts external URLs for:

- webhooks;
- imports;
- previews;
- callbacks;
- image fetches;
- crawlers;
- integrations;

verify that untrusted URLs cannot reach forbidden internal resources.

Check protections for:

- localhost;
- loopback ranges;
- private networks;
- link-local addresses;
- cloud metadata endpoints;
- redirect chains;
- alternate IP representations.

Use mocks or isolated test services. Do not probe real internal or third-party infrastructure.

---

## 10. Open Redirect

Inspect redirect/callback/return URL parameters.

Verify redirects are:

- relative; or
- restricted to an explicit allowlist of trusted origins.

Add tests proving untrusted external redirect targets are rejected.

---

## 11. File and Path Security

For upload/download/file access functionality, inspect for:

- path traversal;
- arbitrary file reads;
- arbitrary overwrite;
- dangerous executable uploads;
- extension/MIME mismatch;
- predictable private file URLs;
- missing authorization on downloads.

Test representative traversal values in the isolated test environment.

Verify generated file paths remain inside the intended storage root.

---

## 12. Sensitive Information Exposure

Inspect:

- Git-tracked environment files;
- `.env.example`;
- application logs;
- exception output;
- API responses;
- debug mode;
- stack traces;
- source maps;
- CI logs;
- client-side bundles;
- README/demo instructions.

Look specifically for:

- database passwords;
- private API keys;
- service-role keys;
- signing secrets;
- OAuth client secrets;
- private tokens;
- session secrets.

Public/browser-safe identifiers should not automatically be classified as secrets; evaluate according to the platform's security model.

Never print discovered real secrets into reports or test output.

---

## 13. Session, Cookie, and Security Configuration

Review production configuration for:

- secure cookies under HTTPS;
- HTTP-only session cookies;
- appropriate SameSite behavior;
- session invalidation;
- debug mode disabled;
- trusted proxy configuration;
- rate limiting on authentication and sensitive endpoints.

Also inspect relevant security headers, including where appropriate:

- Content-Security-Policy;
- X-Content-Type-Options;
- Referrer-Policy;
- frame-embedding protection.

Do not add headers blindly if they would break required application behavior. Verify compatibility.

---

## 14. Business Logic Security

Inspect application-specific invariants such as:

- legal order status transitions;
- price/total calculations;
- privilege escalation paths;
- cancellation/refund rules;
- ownership transfer;
- replayable operations;
- duplicate submissions;
- race-sensitive actions.

Security testing must include business rules, not only OWASP-style payloads.

---

## Severity

Classify confirmed findings:

### Critical
Immediate compromise of sensitive data, authentication, authorization, remote code execution, or destructive production behavior.

### Important
Material security weakness with realistic exploitability, privilege bypass, data exposure, or unsafe trust-boundary behavior.

### Moderate
Defense-in-depth issue, limited-impact weakness, or issue requiring significant preconditions.

### Low
Hardening opportunity with limited direct exploitability.

Do not inflate severity without a reproducible path.

---

## Fix Policy

Fix all confirmed Critical and Important findings found within the requested scope.

For every fix:

1. Preserve existing intended behavior.
2. Add or update a regression test.
3. Run the focused test.
4. Run related feature/integration tests.
5. Run the full regression suite.

Avoid broad architecture rewrites when a minimal secure fix is sufficient.

---

## Verification Output

Return a concise report using this format:

```text
Security Verification

Attack Surface Reviewed
- ...

Confirmed Findings
- [Severity] Finding
  - Location:
  - Impact:
  - Reproduction:
  - Fix:
  - Regression test:

Verified Categories
- SQL Injection: pass / findings
- XSS: pass / findings
- Authentication: pass / findings
- Authorization / IDOR: pass / findings
- CSRF: pass / findings
- Mass Assignment: pass / findings
- Input Validation: pass / findings
- SSRF: pass / not applicable / findings
- Open Redirect: pass / not applicable / findings
- File / Path Security: pass / not applicable / findings
- Sensitive Data Exposure: pass / findings
- Session / Security Config: pass / findings
- Business Logic Security: pass / findings

Tests
- Focused security tests:
- Full regression suite:
- CI:

Remaining Risks
- ...
```

Use `not applicable` only after checking that the attack surface truly does not exist.

---

## Completion Criteria

The verification is complete only when:

- relevant attack surfaces have been enumerated;
- relevant code paths have been inspected;
- representative automated attack/regression tests have been added where practical;
- confirmed Critical and Important vulnerabilities have been fixed;
- focused security tests pass;
- the full regression suite passes;
- no production system was destructively tested;
- remaining risks and untested areas are explicitly documented.

Do not report “secure” or “no vulnerabilities” as an absolute claim. Report what was tested, what passed, what was fixed, and what remains outside the verification scope.
