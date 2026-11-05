# Security and Performance Testing

## 1. Overview

This folder contains the security and performance testing activities conducted for the PyTodo-Pro web application as part of the Software Testing and Quality Assurance (STQA) group project.

The testing focuses on evaluating the application's authentication, authorisation, input validation, data protection, error handling, and response performance.

The main objectives are to:

- Verify that unauthenticated users cannot access protected resources.
- Verify that users can only access and modify their own tasks.
- Identify potential security vulnerabilities in API endpoints.
- Evaluate how the application handles invalid or unexpected input.
- Measure API response times under controlled testing conditions.
- Identify defects and recommend improvements.

## 2. Folder Structure

```text
05_Security_Performance/
│
├── README.md
├── performance_findings.md
├── security_test_cases.csv
├── stride_analysis.csv
│
└── evidence/
    ├── security/
    └── performance/
```

## 3. Security Testing

Security testing was performed against the application's REST API using HTTP requests.

The tests covered:

- Authentication and access control.
- Unauthorised access to protected endpoints.
- Cross-user task access.
- Cross-user task modification.
- Cross-user task deletion.

Two temporary test accounts were used to evaluate whether the application properly enforces ownership restrictions between users.

### Security Test Summary

| Test ID | Test Area | Result |
|---|---|---|
| SEC-001 | Unauthenticated task-list access | Pass |
| SEC-002 | Unauthenticated dashboard access | Pass |
| SEC-003 | Unauthenticated task creation | Pass |
| SEC-004 | Cross-user task viewing | Pass |
| SEC-005 | Cross-user task modification | Pass |
| SEC-006 | Cross-user task deletion | Fail |

**Current result: 5 passed and 1 failed.**

### Identified Security Defect

The testing identified a Broken Object Level Authorisation (BOLA) vulnerability in the DELETE task API endpoint.

The endpoint allows an authenticated user to delete another user's task by providing its task ID.

The source code performs deletion using the task ID without checking whether the task belongs to the currently authenticated user.

This may result in unauthorised deletion and loss of user data.

**Defect ID:** SEC-DEF-001  
**Severity:** High  
**Priority:** High  
**Status:** Open

Refer to the security test cases and STRIDE analysis for further details.

## 4. Performance Testing

Performance testing will evaluate the response time and stability of selected API endpoints under controlled conditions.

The following measurements will be recorded:

- Minimum response time.
- Maximum response time.
- Average response time.
- Median response time.
- 95th percentile response time, where available.
- Successful and failed requests.

Performance results will be documented in `performance_findings.md`.

The tests are conducted in a local development environment. Therefore, the results represent a baseline and should not be treated as production performance measurements.

## 5. STRIDE Threat Analysis

The STRIDE framework is used to identify potential security threats affecting the application.

The six STRIDE categories are:

1. **Spoofing:** Impersonating another user.
2. **Tampering:** Unauthorised modification or deletion of data.
3. **Repudiation:** Denying an action without sufficient evidence or audit records.
4. **Information Disclosure:** Exposing confidential information.
5. **Denial of Service:** Making the application unavailable through excessive or malicious requests.
6. **Elevation of Privilege:** Gaining access or permissions beyond those authorised.

The identified threats, existing controls, testing evidence, and recommendations are recorded in `stride_analysis.csv`.

## 6. Testing Limitations

- Testing was conducted in a local development environment.
- The results depend on the available test accounts and test data.
- Performance measurements may differ from production deployment.
- Only selected security scenarios and API endpoints were evaluated.
- Some potential threats require additional testing before their existence can be confirmed.

## 7. Recommendations

Based on the current findings, the following improvements are recommended:

1. Enforce user ownership checks for every task-related API operation.
2. Validate request data and data types on the server side.
3. Return appropriate HTTP status codes for invalid requests.
4. Avoid exposing detailed internal error messages.
5. Implement appropriate logging and monitoring.
6. Conduct further performance testing under different workloads.
7. Disable Flask debug mode in production environments.

## 8. Conclusion

The security testing confirmed that the application correctly restricts unauthenticated access and prevents users from viewing or modifying another user's task through the tested GET and PUT endpoints.

However, the DELETE endpoint contains an authorisation weakness that allows cross-user task deletion. This issue should be addressed before the application is considered ready for deployment.

Further performance and security testing is required to evaluate the application's overall quality and reliability.
