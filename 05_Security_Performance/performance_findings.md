# Performance Findings: PyTodo Pro

**Tester:** Alya Nabihah (22012109)  
**Date:** 20 November 2026  
**Environment:** Local Windows environment, IntelliJ IDEA PowerShell, Python 3.10.11, Werkzeug 3.1.9  
**Application:** PyTodo Pro (Flask)  
**Testing Method:** Manual HTTP performance testing using PowerShell  
**Test Account:** security_user_a  

## 1. Introduction

Performance testing was conducted to evaluate the responsiveness and basic request-handling capability of the PyTodo Pro web application. The testing focused on measuring response times for selected API endpoints under sequential and concurrent request conditions.

Four test cases were performed to assess the performance of the task, dashboard, and category APIs. The measurements included minimum, average, median, 95th percentile (P95), and maximum response times, together with throughput and error rate where available.

## 2. Test Environment and Methodology

### 2.1 Test Environment

| Item | Description |
|---|---|
| Application | PyTodo Pro |
| Framework | Flask |
| Programming Language | Python 3.10.11 |
| Web Server | Werkzeug 3.1.9 |
| Operating Environment | Local Windows environment |
| Testing Tool | IntelliJ IDEA PowerShell |
| Test Account | security_user_a |
| Base URL | http://127.0.0.1:5000 |

### 2.2 Testing Methodology

Performance testing was conducted using authenticated HTTP GET requests to the application's API endpoints.

Two testing approaches were applied:

- **Sequential testing:** 20 requests were sent individually to each selected endpoint to measure normal request-response performance.
- **Concurrent testing:** 20 requests were distributed across five concurrent workers, with each worker sending four requests to the task API.

The response time of each request was recorded in milliseconds. The results were analysed using minimum, average, median, P95, and maximum response times. Throughput was calculated using the total number of successful requests divided by the measured total test duration where available. Error rate was calculated based on failed requests.

## 3. Performance Test Findings

### PERF-001: Task API Response Time

| Field | Description |
|---|---|
| Test ID | PERF-001 |
| Test Scenario | Sequential task API requests |
| Endpoint | GET /api/tasks |
| Test Load | 20 sequential requests |
| Expected Result | All requests should return HTTP 200 successfully with reasonable response times. |
| Actual Result | All 20 requests returned HTTP 200 successfully. |
| Status | PASS |

**Performance Measurements**

| Metric | Result |
|---|---:|
| Successful Requests | 20 |
| Failed Requests | 0 |
| Minimum Response Time | 29.19 ms |
| Average Response Time | 36.92 ms |
| Median Response Time | 34.80 ms |
| P95 Response Time | 48.33 ms |
| Maximum Response Time | 68.34 ms |
| Error Rate | 0% |
| Throughput | Not recorded |

**Finding:**

The task API successfully handled all 20 sequential requests without errors. The average response time was 36.92 ms, while the P95 response time was 48.33 ms. This indicates that the endpoint responded consistently during the tested sequential workload. Throughput was not recorded because the total wall-clock test duration was not captured.

### PERF-002: Dashboard API Response Time

| Field | Description |
|---|---|
| Test ID | PERF-002 |
| Test Scenario | Sequential dashboard API requests |
| Endpoint | GET /api/dashboard |
| Test Load | 20 sequential requests |
| Expected Result | All requests should return HTTP 200 successfully with reasonable response times. |
| Actual Result | All 20 requests returned HTTP 200 successfully. |
| Status | PASS |

**Performance Measurements**

| Metric | Result |
|---|---:|
| Successful Requests | 20 |
| Failed Requests | 0 |
| Minimum Response Time | 28.60 ms |
| Average Response Time | 31.63 ms |
| Median Response Time | 30.11 ms |
| P95 Response Time | 37.58 ms |
| Maximum Response Time | 45.99 ms |
| Total Test Duration | 0.73 seconds |
| Throughput | 27.44 requests/second |
| Error Rate | 0% |

**Finding:**

The dashboard API completed all 20 requests successfully. It recorded an average response time of 31.63 ms and a P95 response time of 37.58 ms. The results indicate that the dashboard endpoint responded consistently under the tested sequential workload.

### PERF-003: Category API Response Time

| Field | Description |
|---|---|
| Test ID | PERF-003 |
| Test Scenario | Sequential category API requests |
| Endpoint | GET /api/categories |
| Test Load | 20 sequential requests |
| Expected Result | All requests should return HTTP 200 successfully with reasonable response times. |
| Actual Result | All 20 requests returned HTTP 200 successfully. |
| Status | PASS |

**Performance Measurements**

| Metric | Result |
|---|---:|
| Successful Requests | 20 |
| Failed Requests | 0 |
| Minimum Response Time | 30.60 ms |
| Average Response Time | 36.37 ms |
| Median Response Time | 33.95 ms |
| P95 Response Time | 38.07 ms |
| Maximum Response Time | 83.69 ms |
| Total Test Duration | 0.80 seconds |
| Throughput | 25.10 requests/second |
| Error Rate | 0% |

**Finding:**

