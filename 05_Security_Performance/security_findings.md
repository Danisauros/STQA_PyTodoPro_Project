# Security Findings: PyTodo Pro

**Tester:** Alya Nabihah (22012109)  
**Test Accounts:** `security_user_a` and `security_user_b`  
**Date:** 20 November 2026  
**Environment:** GitHub Codespaces and Local Windows Environment (IntelliJ IDEA PowerShell)  
**Application:** PyTodo Pro (Flask)  
**Testing Method:** Manual API testing using HTTP requests through `curl` and PowerShell

## 2. Test Environment and Methodology

PyTodo Pro was tested using HTTP requests to examine the security of its REST API endpoints. The security testing was conducted in two environments: GitHub Codespaces and a local Windows environment.

The initial tests (SEC-001 to SEC-006) were conducted in GitHub Codespaces, while the subsequent tests (SEC-007 to SEC-011) were performed locally using PowerShell through IntelliJ IDEA. The local Flask application was accessed at `http://127.0.0.1:5000/`.

Two test accounts, `security_user_a` and `security_user_b`, were used to evaluate authentication and authorisation controls. Authenticated requests used session cookies to maintain user sessions, while unauthenticated requests were sent without an authenticated session.

The testing focused on authentication enforcement, cross-user resource access, session handling, input validation, invalid resource requests and HTTP method restrictions. Passwords and session-cookie values are excluded from this report.

A total of 11 security test cases were executed. The results were recorded based on the HTTP status codes and server responses returned by the application.

## 3. Test Findings

### SEC-001: Unauthenticated Access to Task API

| Field | Observed result |
|---|---|
| HTTP method | GET |
| Endpoint | `/api/tasks` |
| Status code | `401 UNAUTHORIZED` |
| Expected behaviour | Unauthenticated users should not be allowed to access task information. |
| Actual result | The server rejected the request and returned HTTP 401. |
| Test result | **Pass** — authentication was correctly enforced. |

**Request:**

```bash
curl -i http://127.0.0.1:5000/api/tasks
```

**Actual response:**

```http
HTTP/1.1 401 UNAUTHORIZED
Content-Type: application/json

{"error":"authentication required"}
```

**Finding:** The task API correctly rejected unauthenticated access. The authentication mechanism prevented users without a valid session from retrieving task information. No defect was observed in this test.

### SEC-002: Unauthenticated Access to Dashboard API

| Field | Observed result |
|---|---|
| HTTP method | GET |
| Endpoint | `/api/dashboard` |
| Status code | `401 UNAUTHORIZED` |
| Expected behaviour | Unauthenticated users should not be allowed to access dashboard information. |
| Actual result | The server rejected the request and returned HTTP 401. |
| Test result | **Pass** — authentication was correctly enforced. |

**Request:**

```bash
curl -i http://127.0.0.1:5000/api/dashboard
```

**Actual response:**

```http
HTTP/1.1 401 UNAUTHORIZED
Content-Type: application/json

{"error":"authentication required"}
```

**Finding:** The dashboard API correctly restricted access to authenticated users. Unauthenticated requests were rejected, preventing unauthorised access to dashboard information.

### SEC-003: Unauthenticated Task Creation

| Field | Observed result |
|---|---|
| HTTP method | POST |
| Endpoint | `/api/tasks` |
| Status code | `401 UNAUTHORIZED` |
| Expected behaviour | Unauthenticated users should not be able to create tasks. |
| Actual result | The server rejected the request and returned HTTP 401. |
| Test result | **Pass** — unauthenticated task creation was prevented. |

**Request:**

```bash
curl -i -X POST http://127.0.0.1:5000/api/tasks \
-H "Content-Type: application/json" \
-d '{"title":"Unauthorised Task","description":"Security test"}'
```

**Actual response:**

```http
HTTP/1.1 401 UNAUTHORIZED
Content-Type: application/json

{"error":"authentication required"}
```

**Finding:** The application prevented unauthenticated users from creating tasks. This indicates that authentication is required before performing task creation operations.

### SEC-004: Cross-User Task Viewing

