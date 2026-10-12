# Gradyze

An academic management platform with role-based dashboards for administrators, teachers, and students — built as a full-stack web application.

<p align="center">
  <img src="https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB" />
  <img src="https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white" />
  <img src="https://img.shields.io/badge/Express.js-000000?style=flat-square&logo=express&logoColor=white" />
  <img src="https://img.shields.io/badge/MongoDB-4EA94B?style=flat-square&logo=mongodb&logoColor=white" />
  <img src="https://img.shields.io/badge/JWT-000000?style=flat-square&logo=jsonwebtokens&logoColor=white" />
</p>

---

## What it does

Gradyze handles the operational side of an academic institution:

- **Public marketing site** — landing page, features, pricing, testimonials, about, contact, privacy
- **Role-based authentication** — separate login flows for Admin, Teacher, and Student
- **Admin workspace** — protected dashboard with academic management tools
- **REST API backend** — Express server with MongoDB for record storage

---

## Architecture

```mermaid
%%{init: {'theme':'dark'}}%%
flowchart TD

subgraph group_entry["Public Experience"]
  node_landing["Landing Page<br/>[LandingPage.jsx]"]
  node_features["Features"]
  node_pricing["Pricing<br/>[PricingSection.jsx]"]
  node_testimonials["Testimonials<br/>[Testimonials.jsx]"]
  node_footer["Footer<br/>[Footer.jsx]"]
  node_about["About<br/>[AboutUs.jsx]"]
  node_contact["Contact<br/>[contactus.jsx]"]
  node_privacy["Privacy Policy<br/>[PPSection.jsx]"]
end

subgraph group_identity["Admin Identity"]
  node_adminlogin["Admin Login<br/>[AdminLogin.jsx]"]
  node_signup["Admin Signup<br/>[AdminSignup.jsx]"]
  node_loginroute["Login Endpoint<br/>[adminLoginRoute.js]"]
  node_signuproute["Signup Endpoint"]
  node_adminmodel[("Admin Records<br/>[useradmin.js]")]
  node_studentlogin["Student Login<br/>[StudentLogin.jsx]"]
  node_teacherlogin["Teacher Login<br/>[TeacherLogin.jsx]"]
end

subgraph group_academic["Academic Workspace"]
  node_admindash["Admin Dashboard<br/>[AdminDash.jsx]"]
  node_dashlayout["Dashboard Layout<br/>[ADashLayout.jsx]"]
  node_dashboardtools["Academic Tools<br/>[DashBoardMenu]"]
end

subgraph group_runtime["Application Runtime"]
  node_app["Route App<br/>[App.jsx]"]
  node_server["API Server<br/>[index.js]"]
  node_private["Route Guard<br/>[PrivateRoute.jsx]"]
end

node_visitor(("Visitor"))

node_visitor -->|"opens"| node_app
node_app -->|"routes"| node_landing
node_app -->|"routes"| node_about
node_app -->|"routes"| node_contact
node_app -->|"routes"| node_privacy
node_app -->|"routes"| node_adminlogin
node_app -->|"routes"| node_signup
node_app -->|"routes"| node_studentlogin
node_app -->|"routes"| node_teacherlogin
node_app -->|"guards"| node_private
node_private -->|"renders"| node_admindash
node_landing -->|"renders"| node_features
node_landing -->|"renders"| node_pricing
node_landing -->|"renders"| node_testimonials
node_landing -->|"renders"| node_footer
node_adminlogin -->|"posts login"| node_loginroute
node_signup -->|"posts signup"| node_signuproute
node_server -->|"mounts route"| node_loginroute
node_server -->|"mounts route"| node_signuproute
node_signuproute -->|"checks and saves"| node_adminmodel
node_loginroute -->|"authenticates"| node_adminmodel
node_admindash -->|"uses layout"| node_dashlayout
node_dashlayout -.->|"links sections"| node_dashboardtools

classDef toneBlue fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#172554
classDef toneAmber fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
classDef toneMint fill:#dcfce7,stroke:#16a34a,stroke-width:1.5px,color:#14532d
classDef toneRose fill:#ffe4e6,stroke:#e11d48,stroke-width:1.5px,color:#881337
classDef toneIndigo fill:#e0e7ff,stroke:#4f46e5,stroke-width:1.5px,color:#312e81

class node_landing,node_features,node_pricing,node_testimonials,node_footer,node_about,node_contact,node_privacy toneBlue
class node_adminlogin,node_signup,node_loginroute,node_signuproute,node_adminmodel,node_studentlogin,node_teacherlogin toneAmber
class node_admindash,node_dashlayout,node_dashboardtools toneMint
class node_app,node_server,node_private toneRose
class node_visitor toneIndigo
```

