# 🏗️ MaaCare — System Architecture

> Technical architecture reference for the MaaCare maternal healthcare platform

![System Architecture](C:\Users\amang\.gemini\antigravity\brain\2eec5b48-20b2-4e4e-8e50-27b0e3440082\system_architecture_diagram_1772635456183.png)
*Three-layer architecture: Client (React/Vite) → Server (Node.js/Express) → Persistence (MongoDB/Cloudinary)*

---

## 📐 High-Level Architecture

```mermaid
graph TB
    subgraph CLIENT["🖥️ Client Layer — React 19 + Vite (Netlify CDN)"]
        A[AuthContext<br/>Global JWT State]
        B[Navbar<br/>Dropdown Menus]
        C[Pages & Components<br/>50+ React Components]
        D[Socket.IO Client<br/>Real-time Events]
    end

    subgraph SERVER["⚙️ Server Layer — Node.js + Express 5"]
        E[REST API<br/>/api/* routes]
        F[Socket.IO Server<br/>WebSocket Hub]
        G[JWT Middleware<br/>protect + authorize]
        H[Helmet + CORS<br/>Security Layer]
        I[Nodemailer<br/>Email Service]
        J[node-cron<br/>Job Scheduler]
    end

    subgraph PERSIST["🗄️ Persistence Layer"]
        K[(MongoDB Atlas<br/>Primary Database)]
        L[(Cloudinary<br/>Media Storage)]
        M[(Gmail SMTP<br/>Email Delivery)]
    end

    CLIENT -->|HTTPS + Bearer JWT| SERVER
    D <-->|WSS WebSocket| F
    SERVER -->|Mongoose ODM| K
    SERVER -->|Signed Upload| L
    I -->|SMTP| M

    style CLIENT fill:#0f2027,stroke:#14b8a6,color:#fff
    style SERVER fill:#0a1628,stroke:#14b8a6,color:#fff
    style PERSIST fill:#071224,stroke:#14b8a6,color:#fff
```

---

## 🔒 Security Architecture Flow

```mermaid
sequenceDiagram
    participant B as 🌐 Browser
    participant H as 🛡️ Helmet
    participant C as 🔑 CORS
    participant J as 🔏 JWT Middleware
    participant R as 📋 Role Guard
    participant Ctrl as ⚙️ Controller
    participant DB as 🗄️ MongoDB

    B->>H: HTTP Request
    H->>C: Add Security Headers
    C->>C: Check Origin vs FRONTEND_URL
    alt Origin not allowed
        C-->>B: 403 Forbidden
    else Origin allowed
        C->>J: protect(req, res, next)
        J->>J: jwt.verify(token, secret)
        alt Token invalid/expired
            J-->>B: 401 Unauthorized
        else Token valid
            J->>R: authorize(...roles)
            alt Role not permitted
                R-->>B: 403 Forbidden
            else Role permitted
                R->>Ctrl: Execute controller
                Ctrl->>DB: Query / Mutation
                DB-->>Ctrl: Result
                Ctrl-->>B: JSON Response
            end
        end
    end
```

---

## 🗂️ Complete Component Interaction Map

```mermaid
graph LR
    subgraph FE["Frontend Components"]
        NAV[Navbar]
        AUTH[AuthContext]
        APP[App.jsx Router]
        HOSP[HospitalDetails]
        BOOK[HospitalBookingForm]
        INS[InsuranceDashboard]
        PASS[HealthPassport]
        NAV2[HealthNavigation]
        SOS[EmergencySOSPanel]
        CHAT[Chat.jsx]
    end

    subgraph BE["Backend Services"]
        HB[/api/hospital-bookings]
        INSR[/api/insurance]
        HP[/api/health-passport]
        EM[/api/emergency]
        CH[/api/chat]
        NAV3[/api/navigation]
    end

    subgraph DB["MongoDB Collections"]
        HBDB[(HospitalBooking)]
        INSDB[(InsurancePolicy)]
        HPDB[(HealthPassport)]
        EMDB[(EmergencyEvent)]
        MSDB[(Message)]
    end

    BOOK -->|POST| HB --> HBDB
    INS -->|GET/POST/DELETE| INSR --> INSDB
    PASS -->|GET/POST| HP --> HPDB
    SOS -->|POST| EM --> EMDB
    CHAT -->|Socket.IO + POST| CH --> MSDB
    NAV2 -->|GET| NAV3

    style FE fill:#0f2027,stroke:#14b8a6,color:#fff
    style BE fill:#0a1628,stroke:#14b8a6,color:#fff
    style DB fill:#071224,stroke:#22d3ee,color:#fff
```

---

## 🗺️ Request Lifecycle

