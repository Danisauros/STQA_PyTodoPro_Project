# Application Overview, Feature Inventory & Risk Matrix

## 1. Application Overview
**PyTodo Pro** is a task-management web application built with Python (Flask) and backed by an SQLite database (`pytodo_pro.db`, auto-generated on startup), served locally at `http://127.0.0.1:5000/`. The application provides a browser-based Web UI and JSON REST endpoints for managing personal and categorized tasks. No default user accounts exist in a fresh database; users must register an account before creating or viewing tasks.

## 2. Feature Inventory (Web UI Requirements)
| Requirement ID | Feature / Module | Route | Description & Expected Behavior |
| :--- | :--- | :--- | :--- |
| **REQ-01** | User Registration | `/register` | Creates a new user account (`username`: 3–20 alphanumeric/underscore chars; `password`: minimum 8 characters). Rejects duplicate or malformed credentials. |
| **REQ-02** | User Login | `/login` | Authenticates registered users via session cookies and redirects to `/`. Displays an error banner on invalid credentials. |
| **REQ-03** | User Logout | `/logout` | Terminates the active user session and restricts access to protected routes. |
| **REQ-04** | Task Listing, Filter & Search | `/` | Displays user tasks and filters by **Category** (*Work, Personal, Study, Urgent*), **Status** (*Open, Done*), and **Title Search** (`q`). |
| **REQ-05** | Create Task | `/tasks/new` | Creates a task with `title`, `description`, `category_id`, and optional `due_date` (`YYYY-MM-DD`). |
| **REQ-06** | Edit & Complete Task | `/tasks/<id>/edit` | Updates task details or toggles completion status (`Open` ↔ `Done`). |
| **REQ-07** | Delete Task | `/` (Delete action) | Permanently removes a task from the user's task list and database. |
| **REQ-08** | Task Urgency Classification | `/` | Automatically labels each task as **No due date**, **Overdue** (`due_date < today`), **Due Today** (`due_date == today`), **Due Soon** (within 2 days), **Upcoming** (`> 2 days`), or **Done**. |
| **REQ-09** | Summary Dashboard | `/dashboard` | Displays five summary counters: **Total**, **Open**, **Overdue**, **Due Soon**, and **Completed in the last 7 days**. |

## 3. Initial Scope & Risk Assessment Matrix
| Feature / Module | Risk Description | Likelihood | Impact | Overall Risk | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **User Authentication (`REQ-01`–`REQ-03`)** | Duplicate accounts overwriting existing user data, registration blocks across browser sessions, or weak password acceptance | Medium | High | **High** | P1 |
| **Task Urgency Date Logic (`REQ-08`)** | Off-by-one boundary error misclassifying `Due Today` (`T`) as `Overdue`, or miscalculating the 2-day `Due Soon` window | High | High | **High** | P1 |
| **Task CRUD & Input Validation (`REQ-05`–`REQ-07`)** | Unhandled server crash (`ValueError`) on malformed/5-digit years (`19999-06-14`) or invalid URLs after deletion | Medium | High | **High** | P1 |
| **Summary Dashboard (`REQ-09`)** | Counter mismatch between task list state and dashboard metrics (`Overdue`, `Due Soon`, `Completed in last 7 days`) | High | Medium | **High** | P1 |
| **Task Filtering & Search (`REQ-04`)** | Combined Category + Status + Title Search filters returning inaccurate subsets | Medium | Medium | **Medium** | P2 |
