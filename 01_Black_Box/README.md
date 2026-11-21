# 01 — Black-Box Testing (Project Week 1)

## Role & Scope
- **Owner:** Member 1 — Test Planning, Black-Box & Test Management
- **Target System:** PyTodo Pro (`http://127.0.0.1:5000/`)
- **Testing Approach:** Manual black-box functional testing of the Web UI (`REQ-01` through `REQ-09`) from an end-user perspective without relying on internal source code logic.
- **Out of Scope:** White-box unit/coverage testing (`02_White_Box_Coverage`), GUI/accessibility/compatibility (`03_Special_Testing`), REST `/api/` endpoints (`04_Web_API`), security/STRIDE & performance load testing (`05_Security_Performance`), and automated regression scripts (`06_Automation_Regression`).

## Folder Contents
- `README.md` — Summary of black-box scope, technique justifications, and execution overview.
- `application_overview_and_risk_matrix.md` — System architecture overview, functional feature inventory (`REQ-01` to `REQ-09`), and risk assessment matrix.
- `black_box_test_cases.csv` (and `.xlsx`) — Complete 21-case black-box test suite (`TC-BB-001` to `TC-BB-021`) with execution results, screenshot evidence links, and defect IDs.
- `screenshots/` — Visual execution evidence (`register.png`, `login.png`, `=0.png`, `-1.png`, `-2.png`, `+1.png`, `+2.png`, `+3.png`, `blank_due_date.png`, `bug 1_date error.png`, `register_password.png`, `register_password_error.png`, `register_username_error.png`, `register_username_password_error.png`).

## Technique Justification
1. **Equivalence Partitioning (EP):** Applied to User Registration (`/register`), Login (`/login`), and Create Task (`/tasks/new`) input fields to test representative valid and invalid input classes without redundant test cases.
2. **Boundary Value Analysis (BVA):** Applied to Task Urgency date calculations relative to current date $T$ (`T-2`, `T-1`, `T`, `T+1`, `T+2`, `T+3`, and blank) as well as username/password length boundaries (`3–20` chars username, `min 8` chars password) where off-by-one logic errors commonly occur.
3. **Decision Table Testing:** Applied to Login authentication (`Valid/Invalid Username` × `Valid/Invalid Password`) and Home Page task filtering (`Category` × `Status` × `Search Query`) where multiple conditions jointly determine system output.
4. **State Transition Testing:** Applied to the Task lifecycle (`Non-existent → Open → Done → Reopened → Deleted`, including direct URL access after deletion) and User Session lifecycle (`Logged Out → Logged In → Logged Out → Browser Back Button`).

## Execution Summary
- **Test Account Used:** `ayamgoreng` / `akusukaayam`
- **Total Planned & Executed:** 21 test cases
- **Passed:** 17 (80.95%)
- **Failed:** 4 (19.05%) — `TC-BB-002`, `TC-BB-003`, `TC-BB-005`, `TC-BB-007`
- **Defects Identified:** 5 (`DEF-001` to `DEF-005`), consolidated in `07_Test_Management/consolidated_defect_log.csv`.
