# MaaCare Backend

**Node.js + Express + MongoDB REST API**

This is the backend server for MaaCare — a maternal healthcare platform. It handles all data storage, authentication, file uploads, emails, real-time communication, and scheduled jobs.

---

## How to Run

```bash
# Step 1: Enter the backend folder
cd BACKEND

# Step 2: Install all packages
npm install

# Step 3: Create a .env file (copy the template below and fill values)
# See the Environment Variables section for all required fields

# Step 4: Start the development server (auto-restarts on file changes)
npm run dev

# OR start for production
npm start
```

The server will start on **http://localhost:5000** by default.

---

## Environment Variables — Complete .env Template

Create a file named `.env` inside the `BACKEND/` folder and fill in all values:

```env
# ─── Database ─────────────────────────────────────────────────────
# Get this from MongoDB Atlas → Connect → Connect your application
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/maacare

# ─── Server ───────────────────────────────────────────────────────
PORT=5000

# ─── JSON Web Token ───────────────────────────────────────────────
# Use any long random string (32+ characters). You can generate one at:
# https://generate-secret.vercel.app/64
JWT_SECRET=your_super_secret_jwt_key_here_make_it_long

# ─── Email (Gmail) ────────────────────────────────────────────────
# The Gmail address you want to send emails FROM
EMAIL_USER=youremail@gmail.com

# IMPORTANT: This is NOT your Google account password.
# This is a 16-character App Password. Generate it at:
# https://myaccount.google.com/apppasswords
# (Requires 2-Step Verification to be enabled first)
EMAIL_PASS=xxxx xxxx xxxx xxxx

# The "From" name shown in emails (can be same as EMAIL_USER)
EMAIL_FROM=youremail@gmail.com

# ─── Cloudinary (for image/file uploads) ──────────────────────────
# Get these from https://cloudinary.com → Dashboard
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key_number
CLOUDINARY_API_SECRET=your_api_secret_string

# ─── CORS (Frontend URL) ──────────────────────────────────────────
# During development, use your Vite dev server URL
# During production, use your deployed Netlify URL
FRONTEND_URL=http://localhost:5173

# ─── Admin Account (auto-created on first run via initAdmin.js) ───
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=YourSecureAdminPassword123
ADMIN_NAME=Your Admin Name

# ─── YouTube API (for Education page videos) ──────────────────────
# Get this from https://console.developers.google.com
# Create a project → Enable YouTube Data API v3 → Create API Key
YOUTUBE_API_KEY=AIzaSy_your_youtube_api_key_here

# ─── Contact Form (Getform.io) ────────────────────────────────────
# Sign up at https://getform.io, create a form and copy the endpoint
GETFORM_ENDPOINT=https://getform.io/f/your_form_id
```

> **Where is `.env` used?**
> Every controller and utility that sends email, uploads files, signs tokens, or connects to MongoDB reads these values via `process.env.VARIABLE_NAME`. The file is loaded at startup by `dotenv` in `index.js`.

---

## Complete Folder & File Structure

