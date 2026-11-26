# WebNova Solutions — Website Package

Premium, responsive WebNova Solutions website built with React, Vite, TypeScript, Tailwind CSS, and Lucide icons.

## Updates in this package

- Added the supplied WebNova Solutions logo (`public/webnova-logo.png`) and favicon mark.
- Updated footer social links for Instagram, LinkedIn, YouTube, Facebook, and WhatsApp.
- Removed the public “Verify Offer” / credential verification section from the homepage.
- Aligned brand styling with WebNova’s navy, electric blue, cyan, and violet identity.
- Added a branded 404 page (`public/404.html`) and an app-level not-found screen.
- Added `robots.txt` and `sitemap.xml` for the public domain.
- Updated the footer contact number to +91 87650 51912.

## Run locally

Prerequisite: Node.js 20.19+ or 22.12+ recommended for the current Vite version.

```bash
npm install
npm run dev
```

Open the local URL printed by Vite. To create a production build:

```bash
npm run build
npm run preview
```

## Deploy to Cloudflare Pages

1. Push/extract the project into your repository.
2. In Cloudflare Pages, select the repository and use `npm run build` as the build command.
3. Set the build output directory to `dist`.
4. Add any required environment variables from `.env.example` in the Cloudflare Pages project settings.
5. Deploy, then test the homepage, footer social links, mobile navigation, and a non-existent URL (404).

## Social links

Instagram, LinkedIn, YouTube, Facebook, and WhatsApp link to WebNova Solutions’ official social profiles.

## Important

The site includes client-side admin and client workspace features. Review any forms, authentication, and API keys before public production use. Never commit real secrets to the repository.

## Admin, client portal, chatbot and support tickets

- `/portal` — client sign-in form.
- `/admin` — admin sign-in page. Connect Firebase Authentication before enabling access.
- Floating **Help & Support** button — FAQ-style Nova chatbot and a support ticket form.
- Tickets are currently stored in this browser's `localStorage`; admin can search tickets and change their status in the same browser.

**Important production note:** the UI requires a backend to become a secure multi-user service. Before production, connect Firebase Authentication or another backend, implement server-side authentication and role-based authorization, database access policies, ticket ownership, and email notifications. Replace the FAQ chatbot replies with a securely configured AI/API integration if desired.

Cloudflare Pages SPA fallback is configured so direct visits to `/blog`, `/admin`, `/portal`, `/user`, and other app routes load the React application.


## Authentication

The admin and client sign-in forms are UI placeholders. Connect Firebase Authentication and configure authorized roles before deployment. No hardcoded login credentials are included.


## Book a call on the homepage

The main page includes a **Schedule a Call** card with date, time, and preferred meeting platform: Zoom, Google Meet, or phone call. Submitting creates a booking reference and opens WhatsApp with the booking request to WebNova at `+91 87650 51912`. This does not automatically create a Zoom/Meet room or email a calendar invitation; confirm the booking manually and send the real meeting link to the client.


## WebNova Insights Blog

The dedicated `/blog` page includes a responsive WebNova Insights layout inspired by the supplied design reference: gradient hero, featured article, search, category filters, article cards, reading modal, and newsletter signup UI.

### Manage posts in Admin
1. Open `/admin` and sign in with your Firebase administrator account.
2. Select **Blog** from the admin sidebar.
3. Create, edit, publish/unpublish, or delete articles. Published posts appear in the public Blog page.

**Storage limitation:** posts are stored in this browser's `localStorage`. They are not shared between browsers/devices and may be cleared by the user. For live production editing, connect a shared database (Supabase or Firebase), secure admin permissions server-side, and store cover images in object storage. Newsletter signup is a UI preview and does not send email until an email service is connected.


## Dedicated blog and document administration

- Public blog page: `/blog` (the blog is not embedded in the homepage).
- Admin dashboard: `/admin`, then open **Blog**, **Certificates**, or **Invoices**.
- Public certificate check page: `/verify-certificate` (enter the certificate ID issued by the admin panel).
- Admin certificate registry supports intern name/email, certificate title, optional PDF/image upload, generated ID, and Valid/Revoked status.
- Invoice manager supports client, invoice number, amount, and optional PDF/image attachment.

