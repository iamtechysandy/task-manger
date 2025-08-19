📑 Final System Plan — Task Tracker (Node.js + MongoDB)

🎯 1. Purpose

Internal Task Tracking System for enterprise use.

No public signup → Only default Admin creates users.

Supports departments, department heads, multiple assignees, task workflows, audit logs, file attachments, and email notifications.

Local deployment (LAN or internal network).

👥 2. Roles & Permissions

Role

Permissions

Admin

Create departments, users, assign department heads, manage all tasks, view global reports & audit logs.

Department Head

Manage tasks inside department, assign to members, view dept. reports.

User

View/update tasks assigned to them, comment, upload attachments.

🗂️ 3. Database Design (MongoDB Collections)

Users

{
  "_id": ObjectId,
  "name": String,
  "email": String,
  "passwordHash": String,
  "role": {"type": String, "enum": ["admin", "head", "user"]},
  "departmentId": ObjectId,
  "createdAt": Date
}

Departments

{
  "_id": ObjectId,
  "name": String,
  "headId": ObjectId,
  "createdAt": Date
}

Tasks

{
  "_id": ObjectId,
  "title": String,
  "description": String,
  "departmentId": ObjectId,
  "createdBy": ObjectId,
  "startedBy": ObjectId,
  "priority": {"type": String, "enum": ["low", "medium", "high", "critical"], "default": "medium"},
  "status": {"type": String, "enum": ["todo", "in_progress", "blocked", "done", "cancelled"], "default": "todo"},
  "progressPercentage": {"type": Number, "min": 0, "max": 100, "default": 0},
  "startDate": Date,
  "dueDate": Date,
  "completedAt": Date,
  "followUpDate": Date,
  "lastUpdatedBy": ObjectId,
  "lastUpdatedAt": Date,
  "isRecurring": Boolean,
  "parentTaskId": ObjectId,
  "assignees": [ObjectId],
  "createdAt": Date
}

Task Attachments

{
  "_id": ObjectId,
  "taskId": ObjectId,
  "uploadedBy": ObjectId,
  "filePath": String,
  "fileType": String,
  "uploadedAt": Date
}

Comments

{
  "_id": ObjectId,
  "taskId": ObjectId,
  "userId": ObjectId,
  "comment": String,
  "createdAt": Date
}

Audit Logs

{
  "_id": ObjectId,
  "userId": ObjectId,
  "action": String,
  "entity": String,
  "entityId": ObjectId,
  "timestamp": Date
}

🏗️ 4. Backend (Node.js + Express API)

Auth:

POST /auth/login → JWT session

POST /auth/logout

Users (Admin only):

POST /users → create user

GET /users → list users

PUT /users/:id → update user

DELETE /users/:id → deactivate user

Departments:

POST /departments → create department

GET /departments → list departments

PUT /departments/:id → assign head

Tasks:

POST /tasks → create task (admin/head)

GET /tasks → list tasks (role-based filter)

PUT /tasks/:id → update task (status, progress, etc.)

DELETE /tasks/:id → delete task

Comments:

POST /tasks/:id/comments

GET /tasks/:id/comments

Attachments:

POST /tasks/:id/attachments → upload file(s)

GET /tasks/:id/attachments → list attachments

DELETE /attachments/:id → delete file

Logs:

GET /logs → Admin only

🎨 5. Frontend (React + TailwindCSS + shadcn/ui)

Pages

Login Page → email & password only.

Admin Dashboard → users, departments, global tasks, audit logs.

Department Head Dashboard → manage dept. tasks, reports.

User Dashboard → assigned tasks, update status, progress bar.

Task Detail Page → comments, attachments, history.

Features

React Query (for API calls).

Role-based UI rendering.

Task Kanban view (Todo → In Progress → Done).

Search, filters, priority labels.

Attachment Upload (drag & drop, preview, download).

📩 6. Email Notifications (Nodemailer + SMTP)

Triggers:

Task assigned → email assignees

Task updated → notify creator + assignees

Task overdue → cron job sends reminder

Attachment uploaded → notify task assignees

SMTP Config:

Local mail server OR Gmail with App Password

🔐 7. Security

JWT + HttpOnly cookies for sessions.

Password hashing with bcrypt.

Role-based middleware.

All APIs behind authentication.

Audit logs for every action.

🛠️ 8. Deployment (Local/Internal)

Database: MongoDB running on server (LAN).

Backend: Node.js + Express API (Docker optional).

Frontend: React app served via Nginx or Express static hosting.

Email: Internal SMTP or Gmail integration.

File Storage: Local server storage (mapped folder) or S3-compatible storage (MinIO for local).

🚦 9. Future Enhancements

File versioning & preview in browser.

Mobile app (React Native).

Analytics dashboard (charts, performance reports).

Integrations (Slack/Teams).

AI reminders (auto detect overdue + suggest priority).

