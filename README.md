# LMS Platform

A full-stack Learning Management System (LMS) for browsing and managing courses. This project was built as a mini project for **Day 43: CRUD API (Update and Delete)**. The assignment focus is implemented in the course API with protected `PUT` and `DELETE` operations, input validation, and instructor ownership checks.

## Features

- Browse course listings and view course details.
- Search courses by title, filter by level, and sort by rating, enrollment count, or duration.
- Register and log in; authenticated requests use JWT bearer tokens.
- Create, update, and delete courses as an instructor. Instructors can only update or delete their own courses.
- Create and delete categories in the admin category screen. The category API also provides a protected update endpoint.
- Validate course data and return appropriate API errors for invalid input, missing records, and unauthorized actions.

## Tech Stack

- **Frontend:** React, React Router, Vite
- **Backend:** Node.js, Express
- **Database:** MySQL (`mysql2`)
- **Authentication:** JSON Web Tokens and `bcryptjs`

## Project Structure

```text
backend/
  app.js                 Express app and API route mounting
  server.js              API server entry point
  config/                MySQL connection pool
  controllers/           Request handling and validation
  db/schema.sql          Database schema and starter data
  middleware/            JWT authentication and role authorization
  repositories/          MySQL queries
  routes/                API route definitions
frontend/
  src/pages/              Course, account, dashboard, and category screens
  src/components/         Shared UI components
  src/services/api.js     Frontend API requests
```


## Requirements

- Node.js 22.12 or newer
- npm
- MySQL Server

## Getting Started

### 1. Set up the database

From the project root, run the SQL script using your MySQL client. It creates the `lms_platform` database, its tables, and starter records.

```sh
mysql -u root -p
```

At the MySQL prompt, run `SOURCE backend/db/schema.sql;` from the project root. Alternatively, open `backend/db/schema.sql` in MySQL Workbench and execute it.

### 2. Configure and run the backend

Create `backend/.env` with your local MySQL credentials and a private JWT secret:

```dotenv
PORT=3000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=lms_platform
DB_PORT=3306
JWT_SECRET=replace_with_a_long_random_secret
```

Install dependencies and start the API:

```sh
cd backend
npm install
npm run dev
```

The API is available at `http://localhost:3000`; its routes are under `/api`. The root endpoint (`GET /`) can be used to check that the server is running.

### 3. Configure and run the frontend

In a second terminal, create `frontend/.env`:

```dotenv
VITE_API_URL=http://localhost:3000/api
```

Then install dependencies and start Vite:

```sh
cd frontend
npm install
npm run dev
```

Open the local URL printed by Vite (typically `http://localhost:5173`). Keep both development servers running while using the app.

## API Overview

All endpoints return JSON. Protected endpoints require `Authorization: Bearer <token>`; obtain a token from `POST /api/auth/login`.

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| `POST` | `/api/auth/register` | Public | Register an account |
| `POST` | `/api/auth/login` | Public | Log in and receive a JWT |
| `GET` | `/api/auth/me` | Authenticated | Get the current user |
| `GET` | `/api/courses` | Public | List courses; accepts `search`, `category_id`, `level`, `sort_by`, and `order` query parameters |
| `GET` | `/api/courses/:id` | Public | Get a course by ID |
| `POST` | `/api/courses` | Instructor | Create a course |
| `PUT` | `/api/courses/:id` | Course-owning instructor | Validate and update a course |
| `DELETE` | `/api/courses/:id` | Course-owning instructor | Delete a course |
| `GET` | `/api/categories` | Public | List categories |
| `GET` | `/api/categories/:id` | Public | Get a category by ID |
| `POST` | `/api/categories` | Admin | Create a category |
| `PUT` | `/api/categories/:id` | Admin | Update a category |
| `DELETE` | `/api/categories/:id` | Admin | Delete a category |

Course sorting supports `rating`, `enrolled_count`, and `duration`; `order` accepts `asc` or `desc`. Course create/update requests require `title`, `description`, `rating`, `category_id`, `level`, and `duration`. Updates also require `status`; new courses default to `draft`. `thumbnail` is optional. Allowed levels are `beginner`, `intermediate`, `advanced`, and allowed statuses are `draft`, `published`.

### Update a course

```sh
curl -X PUT http://localhost:3000/api/courses/1 \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title":"Updated course title","description":"Updated course description","rating":8.5,"category_id":1,"level":"intermediate","duration":900,"thumbnail":"course.jpg","status":"published"}'
```

### Delete a course

```sh
curl -X DELETE http://localhost:3000/api/courses/1 \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

The API returns `401` for missing or invalid authentication, `403` when the user lacks the required role or does not own the course, `404` when a requested record does not exist, and `422` when request validation fails.

## Checks

Run the available frontend checks from `frontend/`:

```sh
npm run lint
npm run build
```

The backend package currently does not define an automated test suite.

## Security Notes

- Keep both `.env` files local and never commit real database credentials or JWT secrets.
- Change any starter credentials before using this project outside a local development environment.