**Important storage limitation:** blog posts, certificate records, invoices, and uploaded files are saved to browser local storage. The public verifier can only see records in that same browser profile. Before using this for real interns or clients, connect a shared database and private file storage, enforce server-side admin permissions, and publish certificate validation through a server endpoint. Large files may exceed browser storage limits.


## Form security and Admin Form Inbox

- A lightweight human-check challenge is added to every HTML form, including contact, quotation, booking, newsletter, login, support-ticket, invoice, and certificate forms.
- Completed form attempts are listed in Admin → **Form Inbox** and can be searched, viewed, deleted, or exported to CSV. Passwords, OTPs, tokens, secrets, and payment-card-related fields are intentionally excluded from capture. File inputs record filenames only, not the file contents.
- Current storage uses the visitor's browser `localStorage`. This is useful for local UI testing only: submissions from other visitors/devices do not automatically appear in the admin inbox. Connect Firestore/Supabase before production to persist submissions centrally with access rules.
- The included arithmetic challenge is a lightweight client-side anti-bot check, not secure CAPTCHA. For production, configure `VITE_TURNSTILE_SITE_KEY` and the `TURNSTILE_SECRET_KEY` secret in Cloudflare Pages. The included Pages Function at `functions/api/verify-turnstile.ts` verifies Turnstile tokens server-side before allowing a form submit. If the public site key is not configured, the site uses the lightweight arithmetic fallback. Never rely on a client-side challenge alone.
- Do not collect passwords, authentication secrets, payment-card details, or unnecessary sensitive data in the admin inbox.


## Firebase Authentication and shared Form Inbox

This package includes Firebase email/password authentication, client registration, Google sign-in for clients, email verification, password reset, role-gated `/admin` and `/portal` workspaces, and a Firestore-backed Form Inbox when Firebase is configured. Admin self-registration is disabled.

### 1. Configure Firebase

1. Create or open your project at https://console.firebase.google.com/.
2. In **Authentication → Sign-in method**, enable **Email/Password**. Enable **Google** if you want Google sign-in for clients.
3. In **Authentication → Settings → Authorized domains**, add your deployed domain and `localhost` for local testing.
4. In **Project settings → General → Your apps**, create a Web App and copy its configuration values into `.env` using the names in `.env.example`. For Cloudflare Pages, add the same `VITE_FIREBASE_*` values under **Settings → Environment variables**, then redeploy.
5. Create the Firestore database and deploy the included `firestore.rules` from the Firebase Console Rules tab (or with Firebase CLI).

### 2. Create the first admin safely

1. Create the administrator account through Firebase Console → Authentication → Add user, or register a normal account first and verify the email.
2. Copy the user's **UID** from Authentication.
3. In Firestore, create `users/{UID}` with fields: `uid` (string, same UID), `email` (string), `displayName` (string), `role` (string: `admin`), `createdAt` (timestamp), and `updatedAt` (timestamp).
4. Sign in at `/admin`. Admin role must be assigned manually by the project owner; public registration can only create `client` profiles.

### 3. Client authentication

- `/portal`: client sign in, client registration, Google sign-in, and password reset.
- `/admin`: admin email/password sign in only. Admin self-registration is intentionally blocked.
- New client accounts receive a Firestore `users/{uid}` profile with role `client` and must verify their email before opening the portal.
- Passwords are never saved to the Form Inbox. The global form capture intentionally excludes password, OTP, token, secret, and payment-card-like fields.

### 4. Shared Admin Form Inbox

Once Firebase config and Firestore rules are active, successful website form submissions are written to `formSubmissions`. Admin → **Form Inbox** listens for shared records from other visitors/devices. The inbox retains a local-browser fallback for older entries. The provided rules allow public creation of non-secret form records but only an authenticated Firestore `users/{uid}` document with `role: admin` can read or delete submissions.

### 5. CAPTCHA notes

The global form guard adds a lightweight local human-check by default. For production, configure Cloudflare Turnstile using `VITE_TURNSTILE_SITE_KEY` and the server-side `TURNSTILE_SECRET_KEY` in Cloudflare Pages. Client-side CAPTCHA alone can be bypassed; keep rate limiting and server verification in place. Do not store authentication credentials, OTPs, or other secrets in form submissions.

