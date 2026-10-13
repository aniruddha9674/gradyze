<div align="center">

<img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=700&size=42&duration=3000&pause=1200&color=6366F1&center=true&vCenter=true&width=560&height=70&lines=Gradyze+%F0%9F%8E%93;Academic+Management+Platform" alt="Gradyze" />

**One platform for the whole institution. Role-based dashboards for admins, teachers, and students.**

*Centralised performance data, attendance, marks, and analytics, in a single full-stack web app.*

<br/>

[![Status](https://img.shields.io/badge/status-in_development-F59E0B?style=for-the-badge&logo=git&logoColor=white)](#-status)
[![Stack](https://img.shields.io/badge/stack-MERN-47A248?style=for-the-badge&logo=mongodb&logoColor=white)](#-stack)
[![Auth](https://img.shields.io/badge/auth-JWT_%2B_bcrypt-000000?style=for-the-badge&logo=jsonwebtokens&logoColor=white)](#-authentication)
[![License](https://img.shields.io/badge/license-ISC-6366F1?style=for-the-badge)](#)

<br/>

![React](https://img.shields.io/badge/React_18-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![Vite](https://img.shields.io/badge/Vite_6-646CFF?style=flat-square&logo=vite&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind_3-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![MUI](https://img.shields.io/badge/MUI_6-007FFF?style=flat-square&logo=mui&logoColor=white)
![React Router](https://img.shields.io/badge/React_Router_7-CA4245?style=flat-square&logo=reactrouter&logoColor=white)
![Axios](https://img.shields.io/badge/Axios-5A29E4?style=flat-square&logo=axios&logoColor=white)
![Node](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express_4-000000?style=flat-square&logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white)
![Mongoose](https://img.shields.io/badge/Mongoose_8-880000?style=flat-square&logo=mongoose&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=flat-square&logo=jsonwebtokens&logoColor=white)
![ESLint](https://img.shields.io/badge/ESLint_9-4B32C3?style=flat-square&logo=eslint&logoColor=white)

<br/>

[**Overview**](#-what-it-does) &nbsp;·&nbsp;
[**Architecture**](#-architecture) &nbsp;·&nbsp;
[**Auth**](#-authentication) &nbsp;·&nbsp;
[**Dashboard**](#-admin-dashboard) &nbsp;·&nbsp;
[**Status**](#-status) &nbsp;·&nbsp;
[**API**](#-api) &nbsp;·&nbsp;
[**Run locally**](#-run-locally) &nbsp;·&nbsp;
[**Roadmap**](#-roadmap)

</div>

<br/>

---

## 🎓 What it does

Gradyze handles the operational side of an academic institution.

| 🌐 Public site | 🔐 Role-based access | 🛠 Admin workspace | ⚙️ REST backend |
|---|---|---|---|
| Landing, features, pricing, testimonials, about, contact, privacy, docs | Separate Admin, Teacher, and Student entry points | Protected dashboard with a 12-section academic management menu | Express + MongoDB, JWT-signed sessions |

**The six pillars the product is built around:**

| | Pillar | |
|:-:|---|---|
| 📊 | **Centralised performance data** | One source of truth for marks and results |
| 🔎 | **Learning challenge detection** | Spot struggling students early |
| 🧭 | **Data-driven academic support** | Analytics for faculty and students |
| 🗂 | **Administrative efficiency** | Less paperwork, faster workflows |
| 💬 | **Enhanced communication** | Notifications and feedback loops |
| 🛡 | **Reliability** | Token-based, protected access |

<br/>

## 🏗 Architecture

```mermaid
flowchart LR
    V([👤 Visitor]) --> APP

    subgraph FE["⚛️ React + Vite · :5173"]
        direction TB
        APP[App.jsx<br/><sub>React Router 7</sub>]
        PUB[Public pages<br/><sub>Landing · About · Contact<br/>Privacy · Docs</sub>]
        LOG[Login pages<br/><sub>Admin · Teacher · Student</sub>]
        PR{PrivateRoute<br/><sub>checks adminId</sub>}
        DASH[Admin dashboard<br/><sub>ADashLayout + 12 menu sections</sub>]
        APP --> PUB
        APP --> LOG
        APP --> PR --> DASH
    end

    subgraph BE["🟩 Express API · :5000"]
        direction TB
        SIGN[POST /api/admin/signup]
        LGN[POST /api/admin/adminlogin]
        MW[verifyToken<br/><sub>Bearer JWT middleware</sub>]
    end

    LOG -->|Axios| SIGN
    LOG -->|Axios| LGN
    SIGN --> DB
    LGN --> DB
    DB[(🍃 MongoDB<br/><sub>AdminDB · User</sub>)]
    BE -.serves.-> DIST[FrontEnd/dist<br/><sub>production build</sub>]

    style FE fill:#0d1117,stroke:#6366F1,stroke-width:2px,color:#fff
    style BE fill:#0d1117,stroke:#339933,stroke-width:2px,color:#fff
    style DB fill:#47A248,color:#fff,stroke:none
```

In production the Express server also serves the built React app from `FrontEnd/dist` and falls back to `index.html` for client-side routes.

<br/>

## 🔐 Authentication

```mermaid
sequenceDiagram
    autonumber
    actor A as Admin
    participant F as React app
    participant S as Express API
    participant M as MongoDB

    A->>F: Fill in email + password
    F->>S: POST /api/admin/adminlogin
    S->>M: Admin.findOne({ email })
    M-->>S: admin document
    S->>S: bcrypt.compare(password, hash)
    S-->>F: 200 { token (1h JWT), adminId, name }
    F->>F: Store token, adminId, adminName in localStorage
    F->>A: Redirect to /admindash
    Note over F: PrivateRoute redirects to /adminlogin<br/>if adminId is missing
```

| Piece | How it works |
|---|---|
| **Signup** | Requires `name`, `email`, `password`, `university`, `college`. Rejects duplicate emails. Password is hashed with `bcryptjs` (10 rounds) |
| **Login** | Generic `Invalid email or password` for both unknown email and wrong password, so it doesn't leak which one failed |
| **Token** | JWT signed with `JWT_SECRET`, **1 hour** expiry, carries the admin `id` (and `name` on login) |
| **Guard** | `PrivateRoute` redirects unauthenticated visitors to `/adminlogin` |
| **Middleware** | `verifyToken` reads `Authorization: Bearer <token>`, verifies it, and attaches `req.adminId` |

<br/>

## 🧭 Admin dashboard

`ADashLayout` gives every section a shared sidebar, greeting header, and logout flow. The menu is organised around four areas:

| 👨‍🏫 Faculty | 👥 Students | 📣 Communication | 🧾 Account |
|---|---|---|---|
| Faculty Management | Student Management | Notifications | Subscription Plan |
| Faculty Analytics | Students' Marks | Feedback | Settings |
| | Students' Analytics | Syllabus | |
| | Attendance Reports | | |

Plus an **Overview** landing section.

<br/>

## 💳 Pricing tiers

| | **Free** | **Personal** ⭐ | **Organization** |
|---|:-:|:-:|:-:|
| Price | `$0` | `$11.99` | `$49.99` |
| Badge | Basic | Most popular | Premium |

<br/>

## 🚦 Status

| Area | Feature | State |
|---|---|:-:|
| 🌐 Public | Landing, features, pricing, testimonials, about, contact, privacy, documentation | ✅ Done |
| 🔐 Auth | Admin signup + login (bcrypt, JWT) | ✅ Done |
| 🔐 Auth | `PrivateRoute` guard for `/admindash` | ✅ Done |
| 🛠 Admin | Dashboard layout, sidebar, logout | ✅ Done |
| 🛠 Admin | Menu sections (faculty, students, marks, attendance, …) | 🟡 Scaffolded · content pending |
| 🎓 Teacher | Login page | 🟡 UI only · no backend yet |
| 🎓 Student | Login page | 🟡 UI only · no backend yet |
| 🎓 Teacher / Student | Dashboards | ⏳ Next |

```
Progress  ██████████░░░░░░░░░░░░░░  public site + admin auth shipped · academic modules next
```

<br/>

## 🛠 Stack

| Layer | Choice | Why |
|---|---|---|
| **UI** | ![React](https://img.shields.io/badge/React_18-20232A?style=flat-square&logo=react&logoColor=61DAFB) | Component model for role-based dashboards |
| **Build** | ![Vite](https://img.shields.io/badge/Vite_6-646CFF?style=flat-square&logo=vite&logoColor=white) | Fast HMR and builds |
| **Styling** | ![Tailwind](https://img.shields.io/badge/Tailwind_3-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white) ![CSS Modules](https://img.shields.io/badge/CSS_Modules-000000?style=flat-square&logo=cssmodules&logoColor=white) | Utility classes plus scoped page styles |
| **Components & icons** | ![MUI](https://img.shields.io/badge/MUI_6-007FFF?style=flat-square&logo=mui&logoColor=white) ![Lucide](https://img.shields.io/badge/Lucide-F56565?style=flat-square&logo=lucide&logoColor=white) ![Font Awesome](https://img.shields.io/badge/Font_Awesome-528DD7?style=flat-square&logo=fontawesome&logoColor=white) | UI primitives and iconography |
| **Routing** | ![React Router](https://img.shields.io/badge/React_Router_7-CA4245?style=flat-square&logo=reactrouter&logoColor=white) | Client-side routes and guards |
| **HTTP** | ![Axios](https://img.shields.io/badge/Axios-5A29E4?style=flat-square&logo=axios&logoColor=white) | Shared API client in `utils/api.js` |
| **Server** | ![Express](https://img.shields.io/badge/Express_4-000000?style=flat-square&logo=express&logoColor=white) | Minimal REST API |
| **Database** | ![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white) ![Mongoose](https://img.shields.io/badge/Mongoose_8-880000?style=flat-square&logo=mongoose&logoColor=white) | Document storage with schema validation |
| **Auth** | ![JWT](https://img.shields.io/badge/JWT-000000?style=flat-square&logo=jsonwebtokens&logoColor=white) ![bcrypt](https://img.shields.io/badge/bcryptjs-338?style=flat-square) | Signed sessions, hashed passwords |
| **Tooling** | ![ESLint](https://img.shields.io/badge/ESLint_9-4B32C3?style=flat-square&logo=eslint&logoColor=white) ![Nodemon](https://img.shields.io/badge/Nodemon-76D04B?style=flat-square&logo=nodemon&logoColor=white) | Linting and backend auto-reload |

<br/>

## 📁 Project structure

```
gradyze/
└── Gradyze.com/Gradyze/
    ├── 🟩 BackEnd/
    │   ├── index.js                  # Express app, Mongo connection, static serving
    │   ├── api/admin/
    │   │   ├── adminSignUpRoute.js   # POST /api/admin/signup
    │   │   └── adminLoginRoute.js    # POST /api/admin/adminlogin
    │   ├── middleware/auth.js        # verifyToken (Bearer JWT)
    │   └── models/useradmin.js       # Admin schema → "User" collection
    │
    └── ⚛️ FrontEnd/src/
        ├── App.jsx                   # Route table
        ├── utils/api.js              # Axios instance
        ├── Pages/
        │   ├── LandingPage · AboutUs · contactus · PPSection · Documentation
        │   ├── AdminLogin · AdminSignup · TeacherLogin · StudentLogin
        │   └── AdminDash · TeacherDash · StudentDash
        └── components/
            ├── Navbar · HeroSection · FeaturesSection · PricingSection
            ├── Testimonials · LoginSection · Footor
            ├── PrivateRoute.jsx
            └── DashBoardMenu/        # 12 admin sections
```

<br/>

## 🔌 API

| Method | Endpoint | Body | Returns |
|:-:|---|---|---|
| `POST` | `/api/admin/signup` | `name`, `email`, `password`, `university`, `college` | `201` + `token`, `adminId` |
| `POST` | `/api/admin/adminlogin` | `email`, `password` | `200` + `token`, `adminId`, `name` |

**Frontend routes**

| Route | Page | Access |
|---|---|:-:|
| `/` | Landing page | 🌐 Public |
| `/aboutus` · `/contactus` · `/privacypolicy` · `/documentation` | Info pages | 🌐 Public |
| `/adminlogin` · `/adminsignup` | Admin auth | 🌐 Public |
| `/teacherlogin` · `/studentlogin` | Teacher and student auth | 🌐 Public |
| `/admindash` | Admin dashboard | 🔒 Guarded |

<br/>

## ⚡ Run locally

**Prerequisites:** Node.js 18+ and MongoDB (local or Atlas).

```bash
git clone https://github.com/aniruddha9674/gradyze.git
cd gradyze/Gradyze.com/Gradyze
```

**1. Backend**

```bash
cd BackEnd
npm install
```

Create `BackEnd/.env`:

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017
JWT_SECRET=<long random string>
```

```bash
npm run dev     # nodemon, auto-reload
# or
npm start       # plain node
```

> [!IMPORTANT]
> Set `PORT=5000`. The frontend's Axios client in `FrontEnd/src/utils/api.js` points at `http://localhost:5000`, while the server's fallback port is `5173`.

> [!NOTE]
> The server connects to the `AdminDB` database and stores admins in the `User` collection. Your `MONGO_URI` should be the cluster or server URI without a database name.

**2. Frontend**

```bash
cd ../FrontEnd
npm install
npm run dev     # http://localhost:5173
```

**3. Production build**

```bash
npm run build   # outputs FrontEnd/dist, served by the Express server
```

The backend allows CORS from `http://localhost:5173` with credentials.

<br/>

## 🗺 Roadmap

- [x] Public marketing site (landing, pricing, testimonials, about, contact, privacy, docs)
- [x] Admin signup and login with bcrypt + JWT
- [x] Protected admin dashboard shell with 12-section menu
- [ ] Wire `verifyToken` onto protected API routes
- [ ] Faculty and student management (CRUD)
- [ ] Marks entry and results
- [ ] Attendance tracking and reports
- [ ] Teacher backend + dashboard
- [ ] Student portal with results and timetable
- [ ] Role-based permissions per academic tool
- [ ] Analytics with charts (faculty and student)
- [ ] Email notifications for fee reminders
- [ ] CSV import/export for bulk student records
- [ ] Multi-institution support (tenant per school)

<br/>

## 🤝 Contributing

Personal project, but feedback and issues are welcome. Open an issue or reach out directly.

<br/>

---

<div align="center">

**Aniruddha Nalawade**

[![Email](https://img.shields.io/badge/Email-D14836?style=flat-square&logo=gmail&logoColor=white)](mailto:aniruddhanalawade1234@gmail.com)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/aniruddha9674)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://linkedin.com/in/aniruddha-nalawade)

[⭐ Star the repo](https://github.com/aniruddha9674/gradyze) &nbsp;·&nbsp;
[🐛 Report an issue](https://github.com/aniruddha9674/gradyze/issues)

</div>