```
BACKEND/
│
├── .env                          ← Your private environment variables (NEVER commit this)
├── .gitignore                    ← Ignores .env, node_modules, initAdmin.js
├── index.js                      ← Main entry point: starts server, connects DB, mounts all routes
├── initAdmin.js                  ← One-time script to create the first admin user
├── package.json                  ← Project metadata and npm scripts
├── package-lock.json             ← Locked dependency versions
│
├── config/                       ← Configuration files (2 files)
│   ├── db.js                     ← Connects to MongoDB Atlas using MONGO_URI
│   ├── nodemailer.js             ← Creates reusable Nodemailer email transporter
│   └── cloudinary.js             ← Configures Cloudinary with API credentials
│
├── controllers/                  ← Business logic (29 files — one per feature)
│   ├── authController.js         ← Register, login, OTP verify, password reset
│   ├── doctorController.js       ← Create/update doctor profiles, search by specialty
│   ├── appointmentController.js  ← Book, cancel, list, status-update appointments
│   ├── pregnancyController.js    ← Create/update pregnancy profiles, risk scoring
│   ├── reportController.js       ← Upload and retrieve medical reports
│   ├── ashaController.js         ← ASHA worker operations, visit and assignment
│   ├── babyController.js         ← Baby profile CRUD, milestone tracking
│   ├── vaccinationController.js  ← Baby vaccination schedule and status updates
│   ├── teleConsultController.js  ← Video session booking and room management
│   ├── reviewController.js       ← Submit and retrieve doctor reviews/ratings
│   ├── forumController.js        ← Forum posts, replies, likes
│   ├── dietController.js         ← Generate and fetch personalized diet plans
│   ├── chatController.js         ← Send/receive chat messages with auto-translation
│   ├── quizController.js         ← Fetch quiz questions, submit answers
│   ├── feedbackController.js     ← Submit and fetch platform feedback
│   ├── schemeController.js       ← Government welfare scheme search and eligibility
│   ├── analyticsController.js    ← Platform-wide statistics for admin dashboard
│   ├── insightsController.js     ← Detailed admin insights and reports
│   ├── recommendationController.js ← AI-style doctor recommendations by condition
│   ├── nutritionController.js    ← Nutrition tips and plans
│   ├── reminderController.js     ← Health reminder management
│   ├── hospitalController.js     ← Hospital registration, search, bed management
│   ├── hospitalBookingController.js ← Service bookings with insurance, status updates
│   ├── healthRecordController.js ← Digital health record upload and retrieval
│   ├── mentorController.js       ← Mentor mothers program management
│   ├── insuranceController.js    ← Insurance policy CRUD, hospital coverage check
│   ├── healthPassportController.js ← Digital health passport create/update/fetch
│   ├── healthNavigationController.js ← Step-by-step health journey generation
│   └── emergencyController.js    ← SOS trigger, emergency contact management
│
├── models/                       ← MongoDB Schemas via Mongoose (34 files)
│   ├── User.js                   ← Core user: name, email, password, role, isVerified, OTP
│   ├── Doctor.js                 ← Doctor profile: specialization, qualifications, availability
│   ├── Appointment.js            ← Appointment: userId, doctorId, date, time, type, status
│   ├── PregnancyProfile.js       ← Pregnancy: dueDate, weeks, trimester, riskLevel
│   ├── BabyProfile.js            ← Baby: name, birthDate, weight, height, bloodGroup
│   ├── BabyVaccination.js        ← Vaccination: babyId, vaccine, dueDate, isDone
│   ├── HealthRecord.js           ← Records: userId, type, fileUrl, aiSummary, date
│   ├── Report.js                 ← Medical reports with Cloudinary file links
│   ├── ANCVisit.js               ← Antenatal Care visit logs
│   ├── AshaAssignment.js         ← ASHA worker assignments to mothers
│   ├── VisitLog.js               ← ASHA visit checklist logs
│   ├── TeleConsult.js            ← Video session: userId, doctorId, sessionId, status
│   ├── Review.js                 ← Doctor review: userId, doctorId, rating, comment
│   ├── ForumPost.js              ← Forum post: title, content, userId, category, tags
│   ├── ForumComment.js           ← Comment on a forum post
│   ├── DietPlan.js               ← Weekly diet plan with meals and completion tracking
│   ├── Message.js                ← Chat message: from, to, text, translatedText, timestamp
│   ├── QuizQuestion.js           ← Quiz: question, options, correctAnswer, category
│   ├── UserProgress.js           ← User quiz and education progress
│   ├── GlobalFeedback.js         ← Platform feedback: rating, comment, category
│   ├── GrowthRecord.js           ← Baby growth records: date, weight, height
│   ├── Medicine.js               ← Medicine reminders for mothers
│   ├── SchemeEligibility.js      ← Government scheme eligibility results per user
│   ├── OTPVerification.js        ← OTP storage for password reset (separate collection)
│   ├── Vaccination.js            ← Generic vaccination records (for mothers)
│   ├── Hospital.js               ← Hospital: name, address, specialties, adminId
│   ├── HospitalService.js        ← Hospital services: name, price, category
│   ├── BedAvailability.js        ← Real-time bed count tracking per hospital ward
│   ├── HospitalBooking.js        ← Booking: userId, hospitalId, serviceId, insurance, status
│   ├── MentorMother.js           ← Mentor: userId, experience, languages, availability
│   ├── InsurancePolicy.js        ← Insurance: userId, provider, policyNumber, coverage, hospitals
│   ├── HealthPassport.js         ← Passport: userId, bloodGroup, allergies, conditions, doctor
│   ├── EmergencyContact.js       ← Emergency contact: userId, doctor, family, ASHA, ambulance
│   └── EmergencyEvent.js         ← SOS event log: userId, location, timestamp, isResolved
│
├── routes/                       ← Express route definitions (28 files)
│   ├── authRoutes.js             ← POST /register, /login, /verify-otp, /forget-password
│   ├── doctorRoutes.js           ← GET /doctors, GET /:id, POST /, PUT /:id
│   ├── appointmentRoutes.js      ← POST /, GET /my, PUT /:id/status
│   ├── pregnancyRoutes.js        ← POST /, GET /, PUT /
│   ├── reportRoutes.js           ← POST /upload, GET /my
│   ├── ashaRoutes.js             ← ASHA assignments, visits, dashboard
│   ├── babyRoutes.js             ← Baby profile CRUD
│   ├── vaccinationRoutes.js      ← Vaccination schedule and status
│   ├── teleConsultRoutes.js      ← Teleconsult booking and room join
│   ├── reviewRoutes.js           ← Submit and fetch reviews
│   ├── forumRoutes.js            ← Forum posts, comments, likes
│   ├── dietRoutes.js             ← Diet plan fetch and meal toggle
│   ├── chatRoutes.js             ← Contacts, message history, send message
│   ├── quizRoutes.js             ← Quiz questions and answer submission
│   ├── feedbackRoutes.js         ← Submit and fetch global feedback
│   ├── schemeRoutes.js           ← Government scheme directory
│   ├── analyticsRoutes.js        ← Admin stats (user count, booking totals, etc.)
│   ├── insightsRoutes.js         ← Detailed admin insights
│   ├── healthRoutes.js           ← Health monitoring data
│   ├── recommendationRoutes.js   ← Recommended doctors by condition
│   ├── hospitalRoutes.js         ← Hospital list, detail, register, emergency alert
│   ├── hospitalBookingRoutes.js  ← Create/view/approve bookings
│   ├── healthRecordRoutes.js     ← Upload/retrieve health records
│   ├── mentorRoutes.js           ← Mentor mother profiles and matching
│   ├── insuranceRoutes.js        ← Policy CRUD and coverage check
│   ├── healthPassportRoutes.js   ← Passport get and upsert
│   ├── navigationRoutes.js       ← Health journey by condition
│   └── emergencyRoutes.js        ← SOS trigger, emergency contacts
│
├── middleware/                   ← Custom Express middleware
│   └── (roleMiddleware is in utils — see below)
│
└── utils/                        ← Shared utilities and helpers
    ├── roleMiddleware.js          ← protect() — jwt.verify | authorize(...roles) — role check
    ├── notificationScheduler.js   ← Cron: 8AM daily email reminders for appointments
    ├── reminderScheduler.js       ← Cron: 24-hour advance booking reminders
    └── healthJourneyGenerator.js  ← Generates step-by-step care journeys by condition
```

