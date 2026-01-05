# Software Requirements Specification (SRS)
## Project: PaisaChait (Clone) Financial Management System

| Document ID | SRS-PaisaChait-1.0 |
| :---------- | :----------------- |
| **Version** | 1.0                |
| **Date**    | October 26, 2023   |
| **Author**  | System Architect   |
| **Status**  | Draft              |

---

## 1. Introduction

### 1.1 Purpose
The purpose of this document is to outline the functional and non-functional requirements for the **Karobar** platform. Karobar is a web-based financial management dashboard designed for small to medium enterprises (SMEs) to manage cash flow, track transactions with parties, and monitor sales and purchases.

### 1.2 Scope
The application consists of a React/TypeScript frontend consuming a REST/GraphQL API. The scope covers:
1.  **Authentication Module:** Secure login, registration, email verification, and password recovery.
2.  **Dashboard Module:** Real-time visualization of financial KPIs, cashflow charts, and reminders.
3.  **Parties Module:** Management of debtors, creditors, and transaction history.

### 1.3 Definitions, Acronyms, and Abbreviations
*   **KPI:** Key Performance Indicator.
*   **Party:** A business entity (customer or supplier) with which the user conducts transactions.
*   **To Receive:** Total outstanding amount owed to the user by parties.
*   **To Give:** Total outstanding amount the user owes to parties.
*   **Poush:** A month in the Nepali Bikram Sambat calendar (used as a filter label).

---

## 2. System Overview

### 2.1 Design Philosophy
The UI adopts a **Dark Mode First** aesthetic to reduce eye strain and provide a modern look.
*   **Auth Zone:** High contrast "Red & Black" theme for security focus and visual distinctness.
*   **App Zone:** Deep "Slate/Blue Gray" theme with modular card-based layouts for data density.

### 2.2 User Characteristics
*   **Business Owner:** Requires quick access to "To Receive" and "To Give" figures. Mobile responsiveness is critical.
*   **Accountant:** Requires detailed views of party transactions and data entry forms.

---

## 3. Functional Requirements

### 3.1 Authentication (AUTH)
The system shall provide a secure entry point utilizing the following flows, derived from the `User` schema.

| ID | Requirement | Description |
| :--- | :--- | :--- |
| **AUTH-01** | **User Login** | Users shall be able to log in using `email` and `password`. Upon success, the `User.lastLogin` timestamp must be updated. |
| **AUTH-02** | **User Registration** | New users must provide `name`, `email`, and `password`. The system shall enforce password complexity rules. The initial state of `User.isVerified` shall be `false`. |
| **AUTH-03** | **Email Verification** | Post-registration, the user must verify their email via a 6-digit OTP. The system validates `verificationToken` and `verificationTokenExpiresAt`. |
| **AUTH-04** | **Password Recovery** | Users may request a password reset via email. This utilizes `resetPasswordToken` and `resetPasswordExpiresAt`. |
| **AUTH-05** | **Session Management** | The system shall maintain a session (JWT) until the user logs out or the token expires. |

### 3.2 Dashboard (DASH)
The dashboard serves as the landing page post-login.

| ID | Requirement | Description |
| :--- | :--- | :--- |
| **DASH-01** | **KPI Display** | The system must display four key metrics: "To Receive", "To Give", "Sales (Poush)", and "Purchase (Poush)". |
| **DASH-02** | **Cashflow Visualization** | The system shall render a line or bar chart titled "Cashflow (Last 7 Days)". |
| **DASH-03** | **Total Balance** | The system must display the aggregate cash and bank balance. |
| **DASH-04** | **Reminder Widget** | The system shall list "Upcoming Reminders". If empty, display "0". A button to "Add New Reminder" must be present. |

### 3.3 Parties Management (PARTY)
This module manages external entities and their ledgers.

