# Test Execution Summary & Planned-vs-Actual Report

## 1. Black-Box Test Execution Metrics
| Metric | Count | Percentage |
| :--- | :---: | :---: |
| **Total Planned Test Cases** | 21 | 100.00% |
| **Total Executed Test Cases** | 21 | 100.00% |
| **Passed** | 17 | 80.95% |
| **Failed** | 4 | 19.05% |
| **Blocked / Not Run** | 0 | 0.00% |

## 2. Defect Distribution by Severity & Feature
| Defect ID | Affected Requirement | Severity | Priority | Status | Summary |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **DEF-001** | `REQ-05` Create Task | Critical | High | Open | Unhandled `ValueError` server crash on 5-digit year (`19999-06-14`) |
| **DEF-002** | `REQ-08` Task Urgency | Major | High | Open | Task due today (`T`) mislabeled as `Overdue` instead of `Due Today` |
| **DEF-003** | `REQ-01` User Registration | Critical | High | Open | Duplicate registration overwrites existing user account records |
| **DEF-004** | `REQ-01` User Registration | Major | Medium | Open | Second unique account registration blocked within same browser |
| **DEF-005** | `REQ-01` User Registration | Minor | Low | Open | Weak lowercase-only passwords accepted without complexity rules |

- **Severity Breakdown:** 2 Critical (`40%`), 2 Major (`40%`), 0 Moderate (`0%`), 1 Minor (`20%`).

## 3. Planned-versus-Actual Variance Analysis
- **Execution Coverage:** All 21 planned black-box test cases (`TC-BB-001` through `TC-BB-021`) were executed manually against PyTodo Pro (`100%` execution rate).
- **Key Variances & Uncovered Risks:**
  1. During Boundary Value Analysis of due dates (`TC-BB-006` to `TC-BB-010`), future dates (`T+1`, `T+2`, `T+3`) and past dates (`T-1`, `T-2`) behaved as specified, but the exact current date boundary (`T = 2026-11-21`) failed (`DEF-002`), indicating a strict `<` vs. `<=` comparison defect in urgency calculation.
  2. During Equivalence Partitioning on `/tasks/new` (`TC-BB-005`), testing a 5-digit year (`19999-06-14`) caused an unhandled Flask `ValueError` crash (`DEF-001`) that halted normal page return navigation until the route was manually reloaded.
  3. Because the project evaluates PyTodo Pro without modifying source code, all 5 confirmed defects remain in `Open` status for inclusion in `08_Final_QA_Report`.
