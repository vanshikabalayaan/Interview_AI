# Interview AI

An AI-powered interview preparation app. Upload your resume, describe yourself, and paste a job description. Interview AI uses Google Gemini to generate a personalised interview report and a tailored, ATS-friendly resume PDF.

## Features

- **Authentication**: register, log in and log out with JWT stored in a cookie. Logged-out tokens are blacklisted.
- **Interview report generation**: from your resume (PDF), self description and the target job description, it produces:
  - A match score (0–100) between your profile and the job
  - Likely technical questions with the interviewer's intention and how to answer
  - Likely behavioral questions with the interviewer's intention and how to answer
  - Skill gaps, each with a severity (low / medium / high)
  - A day-wise preparation plan
- **Tailored resume PDF**: generates a resume customised for the job description and downloads it as an A4 PDF.
- **Report history**: view all your past reports and open any one of them.

## Tech Stack

| Layer    | Technologies |
| -------- | ------------ |
| Frontend | React 19, Vite, React Router 7, Axios, Sass |
| Backend  | Node.js, Express 5, MongoDB (Mongoose), JWT, bcryptjs, Multer, pdf-parse |
| AI / PDF | Google Gemini (`@google/genai`), Zod, Puppeteer |

## Project Structure

```
Interview_AI/
├── Backend/
│   ├── server.js                 # Entry point (port 3000)
│   └── src/
│       ├── app.js                # Express app, CORS, routes
│       ├── config/database.js    # MongoDB connection
│       ├── controllers/          # auth + interview logic
│       ├── middlewares/          # auth (JWT) + file upload (Multer)
│       ├── models/               # user, interviewReport, blacklist
│       ├── routes/               # auth + interview routes
│       └── services/ai.service.js # Gemini calls + Puppeteer PDF
└── Frontend/
    └── src/
        ├── app.routes.jsx        # Client-side routes
        └── features/
            ├── auth/             # Login, Register, protected routes
            └── interview/        # Home (form), Interview (report view)
```

## Getting Started

### Prerequisites

- Node.js 18 or newer
- A MongoDB database (local or MongoDB Atlas)
- A Google Gemini API key from [Google AI Studio](https://aistudio.google.com/)

### 1. Clone the repository

```bash
git clone https://github.com/vanshikabalayaan/Interview_AI.git
cd Interview_AI
```

### 2. Set up the backend

```bash
cd Backend
npm install
```

Create a `Backend/.env` file:

```env
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=a_long_random_secret
GOOGLE_GENAI_API_KEY=your_gemini_api_key
```

Start the server:

```bash
npm run dev
```

The API runs at `http://localhost:3000`.

### 3. Set up the frontend

In a new terminal:

```bash
cd Frontend
npm install
npm run dev
```

The app runs at `http://localhost:5173`. The backend only allows CORS requests from this origin.

## API Reference

All routes are prefixed with `/api`. Private routes need the auth cookie set at login.

### Auth

| Method | Endpoint         | Access  | Description |
| ------ | ---------------- | ------- | ----------- |
| POST   | `/auth/register` | Public  | Register a new user |
| POST   | `/auth/login`    | Public  | Log in with email and password |
| GET    | `/auth/logout`   | Public  | Clear the cookie and blacklist the token |
| GET    | `/auth/get-me`   | Private | Get the current user |

### Interview

| Method | Endpoint                          | Access  | Description |
| ------ | --------------------------------- | ------- | ----------- |
| POST   | `/interview/`                     | Private | Generate a report. Send `multipart/form-data` with the `resume` file (max 3 MB), plus the self description and job description |
| GET    | `/interview/`                     | Private | List all reports of the logged-in user |
| GET    | `/interview/report/:interviewId`  | Private | Get one report by id |
| POST   | `/interview/resume/pdf/:interviewReportId` | Private | Generate a tailored resume PDF for a report |

## Scripts

**Backend**

| Command       | Description |
| ------------- | ----------- |
| `npm run dev` | Start the server with nodemon |

**Frontend**

| Command           | Description |
| ----------------- | ----------- |
| `npm run dev`     | Start the Vite dev server |
| `npm run build`   | Build for production |
| `npm run preview` | Preview the production build |
| `npm run lint`    | Run ESLint |

## Notes

- Never commit your `.env` file. The backend `.gitignore` should exclude it.
- Puppeteer downloads Chromium on first install, so the first `npm install` in `Backend` can take a while.
- The Gemini model used is set in `Backend/src/services/ai.service.js`.