| ID | Requirement | Description |
| :--- | :--- | :--- |
| **PARTY-01** | **Party Listing** | The system shall display a list of all parties. The header must show the total count (e.g., "Parties (2)"). |
| **PARTY-02** | **Party Creation** | A button "+ Add Party" must be available to create new records. |
| **PARTY-03** | **Balance Indication** | For each party, the system must show the current balance (e.g., "Rs. 666"). |
| **PARTY-04** | **Status Logic** | Positive balances should be labeled "To Receive". Zero balances should indicate "Settled". |
| **PARTY-05** | **Master-Detail View** | <br>1. **Left/Center:** List of parties.<br>2. **Right Panel:** Defaults to "Party Not Selected". Clicking a party loads their specific transaction history in this panel. |

---

## 4. Non-Functional Requirements

### 4.1 Performance
*   **Latency:** API calls must respond within 200ms for dashboard data.
*   **Rendering:** The dashboard chart should render within 1 second on mobile devices.

### 4.2 Security
*   **Encryption:** Passwords must never be stored in plain text (handled by backend).
*   **XSS Prevention:** All user inputs in forms (Name, Party Names) must be sanitized before rendering in the DOM.

### 4.3 Usability
*   **Responsiveness:** The layout must collapse the sidebar and stack the Party Master-Detail views on screens smaller than 768px.
*   **Accessibility:** Color contrast ratios must meet WCAG AA standards, particularly in the Dark Mode theme.

---

## 5. Data Model Specifications

The frontend interacts with the following database schema structure:

### 5.1 User Table
```prisma
model User {
  id                        Int       @id @default(autoincrement())
  email                     String    @unique
  password                  String    // Hashed
  name                      String
  lastLogin                 DateTime  @default(now())
  isVerified                Boolean   @default(false)
  resetPasswordToken        String?   @db.Text
  resetPasswordExpiresAt    DateTime?
  verificationToken         String?   @db.Text
  verificationTokenExpiresAt DateTime?
  createdAt                 DateTime  @default(now())
  updatedAt                 DateTime  @updatedAt
}
```

### 5.2 Frontend DTOs (Data Transfer Objects)
*   **AuthDTO:** `{ email, password }`
*   **StatsDTO:** `{ toReceive: number, toGive: number, sales: number, purchase: number }`
*   **PartyDTO:** `{ id: string, name: string, balance: number, type: 'CUSTOMER' | 'SUPPLIER' }`

---

## 6. Technical Documentation (Frontend)

### 6.1 Technology Stack
*   **Framework:** React 18+ with TypeScript.
*   **Styling:** Tailwind CSS (Utility-first).
*   **Routing:** React Router v6.
*   **State:** React Context API for Auth; Zustand or TanStack Query for Server State.
*   **Charts:** Recharts.

### 6.2 Project Structure
```
/src
  /assets          # Images, fonts
  /components
    /auth          # Login, Register, Verify components
    /dashboard     # StatCard, CashflowChart, ReminderWidget
    /parties       # PartyList, PartyDetail
    /layout        # Sidebar, Topbar, MainLayout
  /context         # AuthContext.tsx
  /hooks           # useAuth, usePartyData
  /pages           # Route components
  /services        # API client (Axios)
  /types           # TypeScript interfaces
  /utils           # Helper functions (formatters)
```

### 6.3 Color Palette Specification

#### A. Authentication Theme
*   **Background:** `#000000` (Black)
*   **Surface:** `#111111` (Very Dark Gray)
*   **Primary Accent:** `#EF4444` (Red 500)
*   **Text:** `#FFFFFF` (White)

#### B. Application Theme
*   **Background:** `#0F172A` (Slate 900)
*   **Card Surface:** `#1E293B` (Slate 800)
*   **Border:** `#334155` (Slate 700)
*   **Text Main:** `#F8FAFC` (Slate 50)
*   **Text Muted:** `#94A3B8` (Slate 400)

---

## 7. Appendices

### Appendix A: UI Wireframe References
*   **Fig 1 (Dashboard):** Displays "Welcome Bikram Dhimal", Sidebar, KPI Cards, and Cashflow Chart.
*   **Fig 2 (Parties):** Split view with Party List (jhhji - Rs. 666) and Right Panel ("Party Not Selected").
*   **Fig 3 (Auth):** Red bordered "Welcome Back" form and "Create Account" form on black background.