| Field | Observed result |
|---|---|
| HTTP method | GET |
| Endpoint | `/api/tasks/8` |
| Test scenario | User A attempts to access User B's task. |
| Status code | `404 NOT FOUND` |
| Expected behaviour | User A should not be able to retrieve a task belonging to User B. |
| Actual result | The server returned HTTP 404 and did not provide the requested task. |
| Test result | **Pass** — cross-user task access was restricted. |

**Request:**

```bash
curl -i http://127.0.0.1:5000/api/tasks/8 \
-b /tmp/security_user_a.txt
```

**Actual response:**

```http
HTTP/1.1 404 NOT FOUND
Content-Type: application/json

{"error":"not found"}
```

**Finding:** The application correctly prevented User A from viewing a task belonging to User B. The API returned HTTP 404 instead of exposing the task information. No defect was observed in this test.

### SEC-005: Cross-User Task Updating

| Field | Observed result |
|---|---|
| HTTP method | PUT |
| Endpoint | `/api/tasks/8` |
| Test scenario | User A attempts to update User B's task. |
| Status code | `404 NOT FOUND` |
| Expected behaviour | User A should not be allowed to modify a task belonging to User B. |
| Actual result | The server rejected the request and returned HTTP 404. |
| Test result | **Pass** — cross-user task modification was prevented. |

**Request:**

```bash
curl -i -X PUT http://127.0.0.1:5000/api/tasks/8 \
-b /tmp/security_user_a.txt \
-H "Content-Type: application/json" \
-d '{"title":"Modified by User A"}'
```

**Actual response:**

```http
HTTP/1.1 404 NOT FOUND
Content-Type: application/json

{"error":"not found"}
```

**Finding:** The application correctly restricted task modification based on user ownership. User A could not update User B's task, indicating that the update endpoint performed an ownership check.

### SEC-006: Cross-User Task Deletion

| Field | Observed result |
|---|---|
| HTTP method | DELETE |
| Endpoint | `/api/tasks/8` |
| Test scenario | User A attempts to delete User B's task. |
| Status code | `200 OK` |
| Expected behaviour | User A should not be able to delete a task belonging to User B. The server should reject the request. |
| Actual result | The server returned HTTP 200 and successfully deleted User B's task. |
| Test result | **Fail** — unauthorised task deletion was possible. |

**Request:**

```bash
curl -i -X DELETE http://127.0.0.1:5000/api/tasks/8 \
-b /tmp/security_user_a.txt
```

**Actual response:**

```http
HTTP/1.1 200 OK
Content-Type: application/json

{"result":"ok"}
```

**Finding:** The application allowed User A to delete a task belonging to User B. A subsequent request by User B to retrieve the same task returned HTTP 404, confirming that the task had been deleted.

This indicates a Broken Object Level Authorisation (BOLA) vulnerability because the DELETE endpoint did not properly verify task ownership before performing the deletion.

**Identified defect:** SEC-DEF-001 — Unauthorised deletion of another user's task.

### SEC-007: Logout and Session Handling

| Field | Observed result |
|---|---|
| HTTP methods | GET |
| Endpoints | `/api/login`, `/api/tasks`, `/logout` |
| Status codes | `200 OK`, `302 FOUND`, `401 UNAUTHORIZED` |
| Expected behaviour | An authenticated user should be able to access protected APIs before logout, but access should be rejected after logout. |
| Actual result | The task API returned HTTP 200 before logout. The logout endpoint returned HTTP 302, and subsequent access to the task API returned HTTP 401. |
| Test result | **Pass** — the session was invalidated after logout. |

**Request sequence:**

```powershell
# Login
Invoke-RestMethod -Uri "http://127.0.0.1:5000/api/login" `
-Method POST -ContentType "application/json" -Body $loginBody `
-WebSession $sessionA

# Access tasks before logout
Invoke-WebRequest -Uri "http://127.0.0.1:5000/api/tasks" `
-WebSession $sessionA -UseBasicParsing