```mermaid
flowchart TD
    A([Browser Request]) --> B[Vite SPA — React Router]
    B --> C{Protected Route?}
    C -->|No| D[Render Public Page]
    C -->|Yes| E{AuthContext has token?}
    E -->|No| F([Redirect to /login])
    E -->|Yes| G[Axios → HTTPS → Express]
    G --> H[Helmet Headers Applied]
    H --> I[CORS Origin Check]
    I --> J[JWT Verify — protect]
    J --> K[Role Check — authorize]
    K --> L[Controller Logic]
    L --> M[Mongoose Query]
    M --> N[(MongoDB Atlas)]
    N --> O[JSON Response]
    O --> P[React State Update]
    P --> Q([UI Re-renders])

    style A fill:#14b8a6,color:#000
    style F fill:#ef4444,color:#fff
    style Q fill:#14b8a6,color:#000
    style N fill:#22d3ee,color:#000
```

---

## 🔌 Real-Time Architecture (Socket.IO)

```mermaid
graph TB
    subgraph CLA["Client A (Sender)"]
        CA1[User types message]
        CA2[socket.emit send_message]
    end

    subgraph CLB["Client B (Receiver)"]
        CB1[socket.on receive_message]
        CB2[UI updates chat window]
    end

    subgraph SRV["Socket.IO Server (Node.js)"]
        S1[io.on connection]
        S2[rooms map userId→socketId]
        S3[io.to socketId .emit]
    end

    subgraph SOS_S["Emergency SOS Broadcast"]
        E1[Client: SOS button clicked]
        E2[POST /api/emergency/sos]
        E3[Socket broadcast to admin room]
        E4[Email via Nodemailer]
    end

    CA1 --> CA2 --> S1 --> S2 --> S3 --> CB1 --> CB2
    E1 --> E2 --> E3
    E2 --> E4

    style CLA fill:#0f2027,stroke:#14b8a6,color:#fff
    style CLB fill:#0f2027,stroke:#14b8a6,color:#fff
    style SRV fill:#0a1628,stroke:#22d3ee,color:#fff
    style SOS_S fill:#1a0a0a,stroke:#ef4444,color:#fff
```

---

## 🏗️ Backend Route Structure

```mermaid
mindmap
  root((🏥 MaaCare API /api))
    🔐 Auth
      /register
      /login
      /verify-otp
      /forget-password
      /reset-password
    🩺 Clinical
      /doctors
      /appointments
      /teleconsult
      /health-records
    👤 User
      /pregnancy
      /baby
      /health
      /reports
    🏥 Hospital
      /hospitals
      /hospital-bookings
      /mentors
    🆕 New Features
      /insurance
      /health-passport
      /navigation
      /emergency
    🌍 Community
      /forum
      /reviews
      /feedback
      /chat
    📊 Admin
      /analytics
      /insights
      /schemes
      /asha
```

---

## 📦 Storage Architecture

| Data Type | Storage | Format | Size Limit |
|-----------|---------|--------|-----------|
| User profiles | MongoDB Atlas | BSON Documents | Unlimited |
| Health records (metadata) | MongoDB Atlas | BSON with ref | Unlimited |
| Profile images | Cloudinary | JPEG/PNG/WebP | 10 MB per file |
| Health documents | Cloudinary | PDF/Image | 10 MB per file |
| Insurance documents | Cloudinary | PDF/Image | 10 MB per file |
| Chat messages | MongoDB Atlas | BSON | Unlimited |
| Emergency events | MongoDB Atlas | BSON | Unlimited |
| JWT sessions | Client localStorage | String | N/A (stateless) |

---

## ⚖️ Scalability Considerations

```mermaid
graph LR
    A[Current State] --> B[Scale Path]
    
    subgraph NOW["Now (Development)"]
        N1[MongoDB Free Tier M0]
        N2[Single Express Instance]
        N3[Socket.IO Single Node]
        N4[Gmail SMTP]
        N5[Cloudinary Free]
    end
    
    subgraph SCALE["At Scale (Production)"]
        S1[MongoDB Atlas M10+ / Sharding]
        S2[PM2 Cluster / Docker + Kubernetes]
        S3[Socket.IO + Redis Pub/Sub Adapter]
        S4[AWS SES / SendGrid]
        S5[Cloudinary Growth / AWS S3]
    end
    
    N1 -.->|Upgrade| S1
    N2 -.->|Containerize| S2
    N3 -.->|Add Redis| S3
    N4 -.->|Switch SMTP| S4
    N5 -.->|Upgrade| S5

    style NOW fill:#0f1a2a,stroke:#14b8a6,color:#fff
    style SCALE fill:#0a2010,stroke:#22c55e,color:#fff
```