---

## All API Endpoints

### Authentication  `/api/auth`

| Method | Path | What it does | Who can call |
|--------|------|--------------|-------------|
| POST | `/register` | Create new account. Sends OTP to email | Anyone |
| POST | `/login` | Login with email + password. Returns JWT token | Anyone |
| POST | `/verify-otp` | Verify the 6-digit OTP to activate account | Anyone |
| POST | `/resend-otp` | Request a new OTP if the first one expired | Anyone |
| POST | `/forget-password` | Send password reset OTP to email | Anyone |
| POST | `/reset-password` | Set a new password using OTP | Anyone |
| GET | `/me` | Get the currently logged-in user's data | Logged in |

### Doctors  `/api/doctors`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| GET | `/` | List all doctors (supports `?specialization=Gynecologist&name=x`) | No |
| GET | `/:id` | Get full doctor profile by ID | No |
| POST | `/` | Create a doctor profile (linked to logged-in User account) | Doctor |
| PUT | `/:id` | Update qualifications, availability, fees etc. | Doctor |

### Appointments  `/api/appointments`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| POST | `/` | Book a new appointment with a doctor | Mother |
| GET | `/my` | List all appointments for the logged-in user | Any |
| GET | `/:id` | Get full appointment details by ID | Any |
| PUT | `/:id/status` | Doctor approves/rejects/completes appointment | Doctor |

