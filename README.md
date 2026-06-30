# LevyLink

[![Next.js](https://img.shields.io/badge/Next.js-15-black?style=flat-square&logo=next.js)](https://nextjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue?style=flat-square&logo=typescript)](https://www.typescriptlang.org/)
[![Node.js](https://img.shields.io/badge/Node.js-Express-green?style=flat-square&logo=node.js)](https://nodejs.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Prisma-336791?style=flat-square&logo=postgresql)](https://www.postgresql.org/)
[![Nomba API](https://img.shields.io/badge/Powered_by-Nomba-FF6B35?style=flat-square)](https://nomba.com)

> **A digital levy and dues collection platform for Nigerian student associations.** Each student gets a dedicated virtual account for instant, trackable payments via bank transfer. No cash. No disputes. Complete transparency.

---

## The Problem

Student associations, department executives, and hostel representatives across Nigerian universities manage levy and dues collection entirely through cash or informal bank transfers — creating a broken system at every level:

- **No centralized tracking** — payments go unrecorded, funds disappear
- **No receipts** — students can't prove payment; reps can't verify collection
- **No accountability** — defaulters are never systematically identified
- **Manual chasing** — representatives spend hours tracking hundreds of students
- **Payment disputes** — who paid what and when? Nobody knows
- **Reconciliation nightmare** — matching payments to students is manual, error-prone work

This isn't a hypothetical problem — every Nigerian university student has experienced it.

---

## The Solution

**LevyLink** gives student association leaders a dashboard to create levy campaigns, register students, and track payment status in **real time**. Each registered student is automatically assigned a **dedicated static virtual account number** powered by Nomba's Virtual Accounts API.

### How It Works

1. **Admin creates a levy campaign** with amount, deadline, and student list
2. **Each student is provisioned a unique virtual account number** tied to their matric number
3. **Students pay via normal bank transfer** — from any Nigerian bank app, to their assigned account number
4. **Nomba instantly notifies LevyLink when payment lands** via webhook
5. **Admin dashboard updates in real time** — shows who paid, who hasn't, and total collected
6. **Admin disburses collected funds** to association bank account via Nomba Transfers API

**The student experience is frictionless:** no app download, no account creation, no card details — just a bank transfer to a number they recognize. The most trusted payment behavior in Nigeria, zero friction.

---

## Key Features

### Admin Portal

- 🎯 **Campaign Management** — Create, configure, and close levy campaigns with deadlines and target amounts
- 👥 **Student Management** — Bulk import students via CSV or manual entry; auto-provision virtual accounts per student
- 📊 **Real-Time Dashboard** — Live payment tracking, status overview, and collected amount visualization
- 📈 **Payment Analytics** — Filter by status (Paid/Unpaid/Partial), export student lists, view payment history
- 💸 **Disbursement** — Transfer collected funds to association bank account in one click
- 🔔 **Automated Reminders** — Send payment reminders to unpaid students via email
- 📱 **Mobile Responsive** — Works seamlessly on desktop and tablet

### Student Portal (Zero Authentication)

- 🔍 **Instant Lookup** — Enter matric number, see payment account and status immediately
- 💳 **Account Card** — Displays dedicated account number, amount due, and payment deadline
- ✅ **Live Status Updates** — See payment confirmation within seconds of transfer
- 🧾 **Receipt Download** — Generate and download proof of payment
- 🔄 **Partial Payment Support** — Track and top up incomplete payments seamlessly

### Backend Features

- 🔐 **Webhook Security** — Verify Nomba webhook signatures with zero false positives
- ⚡ **Real-Time Sync** — TanStack Query polling catches payment updates instantly
- 🛡️ **Authentication** — JWT-based admin login with bcrypt password hashing
- 📝 **Audit Trail** — Complete transaction history and payment records
- 🪝 **Webhook Reliability** — Idempotent processing, retry mechanisms, audit logging

---

## Tech Stack

### Frontend

- **Framework:** Next.js 15 (App Router)
- **Language:** TypeScript
- **Styling:** Tailwind CSS
- **State Management:**
  - Zustand (UI state)
  - TanStack Query (server state + polling)
- **Forms:** React Hook Form + Zod (validation)
- **Charts:** Recharts (payment analytics)
- **Deployment:** Vercel

### Backend

- **Runtime:** Node.js
- **Framework:** Express.js
- **Database:** PostgreSQL with Prisma ORM
- **Authentication:** JWT + bcrypt
- **API Testing:** Postman
- **Local Webhook Testing:** ngrok
- **Deployment:** Railway or Render

### Integrations

- **Nomba Virtual Accounts API** — Provision static accounts per student
- **Nomba Webhooks API** — Real-time payment notifications
- **Nomba Transfers API** — Disburse funds to bank accounts
- **Nomba Transactions API** — Payment history and audit trail

### Other

- **Version Control:** GitHub (public repository)
- **Document Storage (optional):** Cloudinary or AWS S3

---

## Architecture

### System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        STUDENT                                  │
│                   (Phone Browser)                               │
└──────────────┬────────────────────────────────────┬─────────────┘
               │                                    │
               │ 1. Bank Transfer                   │ 2. Check Status
               │    to Virtual Account             │    via Lookup Page
               ↓                                    ↓
        ┌─────────────────────────────────────────────────────┐
        │                NOMBA PLATFORM                       │
        │  • Virtual Accounts API (student provisioning)      │
        │  • Webhook API (payment notifications)              │
        │  • Transfers API (disbursement)                     │
        └──────────────┬────────────────┬────────────────────┘
                       │ 3. Webhook     │
                       │ (payment       │
                       │  received)     │
                       ↓                ↓
        ┌──────────────────────────────────────────────────────┐
        │              LEVYLINK BACKEND                        │
        │  • Express API server                               │
        │  • Webhook verification & processing                │
        │  • Student & payment record updates                 │
        │  • PostgreSQL database                              │
        └─────────────────────────┬────────────────────────────┘
                              │ 4. DB Update
                              │    Status: Paid
                              ↓
        ┌──────────────────────────────────────────────────────┐
        │            LEVYLINK FRONTEND (ADMIN)                 │
        │  • Next.js dashboard                                │
        │  • TanStack Query polling (every 3s)                │
        │  • Real-time table updates                          │
        └──────────────────────────────────────────────────────┘
```

### Payment Flow

```
Student Transfer
     ↓
Nomba detects credit to virtual account
     ↓
Nomba fires webhook to LevyLink backend
     ↓
Backend verifies webhook signature
     ↓
Backend matches virtual account to student record
     ↓
Backend marks student as Paid in database
     ↓
Frontend polling cycle picks up the change
     ↓
Table row flashes green (800ms)
     ↓
Status badge updates to ✅ Paid
     ↓
Admin sees it all happen in real time
```

---

## Getting Started

### Prerequisites

- Node.js 18+ and npm/yarn
- PostgreSQL database (local or Neon/Supabase)
- Nomba API credentials (sign up at [nomba.com](https://nomba.com))
- GitHub account (for version control)

### Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/yourusername/levylink.git
   cd levylink
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Set up environment variables**

   Create a `.env.local` file in the root directory:

   ```env
   # Database
   DATABASE_URL="postgresql://user:password@localhost:5432/levylink"

   # JWT
   JWT_SECRET="your_jwt_secret_key_here"

   # Nomba API
   NEXT_PUBLIC_NOMBA_PUBLIC_KEY="your_nomba_public_key"
   NOMBA_SECRET_KEY="your_nomba_secret_key"
   NOMBA_BASE_URL="https://api.nomba.com/v1"

   # Backend (for local development)
   NEXT_PUBLIC_API_URL="http://localhost:3001"
   BACKEND_PORT="3001"

   # Webhook
   WEBHOOK_SECRET="your_webhook_secret_for_verification"
   ```

4. **Set up the database**

   ```bash
   npm run db:migrate
   npm run db:seed  # Optional: seed with sample data
   ```

5. **Start the development server**

   ```bash
   npm run dev
   ```

   Frontend runs on `http://localhost:3000`  
   Backend runs on `http://localhost:3001`

6. **Test webhooks locally**
   ```bash
   ngrok http 3001  # Expose backend to internet
   # Update NOMBA_WEBHOOK_URL in Nomba dashboard with the ngrok URL
   ```

### Build for Production

```bash
npm run build
npm run start
```

---

## Project Structure

```
levylink/
├── app/
│   ├── admin/
│   │   ├── dashboard/          # Overview, stats, active campaigns
│   │   ├── campaigns/          # Campaign list, create, edit
│   │   ├── students/           # Student table, bulk import
│   │   ├── transactions/       # Payment history
│   │   └── disbursements/      # Fund transfer management
│   ├── lookup/                 # Public student payment lookup (no auth)
│   ├── auth/
│   │   ├── login/
│   │   └── register/
│   ├── layout.tsx              # Root layout
│   ├── page.tsx                # Landing/home page
│   └── globals.css
│
├── components/
│   ├── ui/                     # Reusable UI components
│   │   ├── StatCard.tsx
│   │   ├── CampaignCard.tsx
│   │   ├── StatusBadge.tsx
│   │   ├── AccountCard.tsx
│   │   └── ...
│   ├── layout/                 # Layout components
│   │   ├── Sidebar.tsx
│   │   ├── TopBar.tsx
│   │   └── PageWrapper.tsx
│   ├── forms/                  # Form components
│   │   ├── CampaignForm.tsx
│   │   ├── StudentImportForm.tsx
│   │   └── ...
│   └── tables/                 # Table components
│       ├── PaymentTable.tsx
│       └── StudentTable.tsx
│
├── lib/
│   ├── api.ts                  # API client & TanStack Query fetchers
│   ├── store.ts                # Zustand state management
│   ├── utils.ts                # Helper functions (currency, dates, etc.)
│   ├── types.ts                # TypeScript interfaces & types
│   ├── auth.ts                 # Authentication utilities
│   └── validations.ts          # Form validation schemas (Zod)
│
├── hooks/
│   ├── usePaymentStatus.ts     # Polling hook (refetchInterval: 3s)
│   ├── useCampaign.ts
│   ├── useStudents.ts
│   └── useAuth.ts
│
├── server/                     # Backend (Express + Prisma)
│   ├── index.ts                # Server entry point
│   ├── middleware/
│   │   ├── auth.ts             # JWT verification
│   │   ├── errorHandler.ts
│   │   └── webhook.ts          # Webhook signature verification
│   ├── routes/
│   │   ├── auth.ts             # Login, register
│   │   ├── campaigns.ts        # Campaign CRUD
│   │   ├── students.ts         # Student CRUD, virtual accounts
│   │   ├── payments.ts         # Payment status, history
│   │   ├── disbursements.ts    # Fund transfers
│   │   ├── webhooks.ts         # Nomba webhook handler
│   │   └── lookup.ts           # Public student lookup
│   ├── services/
│   │   ├── nombaService.ts     # Nomba API integration
│   │   ├── paymentService.ts   # Payment processing
│   │   ├── authService.ts      # JWT & password hashing
│   │   └── emailService.ts     # Reminder emails
│   └── db/
│       └── schema.prisma       # Prisma schema
│
├── prisma/
│   ├── schema.prisma           # Database schema
│   └── migrations/             # Migration files
│
├── public/                     # Static assets
├── .env.local                  # Environment variables (create locally)
├── .env.example                # Example env file
├── tsconfig.json               # TypeScript config
├── next.config.ts              # Next.js config
├── tailwind.config.ts          # Tailwind CSS config
├── package.json
└── README.md
```

---

## Design System

### Color Palette

| Token             | Value     | Usage                                  |
| ----------------- | --------- | -------------------------------------- |
| **Primary**       | `#16A34A` | CTAs, success states, Paid badges only |
| **Primary Light** | `#DCFCE7` | Success highlights, green badges       |
| **Primary Dark**  | `#15803D` | Primary hover states                   |
| **Ink 900**       | `#0D0D0D` | Headlines, key data                    |
| **Ink 600**       | `#3F3F46` | Body text, labels                      |
| **Ink 400**       | `#A1A1AA` | Subtext, placeholders                  |
| **Ink 100**       | `#F4F4F5` | Page backgrounds, alt rows             |
| **Surface**       | `#FFFFFF` | Card backgrounds                       |
| **Border**        | `#E4E4E7` | Dividers, input borders                |
| **Warning**       | `#F59E0B` | Partial payment states                 |
| **Danger**        | `#EF4444` | Failed, unpaid states                  |
| **Pending**       | `#6366F1` | Processing states                      |

**Philosophy:** Green appears only on primary buttons and success states (✅ Paid). Everything else lives in the ink/zinc scale. This makes green feel earned and trustworthy.

### Typography

- **Display:** Inter — weight 700/800 (page titles, campaign names)
- **Body:** Inter — weight 400/500 (descriptions, table content)
- **Mono:** JetBrains Mono (account numbers, matric numbers, transaction refs)

**Why monospace for account numbers?** They look like real bank details — instantly legible and trustworthy. This is the signature typographic detail.

### Layout Grid

- **Sidebar:** 240px fixed (collapses to icons on tablet)
- **Spacing:** 4px base unit (4, 8, 12, 16, 24, 32, 40, 48, 64px)
- **Border radius:** 6px (inputs), 10px (cards), 16px (panels), 9999px (pill badges)
- **Shadows:** Subtle (cards), deeper (modals)

---

## Admin Flow

```
Land on homepage
  ↓
Register (name, association, email, password)
  ↓
Empty Dashboard → "Create Campaign" CTA
  ↓
Create Campaign (name, amount, deadline)
  ↓
Campaign Card shows 0% → "Add Students" CTA
  ↓
Import Students (CSV or manual entry)
  ↓
Each student auto-provisioned with virtual account number
  ↓
Share payment link with students
  ↓
REAL-TIME LOOP: Watch payments land on dashboard
  ├─ Nomba webhook fires → student marked Paid
  ├─ Table row flashes green for 800ms
  ├─ Payment badge updates to ✅ Paid
  └─ Progress bar moves toward 100%
  ↓
Filter by Unpaid → export list or send reminders
  ↓
Once deadline passes → "Disburse Funds"
  ↓
Confirm transfer details → funds move to assoc bank account
  ↓
Campaign closed, receipt generated
```

---

## Student Flow

```
Receive WhatsApp from association rep
  ("Pay your levy on LevyLink — visit levylink.ng/lookup")
  ↓
Visit levylink.ng/lookup on phone browser
  ↓
Enter matric number
  ↓
See account card with:
  • Dedicated account number
  • Amount due
  • Payment deadline
  ↓
Copy account number to clipboard
  ↓
Open bank app → Transfer amount to account number
  ↓
Return to lookup page after a few seconds
  ↓
Status updates to ✅ PAID with timestamp
  ↓
Download receipt (optional)
```

**No login. No app download. Just bank transfer to a number.**

---

## Signature Design Detail

**The most memorable moment:** When a webhook fires and a student's status flips from Unpaid → Paid, the **table row flashes a subtle green background for 800ms** before settling into the Paid badge. No page reload. No manual refresh. It just happens.

This live moment is what makes LevyLink feel like a real fintech product — payments arriving and reflecting instantly on the dashboard. In the demo video, showing both screens side-by-side (admin dashboard + student payment) is when judges will understand the product's power.

---

## Deployment

### Frontend (Vercel)

```bash
# Connect your GitHub repo to Vercel
# Environment variables are set in Vercel dashboard
# Auto-deploys on git push
```

### Backend (Railway or Render)

```bash
# Connect your GitHub repo to Railway/Render
# Set DATABASE_URL, JWT_SECRET, NOMBA credentials in dashboard
# Auto-deploys on git push
```

### Database (Neon or Supabase PostgreSQL)

- Create a PostgreSQL database on Neon or Supabase
- Get the connection string
- Set DATABASE_URL in backend environment

### Webhook URL (Nomba Dashboard)

- After backend is deployed, get the public URL
- In Nomba dashboard, set webhook URL to: `https://your-backend.com/api/webhooks/nomba`
- Nomba will send payment notifications to this endpoint

---

## Building the MVP

### Phase 1: Foundation (Week 1)

- [ ] Authentication (register, login, JWT)
- [ ] Admin dashboard layout (sidebar, topbar, empty state)
- [ ] Campaign creation flow
- [ ] Student import (CSV + manual)
- [ ] Nomba Virtual Accounts integration

### Phase 2: Core Loop (Week 1-2)

- [ ] Payment table with real-time status
- [ ] TanStack Query polling setup
- [ ] Webhook endpoint for Nomba
- [ ] Webhook signature verification
- [ ] Green flash animation on payment update

### Phase 3: Student Portal (Week 2)

- [ ] Public lookup page
- [ ] Account card display
- [ ] Status updates
- [ ] Receipt generation

### Phase 4: Polish & Disbursement (Week 2)

- [ ] Disbursement flow (Nomba Transfers API)
- [ ] Email reminders to unpaid students
- [ ] Export CSV functionality
- [ ] Error handling & edge cases

---

## Goals & Objectives

1. ✅ **Nomba Integration** — Provision unique static virtual accounts per student programmatically
2. ✅ **Webhook Reliability** — Production-ready endpoint that verifies signatures and updates payment status with zero false positives
3. ✅ **Admin Dashboard** — Real-time payment tracking, campaign management, student list management
4. ✅ **Student Lookup** — Zero-auth public page for students to check status instantly
5. ✅ **Disbursement** — Complete flow for transferring collected funds via Nomba Transfers API
6. ✅ **Live MVP** — Accessible public URL + public GitHub repo with commit history
7. ✅ **Demo Video** — 2–3 minutes showing end-to-end flow (campaign → payment → dashboard update → disbursement)

---

## Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines

- Use TypeScript for all new code
- Follow the existing code structure and naming conventions
- Write tests for critical functions
- Ensure the app works on mobile (students use phones)
- Test webhook integrations thoroughly

---

## License

This project is licensed under the MIT License — see the LICENSE file for details.

---
