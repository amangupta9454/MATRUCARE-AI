# MaaCare Frontend

**React 19 + Vite 7 + Tailwind CSS 4**

This is the frontend web application for MaaCare — a maternal healthcare platform. It is a Single Page Application (SPA) built with React, with client-side routing, global auth state, real-time Socket.IO, and a dark glassmorphic UI.

---

## How to Run

```bash
# Step 1: Enter the frontend folder
cd FRONTEND

# Step 2: Install all packages
npm install

# Step 3: Create a .env file (copy the template below)
# See the Environment Variables section for all required fields

# Step 4: Start the development server
npm run dev
```

Open **http://localhost:5173** in your browser.

---

## Environment Variables — Complete .env Template

Create a file named `.env` inside the `FRONTEND/` folder:

```env
# ─── Backend API URL ──────────────────────────────────────────────────────────
# This points to your running Express backend.
# IMPORTANT: Include /api at the end — all API calls rely on this.
#
# For local development:
VITE_API_URL=http://localhost:5000/api
#
# For production (replace with your actual deployed backend URL):
# VITE_API_URL=https://your-backend-domain.onrender.com/api
```

> **Why only one variable?**
> All backend communication goes through a single base URL. The frontend never hardcodes `localhost:5000` anywhere — every API call builds its URL from `import.meta.env.VITE_API_URL`.

> **What does VITE_ prefix mean?**
> In Vite, only variables that start with `VITE_` are exposed to the browser. Other variables in `.env` would be ignored by the frontend.

---

## Complete Folder & File Structure