The category API successfully processed all 20 sequential requests without errors. The average response time was 36.37 ms, with a P95 response time of 38.07 ms. Although one request recorded a higher maximum response time of 83.69 ms, the remaining results indicate generally consistent response performance.

### PERF-004: Concurrent Task API Requests

| Field | Description |
|---|---|
| Test ID | PERF-004 |
| Test Scenario | Concurrent task API requests |
| Endpoint | GET /api/tasks |
| Test Load | 5 concurrent workers × 4 requests each |
| Total Requests | 20 |
| Expected Result | The application should handle concurrent authenticated requests successfully without request failures. |
| Actual Result | All 20 requests returned HTTP 200 successfully. |
| Status | PASS |

**Performance Measurements**

| Metric | Result |
|---|---:|
| Successful Requests | 20 |
| Failed Requests | 0 |
| Minimum Response Time | 8.95 ms |
| Average Response Time | 33.67 ms |
| Median Response Time | 10.36 ms |
| P95 Response Time | 110.38 ms |
| Maximum Response Time | 125.04 ms |
| Total Test Duration | 2.62 seconds |
| Throughput | 7.63 requests/second |
| Error Rate | 0% |

**Finding:**

The application successfully handled all 20 concurrent requests across five workers without returning any failed responses. However, the P95 response time increased to 110.38 ms, indicating greater response-time variation under concurrent activity. The measured throughput was 7.63 requests per second, including the overhead of starting and managing PowerShell background jobs. Therefore, this result represents the performance of the complete test setup rather than the application's maximum server capacity.

## 4. Performance Testing Summary

| Test ID | Endpoint | Test Load | Average (ms) | Median (ms) | P95 (ms) | Maximum (ms) | Throughput | Error Rate | Status |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| PERF-001 | /api/tasks | 20 sequential | 36.92 | 34.80 | 48.33 | 68.34 | Not recorded | 0% | PASS |
| PERF-002 | /api/dashboard | 20 sequential | 31.63 | 30.11 | 37.58 | 45.99 | 27.44 req/s | 0% | PASS |
| PERF-003 | /api/categories | 20 sequential | 36.37 | 33.95 | 38.07 | 83.69 | 25.10 req/s | 0% | PASS |
| PERF-004 | /api/tasks | 5 workers × 4 requests | 33.67 | 10.36 | 110.38 | 125.04 | 7.63 req/s | 0% | PASS |

### Overall Results

| Result | Total |
|---|---:|
| Total Test Cases | 4 |
| Passed | 4 |
| Failed | 0 |
| Pass Rate | 100% |
| Overall Error Rate | 0% |

All four performance test cases passed, with 80 successful HTTP requests recorded across the tests.

## 5. Performance Analysis and Discussion

The results demonstrate that PyTodo Pro responded successfully to all tested sequential and concurrent requests. During sequential testing, the average response times ranged from 31.63 ms to 36.92 ms, indicating that the selected API endpoints provided relatively fast responses under the tested conditions.

The dashboard API recorded the lowest average response time at 31.63 ms, followed by the category API at 36.37 ms and the task API at 36.92 ms.

During concurrent testing, the application successfully processed all 20 requests without authentication or HTTP errors. However, the P95 response time increased to 110.38 ms, compared with 48.33 ms in the sequential task API test. This suggests that response times became more variable when multiple requests were processed concurrently.

Overall, the application demonstrated satisfactory basic responsiveness under the small testing workload. Nevertheless, the results should be treated as an initial performance baseline rather than proof of production-level scalability.

## 6. Testing Limitations

The performance testing was subject to the following limitations:

1. **Limited test volume:** Only 20 requests were used for each test case. This is insufficient to evaluate performance under heavy or sustained workloads.
2. **Local testing environment:** Tests were conducted on a local machine rather than a production server, so the results may differ under actual deployment conditions.
3. **Limited concurrency:** The concurrent test involved only five workers and does not establish the application's maximum concurrent-user capacity.
4. **Test-harness overhead:** The concurrent throughput measurement includes PowerShell background-job startup and management overhead.
5. **No long-duration testing:** Stress testing and endurance testing were not conducted.

## 7. Recommendations

Based on the performance testing results, the following improvements are recommended:

- Conduct additional load testing with a larger number of concurrent users to evaluate scalability.
- Monitor API response times under higher workloads to identify potential performance bottlenecks.
- Optimise database queries and application processing if response times increase significantly as the number of users grows.
- Consider implementing caching for frequently requested data, where appropriate.
- Conduct endurance testing in a production-like environment to evaluate performance stability over longer periods.

## 8. Conclusion

Performance testing of PyTodo Pro was successfully completed using four test cases covering sequential and concurrent API requests. All 80 recorded requests were processed successfully, resulting in a 100% test pass rate and a 0% error rate. The selected API endpoints demonstrated relatively fast response times during sequential testing. Although the concurrent test also completed successfully, an increase in P95 response time indicated greater response-time variation under concurrent activity. Overall, PyTodo Pro demonstrated satisfactory basic performance within the limited testing conditions. Further load and scalability testing would be required to evaluate its behaviour under heavier workloads.
