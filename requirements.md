# Requirements - DEMO-6

## Story Reference
- Jira: DEMO-6
- Title: User Login with Email and Password
- Type: Story
- Priority: Medium

## Business Goal
Enable registered users to securely sign in with email and password to access protected account resources.

## Scope
In scope:
- API login endpoint behavior and payload validation.
- UI login interaction behavior (form input, validation feedback, and response handling).
- Authentication token issuance on successful login.
- Generic authentication error handling and lockout handling.

Out of scope:
- User registration.
- Password reset and recovery flows.
- Multi-factor authentication.
- Social/OAuth sign-in.

## Constraints
- Passwords must be securely stored and validated using hashing.
- Login API performance target: p95 < 2 seconds at 100 RPS in staging over a 15-minute run.
- Account lockout must occur after 5 failed login attempts.

## Functional Requirements
1. The system shall provide login using `email` and `password`.
2. The system shall validate input format and required fields before attempting authentication.
3. The system shall accept only valid email format.
4. The system shall enforce password minimum length of 8 characters.
5. The system shall authenticate only when credentials match an existing registered user.
6. The system shall return HTTP 200 with payload `{token, expiresIn}` for successful login.
7. The system shall issue JWT access token only.
8. The system shall set token expiry to 1 hour.
9. The system shall return HTTP 401 with payload `{errorCode, message}` for invalid credentials.
10. The system shall return HTTP 400 with payload `{errorCode, message, fieldErrors}` for validation failures.
11. The system shall return HTTP 423 for locked accounts.
12. The system shall lock an account after 5 failed login attempts.
13. The system shall support concurrent sessions across devices.
14. The UI shall show field-level validation feedback for invalid inputs.
15. The UI shall present generic login failure messaging without revealing whether email or password is incorrect.

## Non-Functional Requirements
1. Performance: Login API must satisfy p95 < 2 seconds at 100 RPS in staging measured over 15 minutes.
2. Security: Passwords must never be stored, returned, or logged in plaintext.
3. Security: Authentication failure responses must prevent account enumeration.
4. Reliability: Error response schema must be deterministic and consistent across failures.
5. Usability: UI validation and failure messages must be clear and actionable without exposing sensitive details.
6. Observability: Authentication attempts, failures, and lockouts should be logged with masked identifiers and no credential leakage.

## Validation Rules
1. `email` is mandatory.
2. `email` must follow valid email format.
3. `password` is mandatory.
4. `password` must be at least 8 characters.
5. Requests with missing or invalid field types must be rejected with HTTP 400.
6. Invalid credentials must return HTTP 401.
7. Locked accounts must return HTTP 423.

## Error Handling Rules
1. Credential failure response must not reveal whether email or password is incorrect.
2. Validation failures must return structured `fieldErrors` where safe.
3. Account lockout failures must return a lockout-specific error code/message with HTTP 423.
4. Unexpected server errors must return a generic error message without stack traces or internals.
5. All error responses must conform to defined schema fields for their status class.

## Assumptions
1. Login uses JWT access token only.
2. Access token expiration is 1 hour.
3. Concurrent sessions are allowed.
4. Account lockout threshold is exactly 5 failed attempts.
5. API and UI flows are both part of this story implementation scope.
