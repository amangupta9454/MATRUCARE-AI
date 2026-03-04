# 🔄 MaaCare — API Data Flow Documentation

> Step-by-step sequence diagrams for every major platform operation

---

## 1. 🔐 Authentication & Registration Flow

![Auth Flow](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\auth_flow_diagram_1772635560162.png)
*Complete registration + OTP verification + JWT login flow*

```mermaid
sequenceDiagram
    actor U as 👩 User
    participant FE as ⚛️ React Frontend
    participant BE as 🟢 Express API
    participant DB as 🗄️ MongoDB
    participant EMAIL as 📧 Gmail SMTP

    rect rgb(15, 32, 39)
        Note over U,EMAIL: ──── Step 1: Register ────
        U->>FE: Fills registration form
        FE->>BE: POST /api/auth/register<br/>{name, email, password, role}
        BE->>BE: bcrypt.hash(password, 10)
        BE->>BE: generateOTP() → 6 digits
        BE->>DB: INSERT user (isVerified:false)
        DB-->>BE: user._id created
        BE->>EMAIL: sendMail(OTP, subject: "Verify your MaaCare account")
        BE-->>FE: { message: "OTP sent to your email" }
    end

    rect rgb(10, 22, 40)
        Note over U,EMAIL: ──── Step 2: Verify OTP ────
        U->>FE: Enters 6-digit OTP
        FE->>BE: POST /api/auth/verify-otp { email, otp }
        BE->>DB: FIND user by email
        DB-->>BE: user (otp, otpExpiry)
        BE->>BE: Check OTP match + expiry
        BE->>DB: UPDATE isVerified = true
        BE-->>FE: { success: true }
    end

    rect rgb(7, 18, 36)
        Note over U,EMAIL: ──── Step 3: Login ────
        U->>FE: email + password → Login button
        FE->>BE: POST /api/auth/login { email, password }
        BE->>DB: FIND user by email
        DB-->>BE: user with hashed password
        BE->>BE: bcrypt.compare(input, hash) ✓
        BE->>BE: jwt.sign(payload, secret, 7d)
        BE-->>FE: { token, user: { name, role } }
        FE->>FE: localStorage.setItem('token')
        FE->>FE: AuthContext.login(token) → user state
        FE-->>U: Redirect to Dashboard
    end
```

---

## 2. 🏥 Hospital Booking Data Flow

![Hospital Booking Flow](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\hospital_booking_flow_1772635827161.png)
*Complete booking journey from hospital browsing to approval*

```mermaid
sequenceDiagram
    actor M as 🤰 Mother
    participant FE as ⚛️ React
    participant BE as 🟢 Express API
    participant DB as 🗄️ MongoDB
    participant EMAIL as 📧 Nodemailer

    M->>FE: Opens Hospitals page
    FE->>BE: GET /api/hospitals
    BE->>DB: FIND all hospitals
    DB-->>BE: hospitals[]
    BE-->>FE: { hospitals }
    FE-->>M: Hospital cards rendered

    M->>FE: Clicks hospital → Opens HospitalBookingForm
    FE->>BE: GET /api/insurance (Bearer token)
    BE->>DB: FIND policies where userId = req.user._id
    DB-->>BE: policies[]
    BE-->>FE: { policies }
    FE-->>M: Insurance dropdown populated

    M->>FE: Selects insurance → cost calculated
    Note over FE: estimatedCost = coverageAmount - copay

    M->>FE: Submit booking form
    FE->>BE: POST /api/hospital-bookings<br/>{hospitalId, serviceId, insurancePolicyId, estimatedCost, ...}
    BE->>DB: INSERT HospitalBooking (status: 'Pending')
    DB-->>BE: booking._id
    BE->>EMAIL: sendConfirmation(patient email, booking details, cost breakdown)
    BE-->>FE: { booking, message: "Booking created" }
    FE-->>M: ✅ Success confirmation shown

    Note over BE,DB: ──── Hospital Side ────
    BE->>DB: hospital reviews bookings dashboard
    BE->>BE: PUT /api/hospital-bookings/:id/status
    BE->>EMAIL: sendStatusUpdate(patient, "Approved")
```