# Logout
Invoke-WebRequest -Uri "http://127.0.0.1:5000/logout" `
-WebSession $sessionA -UseBasicParsing

# Access tasks after logout
Invoke-WebRequest -Uri "http://127.0.0.1:5000/api/tasks" `
-WebSession $sessionA -UseBasicParsing
```

**Actual response summary:**

| Operation | HTTP status |
|---|---|
| Login | `200 OK` |
| Access tasks before logout | `200 OK` |
| Logout | `302 FOUND` |
| Access tasks after logout | `401 UNAUTHORIZED` |

**Finding:** The application successfully invalidated the authenticated session after logout. Subsequent requests to the protected task API were rejected, indicating that the logout mechanism worked as expected.

### SEC-008: Invalid Input Data Type for Task Title

| Field | Observed result |
|---|---|
| HTTP method | POST |
| Endpoint | `/api/tasks` |
| Test data | Numeric title (`123`) |
| Status code | `500 INTERNAL SERVER ERROR` |
| Expected behaviour | The server should validate the title data type and return HTTP 400 for invalid input. |
| Actual result | The server returned HTTP 500 Internal Server Error. |
| Test result | **Fail** — invalid input caused a server-side error. |

**Request:**

```powershell
$body = @{
    title = 123
    description = "Testing invalid title data type"
} | ConvertTo-Json

Invoke-WebRequest -Uri "http://127.0.0.1:5000/api/tasks" `
-Method POST -ContentType "application/json" `
-Body $body -WebSession $sessionA -UseBasicParsing
```

**Actual response:**

```http
HTTP/1.1 500 INTERNAL SERVER ERROR
```

**Finding:** The application failed to handle a numeric value submitted as a task title. Instead of returning a controlled validation response, the server generated an internal error. This suggests that the input validation does not adequately check the data type before processing the title.

**Identified defect:** SEC-DEF-002 — Invalid task title data type causes an Internal Server Error.

### SEC-009: Missing Task Title

| Field | Observed result |
|---|---|
| HTTP method | POST |
| Endpoint | `/api/tasks` |
| Test data | Description provided without a title. |
| Status code | `400 BAD REQUEST` |
| Expected behaviour | The server should reject task creation when the required title field is missing. |
| Actual result | The server returned HTTP 400 Bad Request. |
| Test result | **Pass** — missing required input was handled correctly. |

**Request:**

```powershell
$body = @{
    description = "Testing missing task title"
} | ConvertTo-Json

Invoke-WebRequest -Uri "http://127.0.0.1:5000/api/tasks" `
-Method POST -ContentType "application/json" `
-Body $body -WebSession $sessionA -UseBasicParsing
```

**Actual response:**

```http
HTTP/1.1 400 BAD REQUEST
```

**Finding:** The application correctly rejected a task creation request without a title. The server returned HTTP 400 instead of creating an incomplete task.

### SEC-010: Request for an Invalid Task ID

| Field | Observed result |
|---|---|
| HTTP method | GET |
| Endpoint | `/api/tasks/999999` |
| Test data | Non-existent task ID (`999999`). |
| Status code | `404 NOT FOUND` |
| Expected behaviour | The server should return HTTP 404 when the requested task does not exist. |
| Actual result | The server returned HTTP 404 Not Found. |
| Test result | **Pass** — invalid resource requests were handled correctly. |

**Request:**

```powershell
Invoke-WebRequest -Uri "http://127.0.0.1:5000/api/tasks/999999" `
-WebSession $sessionA -UseBasicParsing
```

**Actual response:**

```http
HTTP/1.1 404 NOT FOUND
```

**Finding:** The application correctly returned HTTP 404 when a non-existent task was requested. No unexpected server error was observed.

### SEC-011: Invalid HTTP Method

| Field | Observed result |
|---|---|
| HTTP method | PUT |
| Endpoint | `/api/categories` |
| Status code | `405 METHOD NOT ALLOWED` |
| Expected behaviour | The server should reject HTTP methods that are not supported by the endpoint. |
| Actual result | The server returned HTTP 405 Method Not Allowed. |
| Test result | **Pass** — unsupported HTTP methods were rejected. |

