# WebNova Firebase Authentication & workspace roles

This project uses Firebase Authentication and Firestore. The app must point to the same Firebase project that contains your existing users and Firestore database.

## 1. Set the correct Firebase project (important)

This build is configured for your supplied WebNova Solutions Firebase project: `authenticationwebnovasolutions`. The app uses these values as defaults; Netlify environment variables can override them if needed.

In Netlify: Site configuration → Environment variables, add:

- `VITE_FIREBASE_API_KEY`
- `VITE_FIREBASE_AUTH_DOMAIN`
- `VITE_FIREBASE_PROJECT_ID`
- `VITE_FIREBASE_STORAGE_BUCKET`
- `VITE_FIREBASE_MESSAGING_SENDER_ID`
- `VITE_FIREBASE_APP_ID`
- `VITE_FIREBASE_DATABASE_URL` (if using Realtime Database)
- `VITE_FIREBASE_MEASUREMENT_ID` (optional)

The supplied project configuration is already included in `src/firebase.ts` and `.env.example`. If you add/override environment variables in Netlify, redeploy because Vite embeds them at build time. For local development, copy `.env.example` to `.env` and fill in the same values.

## 2. Enable sign-in providers and authorized domains

Firebase Console → Authentication → Sign-in method:
- Enable Email/Password.
- Enable Google if client Google sign-in is wanted and configure the support email.
- Enable Phone and configure the SMS region policy. Add your deployed website hostname under Authentication → Settings → Authorized domains. Phone sign-in uses Firebase reCAPTCHA verification; test SMS numbers can be configured in Firebase Authentication for development.
- Authentication → Settings → Authorized domains: add your production Netlify domain and `webnovasolutions.co.in` (plus any actual custom preview domain you use).

## 3. Configure the administrator role

1. Firebase Console → Authentication → Users → open your administrator account and copy its **User UID**.
2. Firestore Database → Data → collection `users` → create a document with the document ID exactly equal to that UID.
3. Add fields:
   - `uid` — string — exact same UID
   - `email` — string — administrator email
   - `displayName` — string — administrator name
   - `role` — string — `admin`
4. Save and sign in at `/admin`.

Do this manually in the Firebase Console as project owner. Never set `role: admin` through public registration or client-side code. Console edits are privileged and bypass Firestore rules.

## 4. Client accounts and missing-profile repair

Clients register at `/portal`; email/password accounts write `users/{their-auth-uid}` with `role: client` and require email verification. Phone OTP and Google sign-ins can create a client profile with `role: client`. Older signed-in client accounts with a missing profile are repaired as `client` only. Firestore rules prohibit users from promoting their own role to admin.

## 5. Publish Firestore rules

Review `firestore.rules`, then publish them in Firebase Console → Firestore Database → Rules. The rules allow users to create only their own `client` profile, admins to read workspace submissions, and restrict all other unspecified collections by default. Add explicit rules before enabling additional Firestore-backed features.

## 6. Test after redeploy

- `/portal`: create a client account, verify email, sign in, refresh, and sign out.
- `/portal`: test Google sign-in if enabled.
- `/portal`: test phone OTP with an E.164 number (for example, +919876543210).
- `/portal`: try password reset.
- `/admin`: sign in with the administrator account after assigning `role: admin`.
- Confirm a client cannot open `/admin` and cannot read another user's profile.
- If a sign-in method returns `auth/operation-not-allowed`, enable that provider in Firebase Console.
- If it returns `auth/unauthorized-domain`, add the exact current site hostname to Authorized domains.

## Security notes

Firebase web API keys are public client configuration, not server secrets. Never put service-account JSON, private keys, email passwords, or Turnstile secret keys in `VITE_*` variables or browser code. Use Firestore Security Rules for authorization; a hidden route or frontend check is not sufficient.