```
FRONTEND/
│
├── .env                          ← Your private environment variables
├── .gitignore                    ← Ignores node_modules, dist/, .env
├── netlify.toml                  ← Tells Netlify to serve index.html for all routes
├── vite.config.js                ← Vite build configuration
├── package.json                  ← Project metadata and npm scripts
├── package-lock.json             ← Locked dependency versions
├── index.html                    ← HTML shell — Vite injects the React app here
│
└── src/                          ← All application source code
    │
    ├── main.jsx                  ← Entry point: renders <App /> into index.html
    ├── App.jsx                   ← Root component: defines all routes + protected route guard
    ├── index.css                 ← Global CSS: Tailwind base + custom styles
    │
    ├── assets/                   ← Static assets (images, icons used in the UI)
    │
    ├── utils/                    ← Frontend utility helpers
    │   └── api.js (if exists)    ← Shared axios instance or helper functions
    │
    ├── Components/               ← Reusable UI components (55 files)
    │   │
    │   ├── AuthContext.jsx       ← React Context: global login state, user object, JWT
    │   ├── Navbar.jsx            ← Top navigation bar with 3 dropdown menus (Care, My Health, Explore)
    │   ├── VoiceNavigator.jsx    ← Accessibility: voice command navigation helper
    │   │
    │   ├── ── Auth ──────────────────────────────────────────────────────────────
    │   ├── Login.jsx             ← Email + password login form. Stores JWT in localStorage
    │   ├── Register.jsx          ← Registration form with role selection (Mother/Doctor/ASHA/Hospital)
    │   ├── VerifyOtp.jsx         ← 6-digit OTP input form for account activation
    │   ├── ResendOtp.jsx         ← Button to request a new OTP if the previous one expired
    │   ├── ForgetPassword.jsx    ← Email form to request a password reset OTP
    │   │
    │   ├── ── Dashboards ────────────────────────────────────────────────────────
    │   ├── MotherDashboard.jsx   ← Mother's personal hub: quick links, health overview
    │   ├── DoctorDashboard.jsx   ← Doctor's panel: appointment queue, patient list
    │   ├── AshaWorkerDashboard.jsx ← ASHA operations: assigned mothers, visit schedule
    │   ├── Admin.jsx             ← Admin control panel: user management, platform stats
    │   │
    │   ├── ── Appointments ──────────────────────────────────────────────────────
    │   ├── AppointmentForm.jsx   ← Book a new doctor appointment
    │   │
    │   ├── ── Doctors ───────────────────────────────────────────────────────────
    │   ├── DoctorForm.jsx        ← Doctor profile creation/edit form
    │   ├── DoctorRating.jsx      ← Star-rating component for reviewing a doctor
    │   ├── DoctorAvailabilityCalendar.jsx ← Calendar view of doctor availability
    │   ├── RecommendationCard.jsx ← Card showing an AI-recommended doctor
    │   ├── SpecialistConsultationCard.jsx ← Card for specialist consultation booking
    │   │
    │   ├── ── Pregnancy & Baby ──────────────────────────────────────────────────
    │   ├── PregnancyProfileForm.jsx ← Form to enter/update pregnancy details
    │   ├── PregnancyVideos.jsx   ← Educational video player for pregnancy topics
    │   ├── BabyProfileForm.jsx   ← Baby profile creation form (name, birth date, etc.)
    │   ├── GrowthChart.jsx       ← Visual chart of baby's weight/height over time
    │   │
    │   ├── ── ASHA & Community ──────────────────────────────────────────────────
    │   ├── AshaAssignmentCard.jsx ← Shows a mother assigned to an ASHA worker
    │   ├── MentorMotherCard.jsx  ← Card displaying a mentor mother's profile
    │   ├── FamilyAccessCard.jsx  ← Family member access and sharing settings
    │   │
    │   ├── ── Hospitals ─────────────────────────────────────────────────────────
    │   ├── HospitalCard.jsx      ← Hospital listing card: name, specialties, rating
    │   ├── HospitalServiceCard.jsx ← Individual hospital service with price + book button
    │   ├── HospitalBookingForm.jsx ← Full booking modal: insurance selector, cost estimate
    │   │
    │   ├── ── Insurance & Passport ──────────────────────────────────────────────
    │   ├── InsuranceCard.jsx     ← Displays one insurance policy: provider, coverage, validity
    │   ├── InsuranceForm.jsx     ← Form to add a new insurance policy
    │   ├── HealthPassportQR.jsx  ← Renders a QR code from the health passport data using react-qr-code
    │   │
    │   ├── ── Emergency & Navigation ────────────────────────────────────────────
    │   ├── HealthNavigationAssistant.jsx ← Condition input → step-by-step health journey cards
    │   ├── EmergencySOSPanel.jsx ← Big red SOS button, captures GPS, sends alert via backend
    │   ├── ContactEmergencyCard.jsx ← Form to save doctor, family, ASHA, ambulance contacts
    │   ├── EmergencyButton.jsx   ← Compact emergency trigger button
    │   ├── EmergencyRequestButton.jsx ← Sends emergency request to hospital
    │   ├── EmergencyHospitalAlert.jsx ← Triggers a geolocation-based hospital emergency alert
    │   │
    │   ├── ── Teleconsult ───────────────────────────────────────────────────────
    │   ├── TeleConsultCard.jsx   ← Teleconsult session card with join button
    │   ├── TeleConsultRequest.jsx ← Request form for booking a teleconsult
    │   ├── TeleConsultRoom.jsx   ← Embeds Jitsi Meet for in-browser video call
    │   │
    │   ├── ── Health & Wellness ─────────────────────────────────────────────────
    │   ├── DietPlanner.jsx       ← Today's meal plan with checkboxes to mark meals done
    │   ├── NutritionPlan.jsx     ← Nutritional guidance and plan display
    │   ├── MedicineReminder.jsx  ← Medicine schedule tracker with reminder times
    │   ├── HealthReminderCard.jsx ← Displays upcoming health reminders
    │   ├── HealthHeatmap.jsx     ← Visual heatmap of health activity over time
    │   ├── HealthTimeline.jsx    ← Timeline view of health events and milestones
    │   ├── RiskScoreCard.jsx     ← Shows the pregnancy risk assessment score
    │   │
    │   ├── ── Community & Feedback ──────────────────────────────────────────────
    │   ├── ForumPostCard.jsx     ← Displays a forum post with likes and reply count
    │   ├── ForumComment.jsx      ← Individual comment in a forum thread
    │   ├── ReviewCard.jsx        ← Displays a user review with star rating
    │   ├── SubmitFeedback.jsx    ← 5-star rating + comment form for platform feedback
    │   │
    │   ├── ── Reports & Records ─────────────────────────────────────────────────
    │   ├── ReportUpload.jsx      ← Upload health reports (PDF/image) to Cloudinary
    │   ├── SchemeEligibilityForm.jsx ← Form to check eligibility for govt schemes
    │   └── (5 more components)  ← Additional utility components
    │
    └── Pages/                    ← Full page components rendered by the router (25 files)
        │
        ├── ── Public Pages (no login needed) ────────────────────────────────────
        ├── Home.jsx              ← Landing page: hero section, features, CTAs, stats
        ├── About.jsx             ← About MaaCare platform, mission, team info
        ├── Contact.jsx           ← Contact form (submits to Getform endpoint)
        ├── Doctors.jsx           ← Doctor search and listing page with filters
        ├── Hospitals.jsx         ← Hospital listing page with emergency alert panel
        ├── HospitalDetails.jsx   ← Single hospital: services, beds, doctors, booking form
        ├── MentorCommunity.jsx   ← Browse and connect with mentor mothers
        ├── GovernmentSchemes.jsx ← Government welfare schemes directory and search
        ├── Forum.jsx             ← Community Q&A forum: browse, post, reply
        ├── Reviews.jsx           ← Wall of community feedback and ratings
        ├── Education.jsx         ← Maternal health education with YouTube videos
        ├── RecommendedDoctors.jsx ← AI-based doctor recommendations
        ├── DoctorReviews.jsx     ← Reviews page for a specific doctor
        │
        ├── ── Auth Pages ─────────────────────────────────────────────────────────
        │   (Handled as Components, rendered via routes in App.jsx)
        │   Login, Register, VerifyOtp, ResendOtp, ForgetPassword
        │
        ├── ── Mother-Only Pages ──────────────────────────────────────────────────
        ├── HealthDashboard.jsx   ← Pregnancy tracking hub: vitals, weeks, risk, kick counter
        ├── BabyDashboard.jsx     ← Baby milestones, vaccination tracker, growth chart
        ├── HealthRecords.jsx     ← View and upload digital health records with AI summary
        ├── TeleConsult.jsx       ← Browse doctors, book a video session, join a room
        │
        ├── ── Hospital Pages ─────────────────────────────────────────────────────
        ├── HospitalDashboard.jsx ← Hospital admin: booking management, bed tracking, services
        │
        ├── ── Admin Pages ────────────────────────────────────────────────────────
        ├── Analytics.jsx         ← Platform-wide charts: user growth, bookings, specialties
        ├── Insights.jsx          ← Detailed admin insights: top doctors, active users
        ├── AshaVisits.jsx        ← ASHA worker visit management and schedule
        │
        ├── ── New Feature Pages (require login) ──────────────────────────────────
        ├── InsuranceDashboard.jsx ← Manage insurance policies, check hospital coverage
        ├── HealthPassport.jsx    ← Edit health passport fields, generate and view QR code
        ├── HealthNavigation.jsx  ← Health journey assistant + Emergency SOS hub
        │
        └── Chat.jsx              ← Real-time multilingual chat with contacts list
```

