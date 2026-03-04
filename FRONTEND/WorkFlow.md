# ⚙️ MaaCare — User & System Workflow

> Complete end-to-end journey documentation for all platform user roles

---

## 🗂️ Module Overview

```mermaid
mindmap
  root((MaaCare Workflows))
    👩 Mother
      Registration & Login
      Pregnancy Tracking
      Book Appointment
      Hospital Booking + Insurance
      Emergency SOS
      Health Passport
    🩺 Doctor
      Profile Management
      Appointment Dashboard
      TeleConsult Rooms
      Patient Records
    🌿 ASHA Worker
      Visit Scheduling
      Community Outreach
      Scheme Lookup
    🏥 Hospital
      Registration
      Manage Services
      Booking Approval
    👑 Admin
      Analytics
      Platform Insights
      User Management
```

---

## 1. 🔐 User Registration & Login

```mermaid
flowchart TD
    A([New User]) --> B[Opens /register page]
    B --> C[Fills: name, email, password, role]
    C --> D[POST /api/auth/register]
    D --> E{Server validation}
    E -->|❌ Fails| F[Show error message]
    E -->|✅ Passes| G[bcrypt hash password]
    G --> H[Generate 6-digit OTP]
    H --> I[Save user — isVerified: false]
    I --> J[Send OTP via Nodemailer]
    J --> K[User reads email → enters OTP]
    K --> L[POST /api/auth/verify-otp]
    L --> M{OTP valid?}
    M -->|❌ Expired| N[Request new OTP]
    M -->|✅ Valid| O[SET isVerified: true]
    O --> P[User logs in → JWT issued]
    P --> Q([Redirect to role Dashboard])

    style A fill:#14b8a6,color:#000
    style Q fill:#14b8a6,color:#000
    style F fill:#ef4444,color:#fff
    style N fill:#f59e0b,color:#000
```

---

## 2. 🏥 Hospital Booking + Insurance Workflow

![Hospital Booking Visual](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\hospital_booking_flow_1772635827161.png)

```mermaid
flowchart LR
    A([Mother opens /hospitals]) --> B[Search & filter hospitals]
    B --> C[Click hospital → /hospitals/:id]
    C --> D[View services, beds, doctors]
    D --> E[Click 'Book This Service']
    E --> F[HospitalBookingForm opens]
    F --> G[Load insurance policies]
    G --> H{Has insurance?}
    H -->|No| I[Book without insurance]
    H -->|Yes| J[Select policy]
    J --> K[Estimated cost shown]
    K --> L[Fill: name, date, phone, notes]
    I --> L
    L --> M[Submit → POST /api/hospital-bookings]
    M --> N[Booking saved: status Pending]
    N --> O[📧 Confirmation email sent]
    O --> P([Hospital reviews booking])
    P --> Q{Hospital decision}
    Q -->|✅ Approve| R[Status → Approved + email]
    Q -->|❌ Reject| S[Status → Rejected + email]

    style A fill:#14b8a6,color:#000
    style K fill:#22c55e,color:#000
    style S fill:#ef4444,color:#fff
    style R fill:#22c55e,color:#000
```

---

## 3. 🛡️ Insurance Management Workflow

```mermaid
flowchart TD
    A([/insurance page]) --> B[Fetch all policies\nGET /api/insurance]
    B --> C{Has policies?}
    C -->|Yes| D[Display InsuranceCard components]
    C -->|No| E[Show empty state + Add button]

    D --> F{User action}
    F -->|Check Coverage| G[Enter hospital name]
    G --> H[GET /api/insurance/:id/coverage?hospital=X]
    H --> I{Hospital in network?}
    I -->|✅ Yes| J[✅ Show green banner: Covered]
    I -->|❌ No| K[🔴 Show red banner: Not in network]

    F -->|Delete Policy| L[Confirm dialog]
    L --> M[DELETE /api/insurance/:id]
    M --> N[Policy removed from list]

    E --> O[Click + Add Policy]
    D --> O
    O --> P[InsuranceForm opens]
    P --> Q[Fill: provider, type, policy #, dates, hospitals]
    Q --> R[POST /api/insurance]
    R --> S[New InsuranceCard added]

    style A fill:#14b8a6,color:#000
    style J fill:#22c55e,color:#000
    style K fill:#ef4444,color:#fff
    style S fill:#22c55e,color:#000
```

---

## 4. 🆔 Health Passport & QR Workflow

![Health Passport QR](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\health_passport_qr_1772635863063.png)

```mermaid
flowchart LR
    A([User opens /passport]) --> B[GET /api/health-passport]
    B --> C{Passport exists?}
    C -->|First time| D[Show empty form]
    C -->|Exists| E[Populate form with data]
    D --> F[User fills: blood type, allergies,\nconditions, vaccinations, doctor info]
    E --> F
    F --> G[Save → POST /api/health-passport]
    G --> H[Passport saved/updated in DB]
    H --> I[HealthPassportQR renders QR]
    I --> J[QR = JSON.stringify of passport data]
    J --> K([Mother shares/prints QR])
    K --> L[🏥 First Responder scans QR]
    L --> M[Instant medical context:\n🩸 Blood Group\n⚠️ Allergies\n💊 Conditions\n📞 Emergency Contact]

    style A fill:#14b8a6,color:#000
    style M fill:#22c55e,color:#000
```

---

## 5. 🚨 Emergency SOS Workflow

![SOS Flow](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\sos_flow_diagram_1772635766438.png)