Generated with [GitDiagram](https://gitdiagram.com).

---

## Project Structure

```
gradyze/
└── Gradyze.com/
    └── Gradyze/
        ├── FrontEnd/
        │   └── src/
        │       ├── Pages/
        │       │   ├── LandingPage/
        │       │   ├── AboutUs/
        │       │   ├── contactus/
        │       │   ├── PPSection/
        │       │   ├── AdminLogin/
        │       │   ├── AdminSignup/
        │       │   ├── StudentLogin/
        │       │   ├── TeacherLogin/
        │       │   └── AdminDash/
        │       └── components/
        │           ├── FeaturesSection/
        │           ├── PricingSection/
        │           ├── Testimonials/
        │           ├── Footor/
        │           ├── DashBoardMenu/
        │           └── PrivateRoute.jsx
        └── BackEnd/
            ├── api/
            │   └── admin/
            │       ├── adminLoginRoute.js
            │       └── adminSignUpRoute.js
            ├── models/
            │   └── useradmin.js
            └── index.js
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React (Vite), React Router, JSX |
| **Backend** | Node.js, Express |
| **Database** | MongoDB (Mongoose) |
| **Auth** | JWT-based role authentication |
| **State / Routing** | Route guards via `PrivateRoute.jsx` |

---

## Features

### Public Site
- **Landing page** with hero, features grid, pricing tiers, testimonials
- **About**, **Contact**, and **Privacy Policy** pages
- Responsive footer across all pages

### Authentication
- Three separate login flows: **Admin**, **Teacher**, **Student**
- Admin signup endpoint with validation
- JWT-based session management
- `PrivateRoute` guard prevents unauthorised dashboard access

### Admin Dashboard
- Protected route: only renders if the user holds a valid admin token
- Layout via `ADashLayout.jsx` — sidebar, header, content area
- Modular **Academic Tools** accessible from the dashboard menu

---

## Getting Started

### Prerequisites
- Node.js 18+
- MongoDB (local or Atlas)

### 1. Clone the repository

```bash
git clone https://github.com/aniruddha9674/gradyze.git
cd gradyze/Gradyze.com/Gradyze
```

### 2. Set up the backend

```bash
cd BackEnd
npm install
```

Create a `.env` file:

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/gradyze
JWT_SECRET=your_jwt_secret_here
```

Start the server:

```bash
npm start
```

### 3. Set up the frontend

```bash
cd ../FrontEnd
npm install
npm run dev
```

The app runs at `http://localhost:5173`.

---

## API Endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/api/admin/signup` | Register a new admin |
| POST | `/api/admin/login` | Authenticate an admin, returns JWT |

---

## Roadmap

- [ ] Teacher dashboard with attendance + grade entry
- [ ] Student portal with results and timetable
- [ ] Role-based permissions per academic tool
- [ ] Attendance analytics with charts
- [ ] Email notifications for fee reminders
- [ ] CSV import/export for bulk student records
- [ ] Multi-institution support (tenant per school)

---

## Contributing

This is a personal project, but feedback and issues are welcome. Open an issue or reach out directly.

---

## Contact

**Aniruddha Nalawade**  
📧 aniruddhanalawade1234@gmail.com  
🔗 [GitHub](https://github.com/aniruddha9674) · [LinkedIn](https://linkedin.com/in/aniruddha-nalawade)