---

## All Routes in the App

The routes are defined in `src/App.jsx`. Here is every route with its component and access level:

### Public Routes (Anyone can access, no login needed)

| URL Path | Page Component | Description |
|----------|---------------|-------------|
| `/` | `Home.jsx` | Landing page |
| `/about` | `About.jsx` | About the platform |
| `/contact` | `Contact.jsx` | Contact form |
| `/doctors` | `Doctors.jsx` | Doctor search |
| `/hospitals` | `Hospitals.jsx` | Hospital listing |
| `/hospitals/:id` | `HospitalDetails.jsx` | Single hospital detail |
| `/mentor-community` | `MentorCommunity.jsx` | Mentor mothers browse |
| `/schemes` | `GovernmentSchemes.jsx` | Welfare schemes |
| `/forum` | `Forum.jsx` | Community forum |
| `/forum/:id` | `Forum.jsx` | Specific forum post |
| `/reviews` | `Reviews.jsx` | Platform reviews |
| `/education` | `Education.jsx` | Maternal health education |
| `/recommended-doctors` | `RecommendedDoctors.jsx` | AI recommendations |
| `/doctors/:doctorId/reviews` | `DoctorReviews.jsx` | Doctor-specific reviews |
| `/login` | `Login.jsx` | Login form |
| `/register` | `Register.jsx` | Registration form |
| `/verify-otp` | `VerifyOtp.jsx` | OTP verification |
| `/resend-otp` | `ResendOtp.jsx` | OTP resend |
| `/forget-password` | `ForgetPassword.jsx` | Password reset request |