```mermaid
flowchart TD
    A([🔴 User clicks SOS button]) --> B[navigator.geolocation.getCurrentPosition]
    B --> C{Location granted?}
    C -->|❌ Denied| D[Alert: Please enable location]
    C -->|✅ Got location| E[Show: Locating you...]
    E --> F[POST /api/emergency/sos\n{lat, lng, riskLevel}]
    F --> G[Fetch EmergencyContact from DB]
    G --> H[INSERT EmergencyEvent record]
    H --> I[📧 Send SOS email:\n• Location map link\n• Timestamp\n• Contacts]
    I --> J[Backend response: success]
    J --> K[UI: ✅ Help is on the way!]
    K --> L[Show emergency contact numbers]
    L --> M([One-tap call: Doctor / Family / ASHA])

    style A fill:#ef4444,color:#fff
    style D fill:#f59e0b,color:#000
    style K fill:#22c55e,color:#000
    style M fill:#14b8a6,color:#000
```

---

## 6. 📹 Teleconsultation Workflow

```mermaid
sequenceDiagram
    actor M as 👩 Mother
    actor D as 👨‍⚕️ Doctor
    participant FE as ⚛️ TeleConsult Page
    participant BE as 🟢 Express API
    participant DB as 🗄️ MongoDB
    participant JITSI as 🎥 Jitsi Meet

    M->>FE: Opens /teleconsult
    FE->>BE: GET /api/teleconsult (available doctors)
    BE->>DB: FIND available sessions
    DB-->>BE: sessions[]
    BE-->>FE: { sessions }
    FE-->>M: Shows doctor availability

    M->>FE: Books session with Dr. X
    FE->>BE: POST /api/teleconsult { doctorId, scheduledAt }
    BE->>DB: INSERT TeleConsult { sessionId: random }
    DB-->>BE: { sessionId }
    BE-->>FE: { sessionId, roomName: maacare_sessionId }
    FE-->>M: Redirect → /teleconsult/room/:sessionId

    M->>JITSI: TeleConsultRoom.jsx loads Jitsi embed
    D->>JITSI: Doctor joins using same room ID
    JITSI-->>M: Video call begins 🎥
    JITSI-->>D: Video call begins 🎥

    Note over D,JITSI: After session ends
    D->>BE: POST /api/teleconsult/:id/notes { notes, prescription }
    BE->>DB: UPDATE session with notes
```

---

## 7. 💬 Multilingual Chat Workflow

```mermaid
flowchart LR
    A([User opens /chat]) --> B[Load Contacts\nGET /api/chat/contacts]
    B --> C[Select conversation]
    C --> D[Load History\nGET /api/chat/:userId]
    D --> E[render message bubbles]
    E --> F[User types message]
    F --> G[POST /api/chat/send\n{to, text, language}]
    G --> H{Language different?}
    H -->|Yes| I[google-translate-api-x]
    I --> J[Save with translatedText]
    H -->|No| J
    J --> K[socket.io emit receive_message]
    K --> L([Recipient sees message instantly])

    style A fill:#14b8a6,color:#000
    style L fill:#14b8a6,color:#000
    style I fill:#f59e0b,color:#000
```

---

## 8. 🗺️ Health Navigation Workflow

```mermaid
flowchart TD
    A([User opens /navigation]) --> B[HealthNavigationAssistant rendered]
    B --> C[User types medical condition\ne.g. anemia, diabetes, hypertension]
    C --> D[GET /api/navigation/journey?condition=X]
    D --> E[healthJourneyGenerator.js processes]
    E --> F{Condition recognized?}
    F -->|No| G[Show: condition not found message]
    F -->|Yes| H[Return steps array]
    H --> I[Render step cards with icons]
    I --> J[Steps show: title, description, type, links]
    J --> K[User follows journey step by step]
    K --> L([Better health outcomes ✅])
    
    B --> M[EmergencySOSPanel always visible]
    B --> N[ContactEmergencyCard for quick contacts]

    style A fill:#14b8a6,color:#000
    style L fill:#22c55e,color:#000
    style G fill:#f59e0b,color:#000
```

---

## 9. 🎭 Role-Based Access Matrix

![Role Matrix](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\role_matrix_visual_1772635893291.png)

| Feature | 👩 Mother | 🩺 Doctor | 🌿 ASHA | 🏥 Hospital | 👑 Admin |
|---------|:---------:|:---------:|:-------:|:-----------:|:--------:|
| Health Dashboard | ✅ | ❌ | ❌ | ❌ | ❌ |
| Book Appointment | ✅ | ❌ | ❌ | ❌ | ❌ |
| Book Hospital Service | ✅ | ❌ | ❌ | ❌ | ❌ |
| Manage Insurance | ✅ | ✅ | ✅ | ✅ | ✅ |
| Health Passport | ✅ | ✅ | ✅ | ✅ | ✅ |
| Emergency SOS | ✅ | ✅ | ✅ | ✅ | ✅ |
| Doctor Panel | ❌ | ✅ | ❌ | ❌ | ❌ |
| ASHA Panel | ❌ | ❌ | ✅ | ❌ | ❌ |
| Hospital Panel | ❌ | ❌ | ❌ | ✅ | ❌ |
| Platform Analytics | ❌ | ❌ | ❌ | ❌ | ✅ |

> [!IMPORTANT]
> Role assignment is locked at registration. Admins can upgrade/downgrade user roles via the Admin Dashboard. JWT tokens carry the `role` field and all protected routes verify it via `authorize(...roles)` middleware.

---

## 10. ⏰ Background Job Scheduling

```mermaid
gantt
    title Daily Background Job Schedule
    dateFormat HH:mm
    section Notification Scheduler
    Query upcoming appointments    : 08:00, 1m
    Query due vaccinations          : 08:01, 1m
    Send reminder emails (batch)   : 08:02, 5m
    section Reminder Scheduler (Hourly)
    Query confirmed bookings       : 00:00, 1m
    Filter 24h-before bookings     : 00:01, 1m
    Send advance notice emails     : 00:02, 2m
```