---

## 3. 🚨 Emergency SOS Data Flow

![SOS Flow Diagram](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\sos_flow_diagram_1772635766438.png)
*Location capture + DB persistence + email alert in under 3 seconds*

```mermaid
sequenceDiagram
    actor U as 👩 User (High Risk)
    participant GEO as 📍 GPS API
    participant FE as ⚛️ EmergencySOSPanel
    participant BE as 🟢 Express
    participant DB as 🗄️ MongoDB
    participant EMAIL as 📧 Nodemailer

    U->>FE: 🔴 Clicks SOS Button
    FE->>GEO: navigator.geolocation.getCurrentPosition()
    GEO-->>FE: { latitude, longitude }
    FE->>FE: Update UI: "Locating you..."

    FE->>BE: POST /api/emergency/sos<br/>{ location: {lat,lng}, riskLevel: 'high' }
    BE->>DB: FIND EmergencyContact where userId
    DB-->>BE: contact { doctorPhone, familyContact, ashaPhone }
    BE->>DB: INSERT EmergencyEvent { userId, location, timestamp }
    DB-->>BE: event._id

    BE->>EMAIL: sendSOS({
        to: user.email,
        subject: "🚨 MaaCare SOS Alert",
        body: location link + contact numbers + timestamp
    })

    BE-->>FE: { success, eventId }
    FE->>FE: Update UI: "✅ SOS Sent! Help on the way"
    FE->>FE: Show contact numbers panel
```

---

## 4. 🆔 Digital Health Passport & QR Flow

![Health Passport QR](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\health_passport_qr_1772635863063.png)
*Create once, scan anywhere — instant medical context for first responders*

```mermaid
sequenceDiagram
    actor M as 👩 Mother
    actor D as 👨‍⚕️ Doctor (Scanner)
    participant FE as ⚛️ HealthPassport Page
    participant QR as 📱 HealthPassportQR Component
    participant BE as 🟢 Express API
    participant DB as 🗄️ MongoDB

    M->>FE: Opens Health Passport page
    FE->>BE: GET /api/health-passport (Bearer)
    BE->>DB: FIND passport where userId
    DB-->>BE: passport data
    BE-->>FE: { passport: { bloodGroup, allergies, conditions... } }
    FE->>FE: Populate form with data

    M->>FE: Edits passport fields (blood type, allergies, etc.)
    FE->>BE: POST /api/health-passport { bloodGroup, ... }
    BE->>DB: UPSERT HealthPassport
    DB-->>BE: updated passport
    BE-->>FE: { passport }

    FE->>QR: Pass passport as props
    QR->>QR: JSON.stringify(passport)
    QR->>QR: react-qr-code renders QR image
    FE-->>M: 📱 QR code displayed for screenshots/print

    Note over D,QR: ──── Emergency Scenario ────
    D->>QR: Scans QR with phone camera
    QR-->>D: JSON.parse → instant view of:<br/>🩸 Blood Group, ⚠️ Allergies,<br/>🏥 Conditions, 📞 Doctor Contact
```

---

## 5. 💬 Real-Time Chat Data Flow

```mermaid
sequenceDiagram
    actor A as 👩 User A (Sender)
    actor B as 👨 User B (Receiver)
    participant SA as ⚛️ Chat.jsx (A)
    participant SB as ⚛️ Chat.jsx (B)
    participant SOCK as 🔌 Socket.IO Server
    participant BE as 🟢 Express API
    participant TRANS as 🌍 Google Translate
    participant DB as 🗄️ MongoDB

    A->>SA: Opens chat, types message
    SA->>SOCK: socket.connect(backendBaseUrl)
    SB->>SOCK: socket.connect(backendBaseUrl)
    SOCK->>SOCK: rooms[userId] = socketId

    A->>SA: Hits Send button
    SA->>BE: POST /api/chat/send { to, text, language }
    BE->>TRANS: translate(text, targetLanguage)
    TRANS-->>BE: translatedText
    BE->>DB: INSERT Message { from, to, text, translatedText, timestamp }
    DB-->>BE: message._id
    BE->>SOCK: io.to(rooms[B._id]).emit('receive_message', { ... })
    SOCK->>SB: receive_message event fires
    SB->>SB: Append message to chat window
    SB-->>B: 💬 Message appears in real-time

    BE-->>SA: { message: saved, success }
    SA-->>A: ✅ Message sent indicator
```

