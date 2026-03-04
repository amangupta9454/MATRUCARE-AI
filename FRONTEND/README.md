# 🖥️ MaaCare — Frontend Documentation

> **React 19 + Vite 7 + Tailwind CSS 4** powering the MaaCare user interface

---

## 🗂️ Component Tree Overview

![Frontend Architecture](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\frontend_architecture_1772635916606.png)
*React component hierarchy from App.jsx down to leaf components*

---

## 🔀 Application Routing Architecture

```mermaid
flowchart TD
    A([main.jsx]) --> B[App.jsx — BrowserRouter]
    B --> NAV[🔝 Navbar — always visible]
    B --> VOICE[🎤 VoiceNavigator — Accessibility]
    B --> ROUTES[Routes]

    ROUTES --> PUB[Public Routes]
    ROUTES --> PROT[🔐 Protected Routes<br/>ProtectedRoute HOC]

    PUB --> PUB1[/ Home]
    PUB --> PUB2[/about, /contact]
    PUB --> PUB3[/doctors, /hospitals, /hospitals/:id]
    PUB --> PUB4[/mentor-community, /schemes, /forum]
    PUB --> PUB5[/reviews, /education]
    PUB --> PUB6[/login, /register, /verify-otp]

    PROT --> MOTHER[👩 Mother Only]
    PROT --> DOCTOR[🩺 Doctor Only]
    PROT --> ASHA[🌿 ASHA Only]
    PROT --> ADMIN[👑 Admin Only]
    PROT --> ALL[🌐 All Roles]

    MOTHER --> M1[/health-dashboard]
    MOTHER --> M2[/baby-dashboard]
    MOTHER --> M3[/teleconsult]
    DOCTOR --> D1[/dashboard/doctor]
    ASHA --> A1[/dashboard/asha]
    ADMIN --> AD1[/analytics, /insights]
    ALL --> AL1[/insurance, /passport, /navigation]

    style A fill:#14b8a6,color:#000
    style PROT fill:#1a0a0a,stroke:#ef4444,color:#fff
    style PUB fill:#0f2027,stroke:#14b8a6,color:#fff
```

---

## 🎨 Design System

```mermaid
mindmap
  root((MaaCare Design System))
    Colors
      Primary Teal #14b8a6
      Background Black #030712
      Surface white/5
      Border white/10
      Danger red-400
      Success emerald-400
    Typography
      Font Inter ecosystem
      Headings font-extrabold tracking-tight
      Body text-gray-400 leading-relaxed
    Components
      Glassmorphism Cards
        backdrop-blur-xl
        bg-white divided by 5 or 10
        border-white divided by 10
      Buttons
        Primary teal-600 rounded-full
        Secondary border teal-500/30
        Danger red-500/10
    Animations
      Framer Motion
        Page enter fade+slide
        Card hover scale 1.02
        Dropdown slide-in-from-top
    Responsive
      Mobile first
      md breakpoint 768px
      lg breakpoint 1024px
```

---

## 🧩 Component Catalogue

### 🔝 Navigation
| Component | File | Description |
|-----------|------|-------------|
| Navbar | `Navbar.jsx` | Sticky top nav, 3 dropdown menus, mobile accordion, auth-aware |

**Navbar Dropdown Groups:**