### Hospitals  `/api/hospitals`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| GET | `/` | List all registered hospitals | No |
| GET | `/:id` | Full hospital detail with services and bed counts | No |
| POST | `/register` | Register a new hospital (creates hospital profile) | Admin |
| PUT | `/dashboard/me` | Hospital admin updates their own hospital info | Hospital |
| POST | `/emergency` | Send an emergency alert to a hospital | No |

### Hospital Bookings  `/api/hospital-bookings`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| POST | `/` | Create a booking for a hospital service | Any |
| GET | `/my` | User sees their own bookings | Any |
| GET | `/hospital-bookings` | Hospital sees all bookings received | Hospital |
| PUT | `/:id/status` | Hospital approves or rejects a booking | Hospital |

### Insurance  `/api/insurance`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| POST | `/` | Add a new insurance policy for the user | Any |
| GET | `/` | Get all insurance policies for the logged-in user | Any |
| DELETE | `/:id` | Delete one insurance policy | Any |
| GET | `/:id/coverage` | Check if a hospital is in the policy network | Any |

**Example coverage check:**
```
GET /api/insurance/64abc123/coverage?hospitalName=Apollo Hospitals
Response: { isCovered: true, hospitalName: "Apollo Hospitals" }
```

### Health Passport  `/api/health-passport`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| POST | `/` | Create or update the user's health passport | Any |
| GET | `/` | Fetch the user's current health passport | Any |

### Navigation  `/api/navigation`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| GET | `/journey` | Get step-by-step journey for a health condition | Any |

**Example:**
```
GET /api/navigation/journey?condition=anemia
Response: { journey: [ { step: 1, title: "Visit ASHA Worker", description: "...", type: "visit" } ] }
```

### Emergency  `/api/emergency`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| POST | `/sos` | Trigger SOS — saves location, sends email alert | Any |
| POST | `/contacts` | Save or update emergency contacts | Any |
| GET | `/contacts` | Fetch user's saved emergency contacts | Any |

### Chat  `/api/chat`

| Method | Path | What it does | Auth |
|--------|------|--------------|------|
| GET | `/contacts` | Get list of users the logged-in user has chatted with | Any |
| GET | `/:userId` | Load message history with a specific user | Any |
| POST | `/send` | Send a message (auto-translated if languages differ) | Any |

### Other Feature Routes

| Route prefix | What it handles |
|-------------|----------------|
| `/api/pregnancy` | Pregnancy profile CRUD and risk assessment |
| `/api/baby` | Baby profile and milestone tracking |
| `/api/baby-vaccines` | Baby vaccination schedule |
| `/api/teleconsult` | Video session booking and room management |
| `/api/reviews` | Doctor reviews and ratings |
| `/api/forum` | Community forum posts and comments |
| `/api/diet` | Daily personalized diet plans |
| `/api/quiz` | Gamified maternal health quiz |
| `/api/feedback` | Platform feedback submission and display |
| `/api/schemes` | Government welfare schemes directory |
| `/api/analytics` | Admin platform stats |
| `/api/insights` | Detailed admin insights |
| `/api/asha` | ASHA worker assignments and visits |
| `/api/health-records` | Digital health record upload/retrieval |
| `/api/mentors` | Mentor mothers program |

