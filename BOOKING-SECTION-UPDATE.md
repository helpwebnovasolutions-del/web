# WebNova Solutions – Schedule Call Section Update

## Changes in this update
- Removed the ActionHubs component from the homepage so the old request-service and quotation forms are no longer shown there.
- Replaced the old Action Hub with a full-width, responsive Schedule a Call section inspired by the supplied reference.
- Added tidy, accessible form fields for name, work email, phone/WhatsApp, service, preferred date, IST time slot, meeting preference, and project details.
- Added meeting choices for Google Meet, Zoom, Phone Call, and WhatsApp Call.
- Added a post-form direct-contact panel with phone, WhatsApp, and email links.
- Removed the WebNova Gullak planner section from the homepage.

## Booking behaviour
Submitting the form generates a booking reference and opens WhatsApp with the request details. This is a booking request only; a meeting is not confirmed automatically. Connect the form to Firebase/Firestore or another backend to persist bookings for admin review and confirmation.

## Run locally
```bash
npm install
npm run dev
```

## Build check
Dependency installation timed out in the build environment, so a production build could not be verified here. Please run `npm install` and `npm run build` locally before deployment.