```mermaid
graph LR
    NAV[Navbar] --> HOME[Home]
    NAV --> CARE[Care ▾]
    NAV --> HEALTH[My Health ▾]
    NAV --> EXPLORE[Explore ▾]
    NAV --> REVIEWS[Reviews]
    NAV --> CONTACT[Contact]
    NAV --> AUTH[Login / Register]

    CARE --> HOSP[🏥 Hospitals]
    CARE --> DOC[🩺 Doctors]
    CARE --> TELE[📹 TeleConsult]
    CARE --> MENTOR[👥 Mentor Community]

    HEALTH --> HDASH[📊 Health Dashboard]
    HEALTH --> REC[📋 Health Records]
    HEALTH --> BABY[👶 Baby Dashboard]
    HEALTH --> PASS[🆔 Health Passport]
    HEALTH --> INS[🛡️ Insurance]

    EXPLORE --> NAV2[🗺️ Health Navigation]
    EXPLORE --> EDU[📚 Education]
    EXPLORE --> SCHEM[🏛️ Govt Schemes]
    EXPLORE --> FORUM[💬 Forum]

    style NAV fill:#14b8a6,color:#000
    style CARE fill:#0f2027,stroke:#14b8a6,color:#fff
    style HEALTH fill:#0f2027,stroke:#14b8a6,color:#fff
    style EXPLORE fill:#0f2027,stroke:#14b8a6,color:#fff
```

### 🔐 Auth Components
| Component | Description |
|-----------|-------------|
| `Login.jsx` | Email + password with JWT handling |
| `Register.jsx` | Multi-role registration form |
| `VerifyOtp.jsx` | 6-digit OTP code input |
| `ForgetPassword.jsx` | Reset request form |
| `ResendOtp.jsx` | OTP resend trigger |

### 🏥 Hospital Components
| Component | Description |
|-----------|-------------|
| `HospitalCard.jsx` | Hospital listing card with specialties & rating |
| `HospitalServiceCard.jsx` | Individual service + Book button |
| `HospitalBookingForm.jsx` | Full booking with insurance selector + dynamic cost estimate |

### 🆕 New Feature Components
| Component | Description |
|-----------|-------------|
| `InsuranceCard.jsx` | Policy display: provider, validity, coverage amount |
| `InsuranceForm.jsx` | Add policy: type, dates, hospital network, policy # |
| `HealthPassportQR.jsx` | Renders QR from passport JSON using `react-qr-code` |
| `HealthNavigationAssistant.jsx` | Condition search → step-by-step journey cards |
| `EmergencySOSPanel.jsx` | Red SOS button, GPS capture, status updates |
| `ContactEmergencyCard.jsx` | Doctor, family, ASHA contacts form + ambulance # |
| `EmergencyHospitalAlert.jsx` | Nearby hospital quick alert trigger |

### 💬 Community Components
| Component | Description |
|-----------|-------------|
| `DietPlanner.jsx` | Today's meals with completion toggle |
| `SubmitFeedback.jsx` | Star rating + comment form |
| `ReviewCard.jsx` | Display user review card |
| `TeleConsultCard.jsx` | Teleconsult session card |

---

## 📄 Pages Reference

| Page | Route | Role | Key Features |
|------|-------|------|-------------|
| `Home.jsx` | `/` | All | Hero, features showcase, CTAs |
| `Hospitals.jsx` | `/hospitals` | All | Search, emergency alert panel |
| `HospitalDetails.jsx` | `/hospitals/:id` | All | Services, beds, booking form |
| `HospitalDashboard.jsx` | `/dashboard/hospital` | Hospital | Bookings table, approval |
| `HealthDashboard.jsx` | `/health-dashboard` | Mother | Pregnancy vitals tracker |
| `BabyDashboard.jsx` | `/baby-dashboard` | Mother | Milestones + vaccinations |
| `HealthRecords.jsx` | `/health-records` | Mother | AI-summarized medical docs |
| `InsuranceDashboard.jsx` | `/insurance` | All | Policy management, coverage check |
| `HealthPassport.jsx` | `/passport` | All | Edit passport + view QR |
| `HealthNavigation.jsx` | `/navigation` | All | Journey wizard + Emergency hub |
| `TeleConsult.jsx` | `/teleconsult` | Mother/Doctor | Book + join video room |
| `Chat.jsx` | `/chat` | All | Real-time multilingual messaging |
| `Reviews.jsx` | `/reviews` | All | Community feedback wall |
| `Education.jsx` | `/education` | All | Maternal health resources |
| `Forum.jsx` | `/forum` | All | Q&A community |
| `GovernmentSchemes.jsx` | `/schemes` | All | Welfare programs directory |

