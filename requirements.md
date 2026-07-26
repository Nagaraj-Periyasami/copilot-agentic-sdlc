# Requirements - DEMO-6 User Login with Email and Password

## Story Reference
- Jira ID: DEMO-6
- Title: User Login with Email and Password
- Goal: Allow registered users to authenticate with email and password and securely access protected resources.

## Scope
### In Scope
- Email/password-based login for registered users.
- Credential validation and authentication.
- Authentication token issuance on successful login.
- Generic error handling for invalid login attempts.
- Basic security controls for brute-force resistance.

### Out of Scope
- User registration (signup).
- Forgot password flow.
- Password reset flow.
- MFA enrollment and verification.

## Functional Requirements
1. The system shall expose a REST endpoint for login at /api/v1/auth/login.
2. The system shall accept login requests containing email and password fields.
3. The system shall validate request payload format before authentication.
4. The system shall authenticate only registered users with valid credentials.
5. The system shall return a success response containing a JWT access token when authentication succeeds.
6. The system shall return a generic authentication error for failed login attempts without disclosing whether email or password was incorrect.
7. The system shall track consecutive failed login attempts per account.
8. The system shall lock an account after 5 consecutive failed login attempts.
9. The system shall provide a consistent, standardized error response format and status codes.
10. The system shall securely verify passwords against hashed stored credentials.

## Non-Functional Requirements
1. Performance: Login API response time shall meet p95 <= 2 seconds under agreed load conditions.
2. Security: Passwords shall be stored using a strong one-way hashing algorithm with salt (for example, Argon2id, bcrypt, or PBKDF2).
3. Security: Authentication and token issuance shall occur only over HTTPS.
4. Reliability: The login service shall handle malformed and empty inputs gracefully without service crashes.
5. Observability: Failed and successful login attempts shall be logged with traceable metadata, excluding sensitive fields (no plain-text passwords).

## Validation Rules
1. Email is required.
2. Email must follow a valid email format.
3. Password is required.
4. Password must be at least 8 characters.
5. Requests missing required fields shall be rejected before authentication logic runs.
6. Account lockout is triggered after 5 consecutive failed attempts.
7. Login attempts for locked accounts shall be rejected with a generic authentication/lockout-safe message.

## Error Handling Rules
1. Invalid or missing input fields shall return HTTP 400 with standardized validation error codes.
2. Invalid credentials shall return HTTP 401 with a generic message (for example, "Invalid email or password").
3. Locked account attempts shall return HTTP 423 (or project-standard equivalent) with a non-revealing lockout-safe message.
4. Unexpected server failures shall return HTTP 500 with a standardized internal error code and no sensitive detail leakage.
5. Error responses shall include a correlation/request ID for debugging and support.
6. No error response shall include secrets, password content, hash values, or stack traces.

## Assumptions
1. The user account already exists and is active unless explicitly marked otherwise.
2. JWT signing key management and token verification infrastructure already exist in the platform.
3. Token expiration, audience, and claims schema will follow existing platform authentication standards.
4. "Agreed load" for the p95 target will be defined by QA/performance stakeholders.
5. Lockout release policy (manual unlock vs time-based unlock) is governed by existing platform policy unless separately defined.

## Open Items to Confirm
1. Exact JWT claims and expiration policy.
2. Lockout release behavior and duration.
3. Final HTTP code choice for locked accounts if platform standards differ from HTTP 423.
4. Exact error code catalog for validation and authentication failures.
