# Job Tracker

## Project overview

Job Tracker is a full-stack application for managing job applications. It provides a React dashboard for adding, editing, filtering, searching, and deleting applications, backed by an Express API and PostgreSQL database.

Core features:
- Create, update, view, and delete job application records
- Filter applications by status
- Search applications by company name or job title
- Paginated application list with total count
- Responsive UI with Tailwind CSS
- API client powered by Redux Toolkit Query

## Tech stack used

### Client
- React 19
- TypeScript
- Vite
- React Router
- Redux Toolkit Query
- Tailwind CSS
- Vitest and Testing Library

### Server
- Node.js
- TypeScript
- Express 5
- Prisma ORM
- PostgreSQL / Neon
- Zod request validation
- CORS

## Prerequisites

- Node.js 20 or newer
- npm
- PostgreSQL database access, such as a Neon Postgres connection string
- Git

## Installation steps

1. Clone the repository and open the project folder.

2. Install server dependencies:

   ```bash
   cd server
   npm install
   ```

3. Install client dependencies:

   ```bash
   cd ./client
   npm install
   ```

4. Create environment files from the recommended examples below.

   Server:

   ```bash
   cd ./server
   ```

   Create `server/.env` with:

   ```env
   DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DATABASE?sslmode=require"
   SERVER_URL=http://localhost:3000
   PORT=3000
   ```

   Client:

   ```bash
   cd ./client
   ```

   Create `client/.env` with:

   ```env
   VITE_API_URL=http://localhost:3000
   ```


5. Generate the Prisma client:

   ```bash
   cd ./server
   npx prisma generate
   ```

6. Run database migrations:

   ```bash
   npx prisma migrate dev --name init
   ```

## How to run in development mode

Run the API server and client in separate terminals.

Terminal 1 - server:

```bash
cd server
npm run dev
```

The API will run at:

```text
http://localhost:3000
```

Terminal 2 - client:

```bash
cd client
npm run dev
```

The React app will run at:

```text
http://localhost:5173
```

## How to run the built static client from the server

The server serves the built React app from `client/dist` and returns `client/dist/index.html` for unknown routes, so React Router routes work when the frontend is built.

1. Build the client:

   ```bash
   cd client
   npm run build
   ```

2. Start the server from the `server` directory:

   ```bash
   cd ./server
   npm run dev
   ```

3. Open the app:

   ```text
   http://localhost:3000
   ```

## How to run tests

Run client tests:

```bash
cd client
npm test
```

Run server tests:

```bash
cd server
npm test
```

The client currently includes Vitest tests.

## Required environment variables

| Variable | Location | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `DATABASE_URL` | `server/.env` | Yes | None | PostgreSQL connection string used by Prisma. |
| `PORT` | `server/.env` | No | `3000` | Port used by the Express server. |
| `SERVER_URL` | `server/.env` | No | `http://localhost:${PORT}` | Public server URL used by the application. |
| `VITE_API_URL` | `client/.env` | No | `http://localhost:3000` | API base URL used by the React app. |

## Recommended `.env.example` files

### `server/.env.example`

```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DATABASE?sslmode=require"
SERVER_URL=http://localhost:3000
PORT=3000
```

### `client/.env.example`

```env
VITE_API_URL=http://localhost:3000
```

## API docs

Base URL:

```text
http://localhost:3000
```

Authentication is not currently required.

### `GET /applications`

List applications.

Query parameters:

| Parameter | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `status` | `APPLIED`, `INTERVIEWING`, `OFFER`, `REJECTED` | No | All | Filter by application status. |
| `search` | string | No | None | Search by company name or job title. |
| `page` | number | No | `1` | Page number. |
| `limit` | number | No | `10` | Items per page, maximum `100`. |

Example response:

```json
{
  "applications": [],
  "total": 0,
  "page": 1,
  "limit": 10
}
```

### `GET /applications/:id`

Get one application by UUID.

Example response:

```json
{
  "id": "application-uuid",
  "company_name": "Acme Corp",
  "job_title": "Frontend Developer",
  "job_type": "FULL_TIME",
  "status": "APPLIED",
  "applied_date": "2026-06-18",
  "notes": "Follow up next week",
  "created_at": "2026-06-18T00:00:00.000Z",
  "updated_at": "2026-06-18T00:00:00.000Z"
}
```

Possible responses:
- `200`: Application found
- `400`: Invalid UUID
- `404`: Application not found

### `POST /applications`

Create a new application.

Request body:

```json
{
  "company_name": "Acme Corp",
  "job_title": "Frontend Developer",
  "job_type": "FULL_TIME",
  "status": "APPLIED",
  "applied_date": "2026-06-18",
  "notes": "Follow up next week"
}
```

Field rules:

| Field | Type | Required | Rules |
| --- | --- | --- | --- |
| `company_name` | string | Yes | Minimum 2 characters |
| `job_title` | string | Yes | Minimum 1 character |
| `job_type` | enum | Yes | `INTERNSHIP`, `FULL_TIME`, `PART_TIME` |
| `status` | enum | Yes | `APPLIED`, `INTERVIEWING`, `OFFER`, `REJECTED` |
| `applied_date` | date string | Yes | Date format such as `YYYY-MM-DD` |
| `notes` | string | No | Optional text |

Possible responses:
- `201`: Application created
- `400`: Validation error

### `PATCH /applications/:id`

Update an existing application by UUID.

Request body can include any of these optional fields:

```json
{
  "company_name": "Acme Corp",
  "job_title": "Frontend Developer",
  "job_type": "FULL_TIME",
  "status": "INTERVIEWING",
  "applied_date": "2026-06-18",
  "notes": "Updated follow-up note"
}
```

Possible responses:
- `200`: Application updated
- `400`: Invalid UUID or validation error
- `404`: Application not found

### `DELETE /applications/:id`

Delete an application by UUID.

Possible responses:
- `204`: Application deleted
- `400`: Invalid UUID
- `404`: Application not found

### Example request

```bash
curl -X POST http://localhost:3000/applications \
  -H "Content-Type: application/json" \
  -d '{
    "company_name": "Acme Corp",
    "job_title": "Frontend Developer",
    "job_type": "FULL_TIME",
    "status": "APPLIED",
    "applied_date": "2026-06-18",
    "notes": "Follow up next week"
  }'
```