---

## 🔄 State Management

```mermaid
graph TB
    subgraph GLOBAL["🌍 Global State — AuthContext"]
        CTX[AuthContext Provider]
        USER[user — JWT payload]
        TOKEN[token — localStorage]
        LOGIN[login fn — decode + store]
        LOGOUT[logout fn — clear state]
    end

    subgraph LOCAL["📦 Local State — useState + useEffect"]
        LIST[List data from API]
        FORM[Form field values]
        LOAD[Loading / error states]
        MODAL[Modal open/closed]
    end

    subgraph REALTIME["⚡ Real-time — Socket.IO"]
        SOCK[socket instance]
        MSGS[Chat messages]
        SOS_E[SOS events]
    end

    CTX --> LIST
    CTX --> FORM
    CTX --> SOCK

    style GLOBAL fill:#0f2027,stroke:#14b8a6,color:#fff
    style LOCAL fill:#0a1628,stroke:#22d3ee,color:#fff
    style REALTIME fill:#1a0a1a,stroke:#a78bfa,color:#fff
```

---

## 🌐 API Integration Pattern

```mermaid
sequenceDiagram
    participant C as ⚛️ Component
    participant LS as 🗝️ localStorage
    participant AX as 📡 Axios
    participant BE as 🟢 Express API

    C->>LS: getItem('token')
    LS-->>C: JWT token string
    C->>AX: axios.get(VITE_API_URL + '/endpoint',<br/>{ headers: { Authorization: Bearer token } })
    AX->>BE: HTTPS Request + Bearer
    BE->>BE: protect() — jwt.verify()
    BE-->>AX: { success: true, data: {...} }
    AX-->>C: response.data
    C->>C: setState(data) → re-render
```

---

## ⚙️ Environment & Setup

```bash
# Install dependencies
cd FRONTEND && npm install

# Create .env
echo "VITE_API_URL=http://localhost:5000/api" > .env

# Dev server (http://localhost:5173)
npm run dev

# Production build
npm run build
```

```env
# FRONTEND/.env
VITE_API_URL=http://localhost:5000/api
```

> [!IMPORTANT]
> `VITE_API_URL` **must include `/api`** — e.g. `http://localhost:5000/api`. All component fetch calls use relative paths like `/insurance`, `/hospitals` etc. without repeating `/api`.

---

## 🚀 Deployment (Netlify)

```mermaid
flowchart LR
    A[Push to GitHub] --> B[Netlify auto-builds]
    B --> C[npm run build]
    C --> D[dist/ folder created]
    D --> E[CDN deployment]
    E --> F[netlify.toml redirect applied]
    F --> G([SPA routing works for all /routes])

    style A fill:#14b8a6,color:#000
    style G fill:#22c55e,color:#000
```

**netlify.toml** (already configured):
```toml
[[redirects]]
from = "/*"
to = "/index.html"
status = 200
```

**Build Settings:**
| Setting | Value |
|---------|-------|
| Build command | `npm run build` |
| Publish directory | `dist` |
| Environment variable | `VITE_API_URL = https://your-backend.com/api` |

---

## 📦 Key Dependencies Explained

| Package | Why It's Used |
|---------|--------------|
| `react-router-dom v7` | Client-side routing with `useNavigate`, `useParams`, `Link` |
| `axios` | HTTP client with better error handling than `fetch` |
| `framer-motion` | Page entry + hover animations for premium feel |
| `lucide-react` | Consistent, lightweight icon set (500+ icons) |
| `socket.io-client` | WebSocket for real-time chat + SOS |
| `react-qr-code` | Client-side QR generation for Health Passport |
| `@jitsi/react-sdk` | Embeds Jitsi Meet for in-browser video calls |
| `i18next` | Internationalization for multilingual UI support |
| `tailwindcss v4` | Utility CSS — responsive dark glassmorphic design |
