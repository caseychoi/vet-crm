# Veterinary CRM Architecture & Design

## System Overview

The Veterinary CRM is a modern, responsive web application tailored for small-to-medium sized veterinary clinics. It provides an intuitive interface for managing pet owners (Clients), their pets (Patients), medical records, and daily clinic schedules.

## Technology Stack

The application is built using a decoupled client-server architecture:

### Frontend (Client)
* **Framework:** Next.js 14+ (App Router)
* **UI Library:** React
* **Styling:** Tailwind CSS (for rapid, responsive, and highly customizable UI)
* **Icons:** Lucide-React
* **Design Pattern:** A sleek, glassmorphism-inspired "Dashboard" layout optimized for tablets and desktops commonly used in clinic reception areas and exam rooms.

### Backend (API Server)
* **Framework:** FastAPI (Python)
* **ORM:** SQLModel (combining SQLAlchemy for database interactions and Pydantic for data validation/serialization)
* **Database:** SQLite (currently used for rapid local development), engineered to be instantly swappable to PostgreSQL (via Supabase) for production.
* **Migrations:** Alembic (for robust database schema versioning)

## Data Model

The core relational database schema revolves around these primary entities:

1. **Client (Pet Owner)**
   - `id` (UUID)
   - `first_name`, `last_name`, `phone`, `email`
   - Has a one-to-many relationship with `Patient`.

2. **Patient (Pet)**
   - `id` (UUID)
   - `client_id` (Foreign Key -> Client)
   - `name`, `species`, `breed`, `dob`, `weight_kg`
   - `alert_flags` (e.g., "Allergic to Penicillin", "Bites")

3. **Staff (Clinic Employee)**
   - `id` (UUID)
   - `name`, `role` (Admin, Veterinarian, Vet Tech, Receptionist)

4. **Appointment**
   - `id` (UUID)
   - `patient_id` (Foreign Key -> Patient)
   - `vet_id` (Foreign Key -> Staff)
   - `start_time`, `end_time`
   - `type` (Checkup, Surgery, Vaccination)
   - `status` (Scheduled, Checked_In, Completed, Cancelled)

5. **Medical Record**
   - `id` (UUID)
   - `patient_id` (Foreign Key -> Patient)
   - `vet_id` (Foreign Key -> Staff)
   - Contains SOAP notes: `subjective`, `objective`, `assessment`, `plan`

6. **Billing & Invoicing**
   - `Invoice` (UUID, `client_id`, `amount_due`, `status`)
   - `InvoiceItem` (Line items linked to `Invoice`)
   - `Payment` (Tracks Stripe integration, `stripe_payment_intent_id`)

7. **Settings & Clinic Configuration**
   - `ClinicProfile` (Stores global clinic name, address, tax_rate)

## Key Design Decisions

1. **Decoupled Architecture:** By separating the Next.js frontend from the FastAPI backend, we ensure that the frontend can be hosted independently while the backend scales on specialized Python infrastructure.
2. **SQLModel for Backend:** Choosing SQLModel drastically reduces boilerplate. A single class definition serves as both the SQLAlchemy schema and the Pydantic API validation schema.
3. **Optimized User Experience:** The UI is designed to minimize clicks. Features like the "Quick Actions" dropdown on the Dashboard ensure that fast-paced clinic staff can enter data (New Patient, Schedule Appointment) without navigating away from their primary view.
4. **Data Security & Backup:** Local SQLite database snapshots can be taken and restored directly from the Admin Settings UI, ensuring local data continuity.

## Future Implementation Roadmap

* **Phase 5:** Supabase Authentication and robust Role-Based Access Control (RBAC).
* **Phase 6:** Automated SMS Reminders (Twilio integration) for upcoming appointments.