---

## 6. 🗺️ AI Health Navigation Flow

```mermaid
flowchart LR
    A([👩 User types condition]) --> B[HealthNavigationAssistant]
    B --> C[GET /api/navigation/journey?condition=X]
    C --> D{Condition matched?}
    D -->|Yes| E[healthJourneyGenerator.js]
    D -->|No| F[Return 'Not found' message]
    E --> G[Build steps array]
    G --> H[steps: title, desc, type, icon, link]
    H --> I([Render step cards with icons & colors])
    
    style A fill:#14b8a6,color:#000
    style I fill:#14b8a6,color:#000
    style F fill:#ef4444,color:#fff
```

**Supported Conditions:** Anemia, Gestational Diabetes, Hypertension, Preeclampsia, Thyroid, Normal Pregnancy

---

## 7. ⏰ Background Notification Flow

```mermaid
flowchart TD
    START([🌅 Server Starts]) --> CRON1
    START --> CRON2

    subgraph CRON1["notificationScheduler.js — 8:00 AM Daily"]
        C1[Query: Appointments in next 24h]
        C2[Query: Vaccinations due soon]
        C3[For each → Nodemailer send reminder email]
        C1 --> C3
        C2 --> C3
    end

    subgraph CRON2["reminderScheduler.js — Every Hour"]
        R1[Query: Bookings with status 'Confirmed']
        R2[Filter: appointmentDate - 24h = now]
        R3[Send '🏥 Tomorrow is your booking' email]
        R1 --> R2 --> R3
    end

    style CRON1 fill:#0f2027,stroke:#14b8a6,color:#fff
    style CRON2 fill:#0a1a0a,stroke:#22c55e,color:#fff
```

---

## 8. 📡 Standard API Response Format

```mermaid
graph LR
    subgraph SUCCESS["✅ Success Response (2xx)"]
        S1["{ success: true,<br/>message: 'Operation successful',<br/>data: { ... } }"]
    end
    subgraph ERROR["❌ Error Response (4xx / 5xx)"]
        E1["{ success: false,<br/>message: 'Descriptive error message' }"]
    end
    subgraph CODES["📋 HTTP Status Codes"]
        C1[200 — OK]
        C2[201 — Created]
        C3[400 — Bad Request]
        C4[401 — Unauthorized]
        C5[403 — Forbidden]
        C6[404 — Not Found]
        C7[500 — Server Error]
    end

    style SUCCESS fill:#0a2010,stroke:#22c55e,color:#fff
    style ERROR fill:#1a0a0a,stroke:#ef4444,color:#fff
    style CODES fill:#0f1a2a,stroke:#14b8a6,color:#fff
```

---

## 9. 🖼️ API Auth Header Pattern

Every authenticated request from the frontend follows this pattern:

```javascript
// ✅ Correct pattern used throughout MaaCare frontend
const token = localStorage.getItem('token');

const response = await axios.get(`${import.meta.env.VITE_API_URL}/endpoint`, {
    headers: { 
        Authorization: `Bearer ${token}` 
    }
});

// VITE_API_URL = "http://localhost:5000/api"  (includes /api already!)
// Endpoint paths are relative: /insurance, /hospitals, etc.
```

> [!WARNING]
> **Never** hardcode `localhost:5000` — always use `import.meta.env.VITE_API_URL`. Also note `VITE_API_URL` already includes `/api`, so endpoint paths must NOT repeat it.