### Remaining production integrations

This change connects authentication profiles and central Form Inbox submissions. Existing ticket, blog, invoice, certificate, and booking modules still contain browser-local storage paths; migrate those records to Firestore/Cloud Storage with owner-based access rules before treating them as a multi-user production system. Do not enable real client document uploads until private storage rules are configured.

### First-visit service enquiry + SEO updates

- The homepage now shows a dismissible service-enquiry form after a short delay, once per browser tab. It asks for name, email, optional phone, requested service, estimated budget, project requirements, and contact consent.
- The form is labelled `Service Request Form` and uses named, non-secret fields so the existing global form capture adds it to **Admin → Form Inbox**. Local browser capture works immediately; shared cross-device admin access requires Firebase configuration and deployed Firestore rules.
- The prompt can be dismissed and does not block visitors from using the website. It is not shown on admin, portal, blog, or certificate routes.
- Homepage SEO metadata now includes a clearer service-focused title/description, canonical URL, Open Graph/Twitter metadata, robots directives, and Organization structured data.
- Keep the form submission disclosure and consent checkbox. Do not capture passwords, OTPs, authentication tokens, or payment details.


## Schedule Call Admin System

- Call requests are recorded with a unique `WN-CALL-...` reference.
- Admins can open **Admin Dashboard → Call Bookings** to search requests, review the contact details and preferred date/time, export CSV, contact the requester by email/WhatsApp, and update status.
- Configure Firebase using `.env.example`, create Firestore, and deploy `firestore.rules` for cross-device/shared storage. Public visitors can create a pending booking; only an authenticated account whose `users/{uid}.role` is `admin` can read or update booking status.
- Without Firebase configuration, bookings are stored only in the browser's local storage and won't sync to other devices.
- Booking requests are not automatically confirmed and do not create Zoom/Google Meet links; the admin must confirm the time and send the meeting details.

## Production setup: EmailJS and Freelancer page

### Freelancer page
- The public `/freelancer` route now displays a responsive WebNova Freelancer Network launch page.
- The main navigation includes a Freelancers link.

### EmailJS configuration
Set these values in your hosting provider's environment variables (and in a local `.env` for local development):
- `VITE_EMAILJS_PUBLIC_KEY`
- `VITE_EMAILJS_SERVICE_ID`
- `VITE_EMAILJS_OTP_TEMPLATE_ID`
- `VITE_EMAILJS_WELCOME_TEMPLATE_ID`
- `VITE_EMAILJS_FEEDBACK_TEMPLATE_ID`
- `VITE_EMAILJS_CONTACT_TEMPLATE_ID`

The contact form is connected to the EmailJS contact template. The shared `src/lib/emailjs.ts` helper exposes the four named templates (`otp`, `welcome`, `feedback`, `contact`) for use by the corresponding flows. Configure matching template variables in EmailJS; the contact template receives `from_name`, `reply_to`, `email`, `message`, and `to_email`.

Important: EmailJS template IDs and public key are deployment configuration, not values that can be guessed from the project. Replace the `YOUR_...` placeholders with the real values from your EmailJS dashboard. Do not place private service credentials in client-side variables. Do not use a client-generated EmailJS OTP as the sole proof of identity; OTP generation, expiry, rate limiting, and verification should be enforced by a trusted backend. Existing account sign-in and email verification use Firebase Authentication.

### Authentication flow (current Firebase implementation)
1. Configure the Firebase web app values in `.env` using `.env.example` as a guide.
2. Enable Email/Password sign-in in Firebase Authentication.
3. Create the authorized admin account and configure its role/claims or the role record expected by the application. Do not make a user an admin based only on a client-side field.
4. A client registers or signs in from `/portal`; Firebase manages credentials.
5. On registration, the app sends a Firebase email-verification link. The user opens the link and returns to the app.
6. Until the email is verified, the protected route displays a verification screen and allows the user to resend the verification email.
7. The app checks the account role before granting access to the client or admin workspace (`/portal` and `/admin`).
8. For password recovery, use the reset-password action on the sign-in screen; Firebase sends the reset link.
9. Before production, verify Firestore rules and admin role provisioning in the deployed Firebase project, enable abuse protection, and test registration, verification, login, logout, and password reset end to end.

