# Mergington High School Activities API

A super simple FastAPI application that lets students view extracurricular activities and teachers manage signups.

## Features

- View all available extracurricular activities
- Teacher-only signup and unregister controls
- Teacher login using an HTTP-only session cookie

## Getting Started

1. Install the dependencies:

   ```
   pip install fastapi uvicorn
   ```

2. Configure teacher credentials and a session-signing secret in the environment. These values must not be committed to the repository:

   ```
   export ADMIN_USERNAME="teacher"
   export ADMIN_PASSWORD="<a strong teacher password>"
   export ADMIN_SESSION_SECRET="$(python -c 'import secrets; print(secrets.token_urlsafe(32))')"
   ```

   `ADMIN_SESSION_SECRET` must be at least 32 characters. Use a securely managed, stable secret in deployed environments.

3. Run the application:

   ```
   uvicorn src.app:app --reload
   ```

4. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                               | Description                                               |
| ------ | ---------------------------------------------------------------------- | --------------------------------------------------------- |
| GET    | `/activities`                                                          | Get all activities and participant details               |
| GET    | `/auth/me`                                                             | Check whether the current browser session is authenticated |
| POST   | `/auth/login`                                                          | Log in with a JSON `username` and `password`              |
| POST   | `/auth/logout`                                                         | End the current teacher session                           |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu`      | Teacher-only student signup                              |
| DELETE | `/activities/{activity_name}/unregister?email=student@mergington.edu`  | Teacher-only student unregister                          |

Activities and participant lists remain visible without logging in. Signup and unregister requests return `401` without a valid teacher session, or `503` if authentication has not been configured.

## Data Model

The application uses a simple data model with meaningful identifiers:

1. **Activities** - Uses activity name as identifier:

   - Description
   - Schedule
   - Maximum number of participants allowed
   - List of student emails who are signed up

2. **Students** - Uses email as identifier:
   - Name
   - Grade level

All data is stored in memory, which means data will be reset when the server restarts.
