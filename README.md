# MaaCare — Maternal Healthcare Platform

A full-stack web application that connects mothers, doctors, hospitals, and ASHA workers across India. Built with React on the frontend and Node.js on the backend, it covers everything from pregnancy tracking and teleconsultation to hospital booking with insurance integration, an emergency SOS system, and a portable Digital Health Passport.

---

## Table of Contents

- [What is MaaCare](#what-is-maacare)
- [Who it's for](#who-its-for)
- [What problems it solves](#what-problems-it-solves)
- [Features by module](#features-by-module)
- [Project structure](#project-structure)
- [Tech stack](#tech-stack)
- [How to run the project locally](#how-to-run-the-project-locally)
- [Environment variables](#environment-variables)
- [All API routes (summary)](#all-api-routes-summary)
- [User roles and permissions](#user-roles-and-permissions)
- [How each major feature works](#how-each-major-feature-works)
- [Deployment](#deployment)
- [Package dependencies](#package-dependencies)
- [Available scripts](#available-scripts)
- [Contact](#contact)

---

## What is MaaCare

MaaCare is a healthcare platform designed specifically around maternal and child health in India. The platform brings together multiple stakeholders — pregnant mothers, specialist doctors, ASHA (Accredited Social Health Activist) workers, hospitals, and administrators — into one connected system.

A mother can use MaaCare to:
- Track her pregnancy week by week
- Book an appointment with a gynecologist
- Join a video teleconsultation from home
- Find the nearest hospital and book a service
- Apply her insurance policy at the time of booking
- Generate a Digital Health Passport with a QR code (for emergencies)
- Trigger an SOS alert that captures her location and emails her emergency contacts
- Track her baby's milestones and vaccination schedule
- Get step-by-step guidance for health conditions like anemia or gestational diabetes

---

## Who It's For

| Role | What they use MaaCare for |
|------|--------------------------|
| Mother | Pregnancy tracking, doctor booking, hospital booking, insurance, SOS, health records |
| Doctor | Managing appointments, teleconsultation, viewing patient records |
| ASHA Worker | Tracking assigned mothers, scheduling visits, checking government schemes |
| Hospital | Registering services, managing bookings and bed counts, approving/rejecting bookings |
| Admin | Viewing platform analytics and insights, managing all users |

---

## What Problems It Solves

1. Rural mothers cannot easily access specialist doctors → solved by teleconsultation
2. No centralized pregnancy tracking tool → solved by the pregnancy and baby dashboards
3. Insurance complications at hospitals → solved by insurance integration in the booking form
4. No portable medical identity → solved by the Digital Health Passport and QR code
5. No way to quickly alert family/doctor in a high-risk emergency → solved by the SOS panel
6. Information scattered across different government portals → solved by the schemes directory
7. Language barrier between patients and doctors → solved by multilingual real-time chat

---

## Features by Module

### Module 1 — Authentication
- Registration with role selection (Mother, Doctor, ASHA Worker, Hospital, Admin)
- OTP email verification before first login
- JWT-based sessions (tokens stored in localStorage)
- Forgot password and reset via OTP
- Role-based access control on all protected routes

### Module 2 — Pregnancy Tracking
- Pregnancy profile: due date, weeks pregnant, trimester, blood pressure, weight
- Risk level assessment
- Kick counter and contraction timer
- Appointment booking with doctors

### Module 3 — Doctors
- Browse doctors by specialty (Gynecologist, Pediatrician, Cardiologist, etc.)
- Doctor profile pages with qualifications, experience, and availability
- Availability calendar
- Rating and review system
- AI-based doctor recommendations by condition

### Module 4 — ASHA Workers
- Dashboard for assigned mothers and upcoming visits
- Visit scheduling and checklist management
- Government scheme lookup and eligibility check
- ANC (Antenatal Care) visit tracking

### Module 5 — Baby Dashboard
- Baby profile: name, birth date, weight, blood group
- Milestone tracker
- Vaccination schedule with due dates and completion tracking
- Growth chart (weight and height over time)

### Module 6 — Community
- Forum for Q&A discussions among mothers and caregivers
- Doctor reviews and ratings
- AI-recommended doctors based on health condition

### Module 7 — Wellness
- Personalized daily diet plan based on pregnancy stage
- Gamified maternal health quiz
- Real-time multilingual chat (auto-translates messages)
- Platform feedback and star ratings

### Module 8 — Hospitals
- Hospital directory with specialties, contact, and rating
- Real-time bed availability per ward type
- Hospital services listing with prices
- Booking a hospital service (with insurance integration)
- Booking approval/rejection by hospital admin
- Mentor mothers program (experienced mothers support new ones)
- Digital health records upload and storage (with Cloudinary)

### New Features
- **Insurance Management** — Add policies, view coverage, check if a hospital is in-network, apply at booking
- **Digital Health Passport** — A scannable QR code containing blood type, allergies, conditions, emergency contacts
- **Health Navigation Assistant** — Enter a condition (anemia, hypertension, etc.) and get a step-by-step care journey
- **Emergency SOS** — One-tap button that captures GPS location and emails your doctor, family, and ASHA worker

---

## Project Structure

```
MATRUCARE/
│
├── README.md                 ← This file
├── LICENSE                   ← Project license
│
├── BACKEND/                  ← Node.js + Express REST API server
│   ├── .env                  ← Private configuration (never commit this)
│   ├── .gitignore
│   ├── index.js              ← Server entry: connects DB, starts Socket.IO, mounts routes
│   ├── initAdmin.js          ← One-time script to seed the first admin account
│   ├── package.json
│   │
│   ├── config/               ← Shared config setup
│   │   ├── db.js             ← MongoDB connection using MONGO_URI
│   │   ├── nodemailer.js     ← Email transporter using EMAIL_USER and EMAIL_PASS
│   │   └── cloudinary.js     ← Cloudinary SDK setup for file uploads
│   │
│   ├── controllers/          ← Business logic (29 files — one per feature area)
│   │   ├── authController.js
│   │   ├── doctorController.js
│   │   ├── appointmentController.js
│   │   ├── pregnancyController.js
│   │   ├── babyController.js
│   │   ├── vaccinationController.js
│   │   ├── ashaController.js
│   │   ├── schemeController.js
│   │   ├── teleConsultController.js
│   │   ├── reviewController.js
│   │   ├── forumController.js
│   │   ├── dietController.js
│   │   ├── chatController.js
│   │   ├── quizController.js
│   │   ├── feedbackController.js
│   │   ├── analyticsController.js
│   │   ├── insightsController.js
│   │   ├── reportController.js
│   │   ├── recommendationController.js
│   │   ├── nutritionController.js
│   │   ├── reminderController.js
│   │   ├── hospitalController.js
│   │   ├── hospitalBookingController.js
│   │   ├── healthRecordController.js
│   │   ├── mentorController.js
│   │   ├── insuranceController.js
│   │   ├── healthPassportController.js
│   │   ├── healthNavigationController.js
│   │   └── emergencyController.js
│   │
│   ├── models/               ← Mongoose schemas / MongoDB collections (34 files)
│   │   ├── User.js
│   │   ├── Doctor.js
│   │   ├── Appointment.js
│   │   ├── PregnancyProfile.js
│   │   ├── BabyProfile.js
│   │   ├── BabyVaccination.js
│   │   ├── GrowthRecord.js
│   │   ├── ANCVisit.js
│   │   ├── AshaAssignment.js
│   │   ├── VisitLog.js
│   │   ├── HealthRecord.js
│   │   ├── Report.js
│   │   ├── TeleConsult.js
│   │   ├── Review.js
│   │   ├── ForumPost.js
│   │   ├── ForumComment.js
│   │   ├── DietPlan.js
│   │   ├── Message.js
│   │   ├── QuizQuestion.js
│   │   ├── UserProgress.js
│   │   ├── GlobalFeedback.js
│   │   ├── Medicine.js
│   │   ├── SchemeEligibility.js
│   │   ├── OTPVerification.js
│   │   ├── Vaccination.js
│   │   ├── Hospital.js
│   │   ├── HospitalService.js
│   │   ├── BedAvailability.js
│   │   ├── HospitalBooking.js
│   │   ├── MentorMother.js
│   │   ├── InsurancePolicy.js
│   │   ├── HealthPassport.js
│   │   ├── EmergencyContact.js
│   │   └── EmergencyEvent.js
│   │
│   ├── routes/               ← Route definitions pointing to controllers (28 files)
│   │   └── (one file per feature, named *Routes.js)
│   │
│   └── utils/                ← Shared helpers
│       ├── roleMiddleware.js          ← protect() and authorize() for JWT + role checks
│       ├── notificationScheduler.js   ← Daily 8AM cron: sends appointment reminders
│       ├── reminderScheduler.js       ← Hourly cron: sends 24h advance booking notices
│       └── healthJourneyGenerator.js  ← Generates step-by-step journeys by condition
│
└── FRONTEND/                 ← React 19 + Vite 7 single-page application
    ├── .env                  ← Frontend environment variables (VITE_API_URL)
    ├── .gitignore
    ├── netlify.toml          ← Netlify SPA redirect rule (serves index.html for all routes)
    ├── vite.config.js
    ├── package.json
    ├── index.html
    │
    └── src/
        ├── main.jsx          ← Entry point: renders App into the DOM
        ├── App.jsx           ← All routes defined here + ProtectedRoute logic
        ├── index.css         ← Tailwind base styles + global dark background
        │
        ├── Components/       ← 55 reusable components (see FRONTEND/README.md for full list)
        │   ├── AuthContext.jsx       ← Global JWT auth state (user, token, login, logout)
        │   ├── Navbar.jsx            ← Sticky nav with 3 dropdown menus
        │   ├── Login.jsx / Register.jsx
        │   ├── MotherDashboard.jsx / DoctorDashboard.jsx / AshaWorkerDashboard.jsx / Admin.jsx
        │   ├── HospitalBookingForm.jsx
        │   ├── InsuranceCard.jsx / InsuranceForm.jsx
        │   ├── HealthPassportQR.jsx
        │   ├── EmergencySOSPanel.jsx
        │   ├── HealthNavigationAssistant.jsx
        │   └── ... (50+ more)
        │
        └── Pages/            ← 25 full-page components (see FRONTEND/README.md for full list)
            ├── Home.jsx / About.jsx / Contact.jsx
            ├── Hospitals.jsx / HospitalDetails.jsx / HospitalDashboard.jsx
            ├── Doctors.jsx / TeleConsult.jsx
            ├── HealthDashboard.jsx / BabyDashboard.jsx / HealthRecords.jsx
            ├── InsuranceDashboard.jsx / HealthPassport.jsx / HealthNavigation.jsx
            ├── Chat.jsx / Forum.jsx / Reviews.jsx / Education.jsx
            ├── Analytics.jsx / Insights.jsx
            └── ... (10+ more)
```

---

## Tech Stack

### Backend
| Technology | Version | Why we use it |
|-----------|---------|--------------|
| Node.js | 18+ | JavaScript runtime for the server |
| Express | 5.2.1 | HTTP server and routing framework |
| MongoDB Atlas | Cloud | NoSQL database for all application data |
| Mongoose | 9.2.3 | Schema definitions and database queries |
| jsonwebtoken | 9.0.3 | Generates and verifies JWT auth tokens |
| bcrypt | 6.0.0 | Hashing user passwords before storing |
| nodemailer | 8.0.1 | Sends OTP, confirmation, and alert emails via Gmail |
| cloudinary | 2.9.0 | Cloud storage for uploaded images and documents |
| multer | 2.1.0 | Parses file uploads before sending to Cloudinary |
| socket.io | 4.8.3 | WebSocket server for real-time chat and SOS |
| node-cron | 4.2.1 | Schedules daily reminder emails and notifications |
| google-translate-api-x | 10.7.2 | Auto-translates chat messages |
| helmet | 8.1.0 | Adds security HTTP headers to every response |
| cors | 2.8.6 | Allows only the frontend URL to call the API |
| dotenv | 17.3.1 | Loads .env variables into process.env |
| nodemon | 3.1.14 | Dev tool: auto-restarts server on file save |

### Frontend
| Technology | Version | Why we use it |
|-----------|---------|--------------|
| React | 19.2.0 | Component-based UI framework |
| Vite | 7.3.1 | Build tool with instant hot reload |
| react-router-dom | 7.13.1 | Client-side routing and navigation |
| tailwindcss | 4.2.1 | Utility CSS — used for all styling |
| framer-motion | 12.34.4 | Animations (page enter, card hover effects) |
| axios | 1.13.6 | HTTP client for calling the backend API |
| lucide-react | 0.576.0 | Icon library (500+ icons) |
| socket.io-client | 4.8.3 | WebSocket client for real-time chat and SOS |
| react-qr-code | 2.0.18 | QR code generator for the Health Passport |
| @jitsi/react-sdk | 1.4.4 | Embeds Jitsi Meet video calls in the browser |
| i18next | 25.8.13 | Internationalization library |
| react-i18next | 16.5.4 | React bindings for i18next |

---

## How to Run the Project Locally

You need to run two separate servers — one for the backend, one for the frontend.

### Step 1 — Prerequisites

Make sure these are installed on your machine:
- **Node.js** version 18 or higher (download from nodejs.org)
- **npm** (comes with Node.js)
- A **MongoDB Atlas** account (free tier works fine)
- A **Gmail** account with App Password enabled
- A **Cloudinary** account (free tier works fine)

### Step 2 — Clone the repository

```bash
git clone https://github.com/amangupta9454/maacare.git
cd maacare
```

### Step 3 — Set up and start the Backend

```bash
cd BACKEND
npm install
```

Create a `.env` file inside `BACKEND/` with the following contents (fill in real values):

```env
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/maacare
PORT=5000
JWT_SECRET=any_long_random_secret_string_here_32_chars_minimum
EMAIL_USER=youremail@gmail.com
EMAIL_PASS=your_16_char_gmail_app_password
EMAIL_FROM=youremail@gmail.com
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
FRONTEND_URL=http://localhost:5173
ADMIN_EMAIL=admin@yourdomain.com
ADMIN_PASSWORD=SecureAdminPassword123
ADMIN_NAME=Your Name
YOUTUBE_API_KEY=your_youtube_data_api_v3_key
GETFORM_ENDPOINT=https://getform.io/f/your_form_endpoint
```

```bash
npm run dev
```

The backend will start at **http://localhost:5000**

### Step 4 — Set up and start the Frontend

Open a new terminal window:

```bash
cd FRONTEND
npm install
```

Create a `.env` file inside `FRONTEND/` with:

```env
VITE_API_URL=http://localhost:5000/api
```

```bash
npm run dev
```

The frontend will start at **http://localhost:5173**

### Step 5 — Create the Admin account (first time only)

```bash
cd BACKEND
npm run init-admin
```

This uses `ADMIN_EMAIL`, `ADMIN_PASSWORD`, and `ADMIN_NAME` from `.env` to create the Admin user in MongoDB.

### Step 6 — Open the app

Go to **http://localhost:5173** in your browser. You can now register as a Mother, Doctor, ASHA Worker, or Hospital — or sign in with the Admin account you just created.

---

## Environment Variables

### Backend (`BACKEND/.env`)

| Variable | Required | What it does | Where to get it |
|----------|----------|-------------|-----------------|
| `MONGO_URI` | Yes | MongoDB connection string | MongoDB Atlas → Connect → Connect your application |
| `PORT` | No | Port the server runs on (default: 5000) | Set to any free port |
| `JWT_SECRET` | Yes | Signs and verifies JWT tokens | Any random 32+ char string |
| `EMAIL_USER` | Yes | Gmail address that sends emails | Your Gmail address |
| `EMAIL_PASS` | Yes | Gmail App Password (not your login password) | myaccount.google.com/apppasswords |
| `EMAIL_FROM` | Yes | "From" address shown in sent emails | Same as EMAIL_USER |
| `CLOUDINARY_CLOUD_NAME` | Yes | Cloudinary account name | cloudinary.com → Dashboard |
| `CLOUDINARY_API_KEY` | Yes | Cloudinary API key | cloudinary.com → Dashboard |
| `CLOUDINARY_API_SECRET` | Yes | Cloudinary API secret | cloudinary.com → Dashboard |
| `FRONTEND_URL` | Yes | Frontend URL for CORS whitelist | `http://localhost:5173` (dev) or Netlify URL (prod) |
| `ADMIN_EMAIL` | Yes | Email for the admin account (used by initAdmin.js) | Choose your own |
| `ADMIN_PASSWORD` | Yes | Password for the admin account | Choose a strong password |
| `ADMIN_NAME` | Yes | Display name for admin | Choose your own |
| `YOUTUBE_API_KEY` | Yes | Fetches videos for the Education page | console.developers.google.com |
| `GETFORM_ENDPOINT` | Yes | Receives contact form submissions | getform.io |

### Frontend (`FRONTEND/.env`)

| Variable | Required | What it does |
|----------|----------|-------------|
| `VITE_API_URL` | Yes | Base URL for all API calls. Must end with `/api`. Example: `http://localhost:5000/api` |

---

## All API Routes (Summary)

Every route starts with `/api`. Full documentation is in `BACKEND/README.md`.

| Route prefix | What it handles |
|-------------|----------------|
| `/api/auth` | Register, login, OTP, password reset |
| `/api/doctors` | Doctor profiles and search |
| `/api/appointments` | Book and manage appointments |
| `/api/pregnancy` | Pregnancy profile and tracking |
| `/api/baby` | Baby profile and milestones |
| `/api/baby-vaccines` | Vaccination schedules |
| `/api/teleconsult` | Video consultation booking and rooms |
| `/api/reviews` | Doctor reviews and ratings |
| `/api/forum` | Community forum posts and comments |
| `/api/diet` | Daily diet plans |
| `/api/chat` | Real-time multilingual chat |
| `/api/quiz` | Maternal health quiz |
| `/api/feedback` | Platform feedback submission |
| `/api/schemes` | Government welfare schemes |
| `/api/analytics` | Admin platform statistics |
| `/api/insights` | Detailed admin insights |
| `/api/asha` | ASHA worker operations |
| `/api/health-records` | Health record upload and view |
| `/api/hospitals` | Hospital listing and registration |
| `/api/hospital-bookings` | Service booking and approval |
| `/api/mentors` | Mentor mothers program |
| `/api/insurance` | Insurance policy management |
| `/api/health-passport` | Digital health passport |
| `/api/navigation` | Step-by-step health navigation |
| `/api/emergency` | SOS alerts and emergency contacts |

---

## User Roles and Permissions

There are 5 roles in the system. The role is chosen at registration and is embedded in the JWT token.

| Feature | Mother | Doctor | ASHA | Hospital | Admin |
|---------|:------:|:------:|:----:|:--------:|:-----:|
| Register / Login | Yes | Yes | Yes | Yes | (created via script) |
| Health Dashboard | Yes | No | No | No | No |
| Book Appointment | Yes | No | No | No | No |
| Manage Insurance | Yes | Yes | Yes | No | No |
| Health Passport | Yes | Yes | Yes | No | No |
| Emergency SOS | Yes | Yes | Yes | No | No |
| Book Hospital Service | Yes | No | No | No | No |
| Doctor Panel | No | Yes | No | No | No |
| Teleconsult | Yes | Yes | No | No | No |
| ASHA Panel | No | No | Yes | No | No |
| Hospital Dashboard | No | No | No | Yes | No |
| Approve Bookings | No | No | No | Yes | No |
| Admin Panel | No | No | No | No | Yes |
| Platform Analytics | No | No | No | No | Yes |

---

## How Each Major Feature Works

### Insurance at Booking
When a user opens the Hospital Booking Form, the frontend loads all of their saved insurance policies from the backend and shows them in a dropdown. When a policy is selected, the estimated cost is calculated automatically. The selected policy ID and cost are saved with the booking record.

### Digital Health Passport + QR
The user fills in their blood type, allergies, chronic conditions, doctor name and contact, and insurance provider. This is saved in MongoDB as a `HealthPassport` document. On the frontend, `react-qr-code` converts the passport data into a QR code image. Any first responder can scan this QR with a phone and instantly see the patient's critical medical data — without needing internet access to a database.

### Emergency SOS
The user taps the SOS button. The browser asks for location permission via `navigator.geolocation`. The coordinates are sent to `POST /api/emergency/sos`. The backend saves an `EmergencyEvent` in MongoDB, pulls the user's saved emergency contacts, and sends an email via Nodemailer. The email includes a Google Maps link to the user's exact coordinates.

### Health Navigation Assistant
The user types a condition (e.g., "anemia" or "gestational diabetes"). The frontend calls `GET /api/navigation/journey?condition=anemia`. The `healthJourneyGenerator.js` utility matches the condition and returns an ordered array of steps (visit ASHA worker, go for blood test, see gynecologist, etc.). Each step has a title, description, type, and icon.

### Real-Time Chat
Both sender and receiver connect to the backend Socket.IO server when they open the chat page. When A sends a message, the POST request saves it to MongoDB. The backend also calls `io.to(receiverSocketId).emit('receive_message', data)`. The receiver's browser receives this event and appends the message to their chat window instantly.

### Teleconsultation
The user books a session via the API. The backend creates a `TeleConsult` record with a unique `sessionId`. Both the mother and doctor navigate to `/teleconsult/room/:consultId`. The `TeleConsultRoom` component uses `@jitsi/react-sdk` to embed a Jitsi Meet room named `maacare_<sessionId>`. Both users join the same room and can video call directly in the browser.

---

## Deployment

### Frontend — Netlify

1. Push code to GitHub
2. Go to netlify.com → New Site → Connect GitHub repo
3. Set **Base directory** to `FRONTEND`
4. Set **Build command** to `npm run build`
5. Set **Publish directory** to `FRONTEND/dist`
6. Add environment variable: `VITE_API_URL = https://your-backend.com/api`
7. Deploy

The `netlify.toml` in the FRONTEND folder already handles SPA routing.

### Backend — Any Node.js host (Render, Railway, etc.)

1. Push code to GitHub
2. Create a new Web Service on Render (or similar)
3. Set root directory to `BACKEND`
4. Set start command to `node index.js`
5. Add all backend environment variables in the hosting dashboard
6. After deploying, copy the backend URL and set it as `VITE_API_URL` in Netlify

---

## Package Dependencies

### Backend — Key packages

| Package | Version |
|---------|---------|
| express | ^5.2.1 |
| mongoose | ^9.2.3 |
| bcrypt | ^6.0.0 |
| jsonwebtoken | ^9.0.3 |
| nodemailer | ^8.0.1 |
| cloudinary | ^2.9.0 |
| multer | ^2.1.0 |
| socket.io | ^4.8.3 |
| node-cron | ^4.2.1 |
| google-translate-api-x | ^10.7.2 |
| helmet | ^8.1.0 |
| cors | ^2.8.6 |
| dotenv | ^17.3.1 |
| nodemon (dev) | ^3.1.14 |

### Frontend — Key packages

| Package | Version |
|---------|---------|
| react | ^19.2.0 |
| react-dom | ^19.2.0 |
| react-router-dom | ^7.13.1 |
| axios | ^1.13.6 |
| tailwindcss | ^4.2.1 |
| framer-motion | ^12.34.4 |
| lucide-react | ^0.576.0 |
| socket.io-client | ^4.8.3 |
| react-qr-code | ^2.0.18 |
| @jitsi/react-sdk | ^1.4.4 |
| i18next | ^25.8.13 |
| react-i18next | ^16.5.4 |
| vite (dev) | ^7.3.1 |

---

## Available Scripts

### Backend (`cd BACKEND`)

| Command | What it does |
|---------|-------------|
| `npm run dev` | Start with nodemon (auto-restarts on file changes) |
| `npm start` | Start normally for production |
| `npm run init-admin` | Create the first admin user from .env values |

### Frontend (`cd FRONTEND`)

| Command | What it does |
|---------|-------------|
| `npm run dev` | Start Vite dev server on http://localhost:5173 |
| `npm run build` | Build for production (output goes to `dist/`) |
| `npm run preview` | Preview the production build locally |
| `npm run lint` | Run ESLint to check for code issues |

---

## Contact

**Aman Gupta** — Full-Stack Developer

- Email: [ag0567688@gmail.com](mailto:ag0567688@gmail.com)
- LinkedIn: [linkedin.com/in/amangupta9454](https://linkedin.com/in/amangupta9454)
- GitHub: [github.com/amangupta9454](https://github.com/amangupta9454)
- Portfolio: [gupta-aman-portfolio.netlify.app](http://gupta-aman-portfolio.netlify.app/)