### Pre-launch checks
- Fill all EmailJS and Firebase environment variables in the deployment platform.
- Confirm the EmailJS contact template's recipient/reply-to settings and send a real test message.
- Configure the OTP and welcome templates before wiring them to an authentication flow; configure the feedback template before adding a feedback submission flow.
- Run `npm install`, `npm run lint`, and `npm run build`; then smoke-test `/`, `/freelancer`, `/blog`, `/portal`, `/admin`, and `/verify-certificate` on the deployed site.

## Production readiness notes (October 2026)
- Public legal pages are available at `/privacy-policy` and `/terms-and-conditions`. Review the wording against your actual data practices and have it legally reviewed before launch.
- The Admin > Settings panel includes a Google Reviews visibility toggle. In this version it is stored in the current browser only; use a secured shared configuration document in Firebase if the setting must affect every visitor/device.
- No shared admin/client password is hard-coded into this frontend. Create accounts in Firebase Authentication and assign roles through your trusted administrative process and Firestore rules. Never put credentials in source code or Vite `VITE_*` variables.
- Search `src/` for demo/dummy/sample-data placeholders before release; no explicit demo/dummy/lorem-ipsum strings were found in source during this update. Run `npm run lint` and `npm run build` before deployment.


## Public forms, admin inbox, support workflow, and database setup

- Public website forms are captured by `FormSecurityAndCapture.tsx`; non-secret form fields are stored locally as a fallback and submitted to Firestore `formSubmissions` when Firebase is configured.
- Admin > Form Inbox listens for shared Firestore submissions. Ensure the signed-in administrator has a `users/{uid}` document with `role: "admin"` (create this securely in Firebase Console or with trusted server tooling; never allow users to self-assign admin).
- Public support tickets can be submitted without exposing the admin workspace. With Firebase configured, they are stored in `supportTickets`; the admin dashboard receives live updates and can set workflow status and a customer-facing status report.
- `firestore.rules` now permits public ticket creation with field/length validation, while only admins can read/update/delete tickets. Customer and project collections are admin-only until per-customer ownership rules are implemented.
- Certificate and invoice management currently uses browser local storage. For real cross-device certificate verification, move records to Firestore and uploaded files to Firebase Storage, then add strict admin-only write rules and public read access limited to certificate ID + verification-safe fields. Do not make invoices or private customer details public.
- `/admin` must remain protected. “Make the website public” means public visitors can access the marketing site and submit forms; it must not mean making private customer messages, tickets, invoices, or admin controls publicly readable.
- Google review CTA wording was updated to request honest, constructive feedback. Confirm the Google review URL points to the correct WebNova business profile before publishing.

## Certificate admin and public verification

The admin workspace includes **Certificates** at `/dashboard` for users whose Firestore profile has `role: "admin"`. Admins can issue a certificate ID, search the registry, revoke a certificate, restore a revoked certificate, and delete a record. The public `/verify-certificate` page looks up that exact certificate ID in Firestore and shows only the intern name, certificate title, issue date, and current status.

### Enable it in Firebase

1. Enable Firebase Authentication and sign in with the account intended to be the administrator.
2. In Firebase Console → Firestore Database, create/update `users/{ADMIN_UID}` with the authenticated administrator's exact UID as the document ID and `role: "admin"`. Do not let a public registration form set this role; client registration is restricted to `role: "client"` by the rules.
3. Publish the project's `firestore.rules` file in Firestore Database → Rules.
4. Test the admin certificate workflow while signed in as the admin. Certificate metadata is stored in `certificates/{certificateId}` (admin-only); a sanitized public mirror is stored in `certificateVerification/{certificateId}`. The public verifier can read a single record by exact ID but cannot list records or modify status.
5. Test the public `/verify-certificate?code=WN-CERT-...` URL in a signed-out/incognito browser. Issue, revoke, and restore a test certificate and verify that the public result follows the status.

Certificate files are not uploaded by this registry yet; only metadata and validation status are cloud-backed. Invoice records remain browser-local. Do not use local-only invoice storage for real financial documents; configure private Firebase Storage and per-customer access rules before production use.
