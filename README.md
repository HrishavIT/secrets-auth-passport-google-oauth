# Secrets: Authentication & OAuth with Node.js

A web app where users can register and log in with **email and password** or with their **Google account**, then access a protected `/secrets` page. Built with Express, Passport.js, PostgreSQL, and EJS.

## Features

- Register and log in with email and password
- Passwords hashed with **bcrypt** (10 salt rounds)
- **Sign in with Google** (OAuth 2.0)
- Session-based authentication with `express-session`
- Protected route: `/secrets` is only accessible when logged in
- Logout support
- Server-side rendered views with EJS

## Tech Stack

| Layer          | Technology                                               |
| -------------- | ---------------------------------------------------------|
| Runtime        | Node.js                                                  |
| Framework      | Express                                                  |    
| Auth           | Passport.js (`passport-local`, `passport-google-oauth2`) |
| Database       | PostgreSQL (`pg`)                                        |
| Hashing        | bcrypt                                                   |
| Sessions       | express-session                                          |
| Templating     | EJS                                                      |
| Config         | dotenv                                                   |

## Project Structure

```
.
├── public/
│   └── css/
│       └── styles.css
├── views/
│   ├── partials/
│   │   ├── header.ejs
│   │   └── footer.ejs
│   ├── home.ejs
│   ├── login.ejs
│   ├── register.ejs
│   ├── secrets.ejs
│   └── submit.ejs
├── .env
├── .gitignore
├── index.js
├── package.json
└── package-lock.json
```

## Prerequisites

- [Node.js](https://nodejs.org/) v18 or later
- [PostgreSQL](https://www.postgresql.org/) installed and running
- A Google Cloud project with OAuth 2.0 credentials

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/HrishavIT/secrets-auth-passport-google-oauth
cd secrets-auth-passport-google-oauth
```

### 2. Install dependencies

```bash
npm install
```

Make sure `package.json` contains `"type": "module"`, since the project uses ES module imports.

### 3. Set up the database

Create a database (for example `secrets`) and run:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(100) NOT NULL UNIQUE,
  password VARCHAR(100)
);
```

### 4. Set up Google OAuth

1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a project and open **APIs & Services → Credentials**.
3. Create an **OAuth client ID** (Application type: *Web application*).
4. Add this **Authorized redirect URI**:
```
   http://localhost:3000/auth/google/secrets
```
5. Copy the **Client ID** and **Client Secret**.

### 5. Configure environment variables

Create a `.env` file in the project root:

```env
SESSION_SECRET=your_long_random_secret

PG_USER=postgres
PG_HOST=localhost
PG_DATABASE=secrets
PG_PASSWORD=your_db_password
PG_PORT=5432

GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
```

> ⚠️ Never commit your `.env` file. Make sure it's listed in `.gitignore`.

### 6. Run the app

```bash
node index.js
```

Or, with auto-reload:

```bash
npx nodemon index.js
```

Open [http://localhost:3000](http://localhost:3000).

## Routes

| Method | Route                   | Description                                   | Auth required |
| ------ | ----------------------- | --------------------------------------------- | ------------- |
| GET    | `/`                     | Home page                                     | No            |
| GET    | `/login`                | Login page                                    | No            |
| GET    | `/register`             | Registration page                             | No            |
| POST   | `/register`             | Create a new account                          | No            |
| POST   | `/login`                | Log in with email and password                | No            |
| GET    | `/auth/google`          | Start Google OAuth flow                       | No            |
| GET    | `/auth/google/secrets`  | Google OAuth callback                         | No            |
| GET    | `/secrets`              | Protected secrets page                        | **Yes**       |
| GET    | `/logout`               | Log out and redirect to home                  | Yes           |

## How Authentication Works

1. **Registration:** the password is hashed with bcrypt and stored in the `users` table, then the user is logged in automatically.
2. **Local login:** Passport's `LocalStrategy` looks up the user by email and compares the submitted password against the stored hash.
3. **Google login:** Passport's Google strategy fetches the user's profile. If the email isn't in the database yet, a new user is created.
4. **Sessions:** on success, the user is serialized into the session. `req.isAuthenticated()` protects private routes.

## Future Improvements

- Let users submit their own secrets (`submit.ejs`)
- Store secrets in the database per user
- Add password reset by email
- Add more OAuth providers (GitHub, Facebook)
- Use a persistent session store (e.g. `connect-pg-simple`)

## Author

Made by **Hrishav Raj Singh** ([@HrishavIT](https://github.com/HrishavIT))
