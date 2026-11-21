# Software Test Plan & Strategy — PyTodo Pro

## 1. Objectives & Risk Analysis ("Why are we testing?")
- **Primary Objective:** Conduct an independent, evidence-based Software Quality Assurance (SQA) evaluation of **PyTodo Pro** across functional and non-functional quality dimensions without modifying application source code.
- **Key Quality Risks:**
  1. **Date-Boundary & Input Validation Defects (High Risk):** Off-by-one errors in due-date urgency classification (`Overdue` vs. `Due Today` vs. `Due Soon`) and unhandled exceptions when malformed dates (e.g., 5-digit years) are submitted.
  2. **Authentication & Account Integrity (High Risk):** Duplicate registration overwriting existing user records, cross-session registration restrictions, and weak password validation.
  3. **Dashboard Metric Consistency (Medium-High Risk):** Discrepancies between underlying SQLite task states and `/dashboard` counters.

## 2. Testing Scope ("What are we testing?")
- **In-Scope:**
  - **Black-Box Functional Testing (`01_Black_Box`):** End-to-end Web UI workflows covering `REQ-01` (Registration), `REQ-02` (Login), `REQ-03` (Logout), `REQ-04` (Task List/Filter/Search), `REQ-05` (Create Task), `REQ-06` (Edit/Complete Task), `REQ-07` (Delete Task), `REQ-08` (Task Urgency), and `REQ-09` (Summary Dashboard).
  - **Cross-Phase Integration (`02`–`06`):** White-box coverage, GUI/accessibility/compatibility, Web/REST API validation (`04_Web_API`), STRIDE security & performance benchmarking (`05_Security_Performance`), and automated regression (`06_Automation_Regression`).
- **Out-of-Scope:**
  - Modifying or patching PyTodo Pro source code (`app.py`, `helpers.py`, HTML templates).
  - Uncontrolled external penetration testing.

## 3. Test Strategy & Techniques ("How will we test?")
- **Black-Box Design Techniques:**
  - **Equivalence Partitioning (EP):** Valid and invalid input classes for registration, login, and task creation forms.
  - **Boundary Value Analysis (BVA):** Exact date thresholds (`T-2`, `T-1`, `T`, `T+1`, `T+2`, `T+3`, null) and field character length limits (`3–20` chars username, `min 8` chars password).
  - **Decision Table Testing:** Multi-condition combinations for Login authentication and Home Page filtering (`Category` × `Status` × `Search`).
  - **State Transition Testing:** Task lifecycle (`Open → Done → Reopened → Deleted`) and User Session lifecycle (`Logged Out → Logged In → Logged Out`).
- **Tools Used:** Google Chrome (manual browser execution), Python 3 / Flask local runtime, SQLite (`pytodo_pro.db`), Microsoft Excel / CSV templates, Windows Snipping Tool (visual evidence), and GitHub (version-controlled QA repository).

## 4. Environment, Test Data & Schedule ("When and with what?")
- **Test Environment:** Windows 11 OS, Python 3 virtual environment (`venv`), Flask local development server (`http://127.0.0.1:5000/`), Google Chrome browser.
- **Test Data Rules:** Clean SQLite database (`pytodo_pro.db` reset prior to test execution); baseline user credentials `ayamgoreng` / `akusukaayam`; deterministic boundary dates anchored to execution date `2026-11-21`.
- **Schedule:** Progressive execution across Project Weeks 1–8 aligned with course milestones (`01_Black_Box` through `09_Presentation`).

## 5. Entry & Exit Criteria ("How will we know we are done?")
- **Entry Criteria:**
  1. PyTodo Pro launches cleanly on `http://127.0.0.1:5000/` without startup errors.
  2. Clean SQLite database initialized and test case templates prepared.
- **Exit Criteria:**
  1. 100% of planned black-box test cases (`21/21`) executed with recorded actual results.
  2. Every failed test case supported by screenshot evidence and logged in `consolidated_defect_log.csv`.
  3. Requirements Traceability Matrix (`rtm.csv`) 100% mapped across `REQ-01` to `REQ-09`.