---

## How Authentication Works

Every protected route requires a **Bearer token** in the request header:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

The `protect` middleware in `utils/roleMiddleware.js` does this:

1. Reads the `Authorization` header from the request
2. Extracts the token after `Bearer `
3. Verifies it using `jwt.verify(token, process.env.JWT_SECRET)`
4. If valid: attaches the decoded user object to `req.user` and calls `next()`
5. If invalid or missing: returns `401 Unauthorized`

The `authorize(...roles)` middleware then checks `req.user.role` matches the allowed roles:

```js
// Example: Only Doctor role can access this route
router.put("/:id/status", protect, authorize("Doctor"), updateStatus);

// Example: Any logged-in user can access this route
router.get("/my", protect, getMyBookings);
```

---

## How Email Works

All emails go through **Gmail SMTP via Nodemailer**.

The `config/nodemailer.js` creates a transporter using `EMAIL_USER` and `EMAIL_PASS` from `.env`.

**Emails sent automatically:**
- OTP for registration and password reset
- Appointment booking confirmation (to patient + doctor)
- Hospital booking confirmation with insurance cost breakdown
- Hospital booking status update (Approved / Rejected)
- Emergency SOS alert with GPS location link
- Daily health reminders (via cron job at 8AM)
- 24-hour advance reminder before bookings (via cron job)

---

## How File Uploads Work

File uploads use **Multer** (temporarily stores in memory) + **Cloudinary** (permanent cloud storage).

Flow:
1. Frontend sends a `multipart/form-data` request with a file field
2. Multer middleware processes the file into `req.file`
3. The controller uploads `req.file.buffer` to Cloudinary
4. Cloudinary returns a permanent `secure_url`
5. The URL is saved in MongoDB (in the relevant model's `fileUrl` or `documentUrl` field)

Used for: Profile images, health record documents, insurance documents, doctor qualification PDFs.

---

## Real-Time (Socket.IO)

The Socket.IO server is attached to the same HTTP server as Express. The instance is stored in `app.set('io', io)` so controllers can access it.

```js
// In controllers, emit an event to a specific user
const io = req.app.get('io');
io.to(socketRoomId).emit('receive_message', messageData);
```

Used for:
- **Real-time chat** — messages appear instantly without page reload
- **Emergency SOS** — can broadcast alerts to admin/hospital rooms

---

## Background Jobs (Cron)

Two schedulers run automatically when the server starts:

```
notificationScheduler.js → Runs at 8:00 AM every day
  - Queries appointments scheduled for the next 24 hours
  - Queries vaccinations due soon (baby vaccinations)
  - Sends reminder emails to the relevant users

reminderScheduler.js → Runs every hour
  - Queries confirmed hospital bookings
  - Finds bookings where (preferredDate - now) <= 24 hours
  - Sends "Your appointment is tomorrow" email
```

---

## Error Response Format

All error responses follow this format:

```json
{
  "success": false,
  "message": "A clear description of what went wrong"
}
```

| HTTP Code | When it's returned |
|-----------|-------------------|
| 200 | Successful request |
| 201 | Resource created successfully |
| 400 | Missing required fields, validation failed |
| 401 | No token provided or token is invalid/expired |
| 403 | Token is valid but user role is not permitted |
| 404 | Requested resource does not exist in DB |
| 500 | Unhandled server error (check server console logs) |

---

## npm Scripts

```bash
npm run dev      # Start with nodemon (auto-restarts when you save a file)
npm start        # Start normally with node (for production)
```