**Request:**

```powershell
Invoke-WebRequest -Uri "http://127.0.0.1:5000/api/categories" `
-Method PUT -ContentType "application/json" `
-Body '{"name":"Test Category"}' -UseBasicParsing
```

**Actual response:**

```http
HTTP/1.1 405 METHOD NOT ALLOWED
```

**Finding:** The application correctly rejected an unsupported HTTP method for the categories endpoint. This indicates that the endpoint restricts requests to its configured HTTP methods.

## 4. Security Testing Summary

The security testing consisted of 11 test cases covering authentication, authorisation, session management, input validation and HTTP method restrictions.

| Test result | Number of cases |
|---|---:|
| Pass | 9 |
| Fail | 2 |
| Total | 11 |
| Pass rate | 81.8% |

Two defects were identified during the testing:

| Defect ID | Related test | Description | Severity | Priority | Status |
|---|---|---|---|---|---|
| SEC-DEF-001 | SEC-006 | User A can delete another user's task without ownership verification. | High | High | Open |
| SEC-DEF-002 | SEC-008 | Numeric task title causes HTTP 500 instead of a validation response. | Medium | Medium | Open |

## 5. STRIDE Threat Analysis

The STRIDE model was used to categorise potential security threats affecting the application.

| STRIDE category | Identified threat / observation | Related test | Risk |
|---|---|---|---|
| Spoofing | Unauthenticated users may attempt to access protected API endpoints. Authentication checks rejected the tested unauthenticated requests. | SEC-001, SEC-002, SEC-003, SEC-007 | Medium |
| Tampering | A user can delete another user's task due to missing ownership verification in the DELETE endpoint. | SEC-005, SEC-006 | High |
| Repudiation | Audit logging and the ability to trace user actions were not evaluated during this testing. | Not tested | Medium |
| Information Disclosure | Cross-user task retrieval was rejected by the API during the tested request. | SEC-004 | High |
| Denial of Service | Application behaviour under high request loads will be evaluated separately during performance testing. | Performance testing pending | Medium |
| Elevation of Privilege | User A was able to perform an unauthorised deletion on User B's task. | SEC-004, SEC-005, SEC-006 | High |

## 6. Recommendations

Based on the security testing results, the following improvements are recommended:

1. **Implement ownership verification for task deletion.** The DELETE endpoint should verify that the requested task belongs to the currently authenticated user before deleting it.
2. **Improve input validation.** The application should validate the data type of task titles and return HTTP 400 for invalid values instead of generating an internal server error.
3. **Maintain authentication enforcement.** Protected API endpoints should continue requiring valid authenticated sessions.
4. **Strengthen session management.** The application should continue invalidating sessions after logout and ensure protected resources cannot be accessed using expired or invalid sessions.
5. **Introduce audit logging.** Important user actions, particularly task creation, modification and deletion, should be recorded to support accountability and investigation.
6. **Conduct further performance testing.** The application should be evaluated under controlled workloads to identify potential availability and resource-handling issues.

## 7. Limitations

The security testing focused on selected API endpoints and common application-level security risks. It was not a comprehensive penetration test or a full security audit.

The 11 test cases were executed across two different environments: GitHub Codespaces and a local Windows environment. Therefore, the results represent findings from both testing sessions rather than a single execution in one environment.

Other areas, including encryption, password-storage security, audit logging, automated vulnerability scanning and large-scale denial-of-service resistance, were not comprehensively assessed.

## 8. Conclusion

The security testing of PyTodo Pro achieved an overall pass rate of 81.8%, with 9 out of 11 test cases passing. The application demonstrated appropriate behaviour in several areas, including authentication enforcement, cross-user task viewing and updating, session invalidation, missing input handling and unsupported HTTP method rejection.

However, two defects were identified. The first was a high-severity authorisation vulnerability that allowed a user to delete another user's task. The second was an input validation issue that caused an internal server error when a numeric value was submitted as a task title.

These findings indicate that although the application implements several basic security controls, improvements are required in resource ownership verification and input validation to strengthen its overall security.
