# Security and Performance Testing — PyTodo Pro

## 1. Overview

This folder contains the security and performance testing documentation for the PyTodo Pro Flask application. The testing was conducted to evaluate the application's API security controls, identify potential vulnerabilities, and assess its basic response performance.

## 2. Folder Contents

| File | Description |
|---|---|
| `security_test_cases.csv` | Records the 11 security test cases, expected results, actual results, and test status. |
| `stride_analysis.csv` | Analyses potential threats using the STRIDE threat-modelling framework. |
| `defect_log.csv` | Records identified security defects, their severity, priority, and status. |
| `security_findings.md` | Provides detailed security testing methodology, results, findings, and recommendations. |
| `performance_test_results.csv` | Summarises measurements from the four performance test cases. |
| `performance_findings.md` | Documents performance testing methodology, measurements, analysis, and recommendations. |

## 3. Testing Summary

### Security Testing

- Total test cases: 11
- Passed: 9
- Failed: 2
- Pass rate: 81.8%
- Identified defects: 2

The two failed security tests identified unauthorised deletion of another user's task and an internal server error when a numeric value was submitted as a task title.

Security tests SEC-001 to SEC-006 were conducted in GitHub Codespaces, while SEC-007 to SEC-011 were conducted in the local Windows environment.

### Performance Testing

- Total test cases: 4
- Passed: 4
- Failed: 0
- Pass rate: 100%
- Total successful requests: 80
- Recorded request error rate: 0%

Performance testing covered sequential requests to the task, dashboard, and category APIs, as well as concurrent requests to the task API.

## 4. Testing Environment

The security and performance tests were conducted using the PyTodo Pro Flask application.

The performance tests and security tests SEC-007 to SEC-011 used the local Windows environment with IntelliJ IDEA PowerShell. The earlier security tests SEC-001 to SEC-006 were conducted in GitHub Codespaces.

## 5. Important Notes

- Security and performance results are documented separately because they evaluate different quality characteristics.
- The initial unsuccessful setup attempt for PERF-004 was excluded. Only the corrected run with 20 successful authenticated requests is included in the final results.
- No screenshots or separate evidence folder are included.
- The identified security defects remain open and should be reviewed before production deployment.

## 6. Conclusion

The testing identified two security defects that require attention. The performance tests showed successful request handling under the limited workloads applied. These results provide a baseline assessment of the application's security and performance, but additional testing would be required before production deployment.