### Protected Routes (Login required — redirects to `/login` if not logged in)

| URL Path | Component | Which Roles |
|----------|-----------|-------------|
| `/dashboard/mother` | `MotherDashboard.jsx` | Mother only |
| `/health-dashboard` | `HealthDashboard.jsx` | Mother only |
| `/baby-dashboard` | `BabyDashboard.jsx` | Mother only |
| `/health-records` | `HealthRecords.jsx` | Mother only |
| `/teleconsult` | `TeleConsult.jsx` | Mother, Doctor |
| `/teleconsult/room/:consultId` | `TeleConsultRoom.jsx` | Mother, Doctor |
| `/dashboard/doctor` | `DoctorDashboard.jsx` | Doctor only |
| `/dashboard/asha` | `AshaWorkerDashboard.jsx` | ASHA only |
| `/asha-visits` | `AshaVisits.jsx` | ASHA only |
| `/dashboard/admin` | `Admin.jsx` | Admin only |
| `/dashboard/hospital` | `HospitalDashboard.jsx` | Hospital only |
| `/analytics` | `Analytics.jsx` | Admin only |
| `/insights` | `Insights.jsx` | Admin only |
| `/insurance` | `InsuranceDashboard.jsx` | Mother, Doctor, ASHA |
| `/passport` | `HealthPassport.jsx` | Mother, Doctor, ASHA |
| `/navigation` | `HealthNavigation.jsx` | Mother, Doctor, ASHA |

---

## How Authentication Works in the Frontend

Authentication is managed entirely through `Components/AuthContext.jsx`.

**What it stores:**
- `user` — the decoded JWT payload object `{ _id, name, email, role }`
- `token` — the raw JWT string from localStorage

**How login works:**
1. User submits `Login.jsx` form
2. `axios.post(VITE_API_URL + '/auth/login', { email, password })`
3. Backend returns `{ token, user }`
4. `AuthContext.login(token)` is called
5. Token stored in `localStorage.setItem('token', token)`
6. Token decoded with `jwtDecode` (from `jwt-decode`) → sets `user` in state

**How protected routes work (`ProtectedRoute` in `App.jsx`):**
```jsx
const ProtectedRoute = ({ children, allowedRoles }) => {
  const { user } = useContext(AuthContext);
  if (!user) return <Navigate to="/login" replace />;
  if (allowedRoles && !allowedRoles.includes(user.role)) return <Navigate to="/" replace />;
  return children;
};
```
- No user in context → redirect to `/login`
- User has wrong role → redirect to `/` (home)
- User exists and role matches → show the page

---

## How API Calls Work

Every component that needs backend data follows this pattern:

```jsx
import axios from 'axios';

const token = localStorage.getItem('token');

// GET request example
const res = await axios.get(`${import.meta.env.VITE_API_URL}/hospitals`, {
    headers: { Authorization: `Bearer ${token}` }
});

// POST request example
const res = await axios.post(`${import.meta.env.VITE_API_URL}/insurance`, formData, {
    headers: { Authorization: `Bearer ${token}` }
});
```

**Important rule:** `VITE_API_URL` already ends with `/api`, so individual endpoint paths must NOT include `/api`:

```
Correct:   `${VITE_API_URL}/hospitals`        → http://localhost:5000/api/hospitals
Wrong:     `${VITE_API_URL}/api/hospitals`    → http://localhost:5000/api/api/hospitals (broken!)
```

---

## How the Navbar Works

The Navbar (`Components/Navbar.jsx`) is a sticky top navigation bar. All links are organized into **3 dropdown menus** that open on hover (desktop) or tap (mobile):

```
[Logo + MaaCare]  [Home]  [Care ▾]  [My Health ▾]  [Explore ▾]  [Reviews]  [Contact]  [Login] [Register]
```

**Care Dropdown contains:**
- Hospitals
- Doctors
- TeleConsult
- Mentor Community

**My Health Dropdown contains:**
- Health Dashboard
- Health Records
- Baby Dashboard
- Health Passport
- Insurance

**Explore Dropdown contains:**
- Health Navigation
- Education
- Govt Schemes
- Forum

On mobile, it becomes an accordion menu with the same sections collapsed under expandable headings.

---

## Real-Time Features (Socket.IO)

Two features use Socket.IO for real-time updates:

**Chat (`Pages/Chat.jsx`):**
```js
const socket = io('http://localhost:5000');  // or production URL
socket.emit('join', userId);
socket.on('receive_message', (message) => {
    setMessages(prev => [...prev, message]);
});
```

**Emergency SOS (`Components/EmergencySOSPanel.jsx`):**
- Captures GPS using `navigator.geolocation.getCurrentPosition()`
- POSTs to backend `/api/emergency/sos` with lat/lng
- Backend saves the event and sends email alerts

---

## Netlify Deployment

The `netlify.toml` file at the root of FRONTEND already handles the SPA routing issue on Netlify:

```toml
[[redirects]]
  from = "/*"
  to   = "/index.html"
  status = 200
```

**What this does:** Without this, navigating directly to `/hospitals` or `/insurance` on Netlify would return a 404. This rule tells Netlify to always serve `index.html` and let React Router handle the URL.

**Deployment steps:**
1. Push your code to a GitHub repository
2. Log in to [netlify.com](https://netlify.com) and click "Add new site"
3. Connect your GitHub repo
4. Set these build settings:
   - **Base directory:** `FRONTEND`
   - **Build command:** `npm run build`
   - **Publish directory:** `FRONTEND/dist`
5. Add environment variable: `VITE_API_URL = https://your-backend-domain.com/api`
6. Click Deploy

---

## npm Scripts

```bash
npm run dev      # Start Vite dev server on http://localhost:5173
npm run build    # Create production build (output: dist/ folder)
npm run preview  # Preview the production build locally
npm run lint     # Run ESLint checks on the code
```

---

## Key Packages and What They Do

| Package | What it does in this project |
|---------|------------------------------|
| `react` `react-dom` | The UI framework |
| `react-router-dom` | All the routing (`/hospitals`, `/insurance`, etc.) |
| `axios` | Makes HTTP requests to the backend API |
| `tailwindcss` | All CSS styling — dark glassmorphic design |
| `framer-motion` | Page entry animations and card hover effects |
| `lucide-react` | All icons used in nav, cards, buttons |
| `socket.io-client` | Real-time chat and emergency SOS |
| `react-qr-code` | Generates the QR code in Health Passport page |
| `@jitsi/react-sdk` | Embeds a Jitsi Meet video call in the browser |
| `i18next` `react-i18next` | Internationalization support (multilingual chat) |

---

## Design System

The entire UI uses a **dark glassmorphic** aesthetic:

```
Background:     #030712  (near black)
Cards:          bg-white/5 with border-white/10  (glass effect)
Primary color:  teal-400 / #14b8a6  (buttons, highlights, icons)
Text:           white (headings) + gray-400 (body)
Blur:           backdrop-blur-xl  (glass panels)
Animations:     Framer Motion (fade + slide on enter, scale on hover)
```

All components use **Tailwind CSS utility classes** directly — there are no separate CSS module files.
