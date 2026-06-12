# Password Manager PWA

A secure, installable Password Manager built as a single-file Progressive Web App. Vault data is encrypted client-side with AES-256 before it ever touches storage — locally or in the cloud.

## Features

- **PIN-protected vault** — 6-digit PIN required on every app launch
- **AES-256 encryption** — all credential data is encrypted with your PIN before being saved
- **Security Question recovery** — reset a forgotten PIN by answering your security question (re-encrypts the vault with the new PIN)
- **Fingerprint / Face ID unlock** — optional biometric unlock via WebAuthn, toggle on/off anytime in Settings
- **Guest mode by default** — works fully offline with local storage; sign in with Google only if you want cloud sync
- **Google Sign-In + Firestore sync** — encrypted vault backup across devices (Firebase never sees plaintext data)
- **App-based organization** — group credentials by app/service, sorted alphabetically (A–Z)
- **Per-app detail view** — Google Password Manager–style layout: username + copy, masked password + reveal + copy, edit/delete
- **Copy to clipboard** — one-tap copy for usernames and passwords
- **Browser-style back navigation** — Android back button navigates through screens/modals before exiting
- **Installable PWA** — add to home screen on mobile or desktop with custom icon and name
- **Change PIN / Change Security Q&A** — available anytime from Settings

## Tech Stack

- Vanilla HTML, CSS, JavaScript (single file, no build step)
- [CryptoJS](https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.2.0/crypto-js.min.js) — AES-256 encryption & SHA-256 hashing
- [Firebase](https://firebase.google.com/) — Authentication (Google Sign-In) + Firestore (encrypted cloud backup)
- [WebAuthn API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API) — fingerprint/biometric unlock

## Setup

### 1. Firebase Configuration

Create a Firebase project at [console.firebase.google.com](https://console.firebase.google.com/) and:

1. Enable **Authentication → Google** sign-in provider
2. Create a **Firestore Database** (production mode)
3. Set Firestore security rules:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /vaults/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

4. Copy your Firebase config into the `FB_CFG` object near the top of the `<script>` section in `password-manager.html`:

```javascript
const FB_CFG = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.firebasestorage.app",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

### 2. Hosting (required for Fingerprint Auth)

WebAuthn (fingerprint/Face ID) only works over **HTTPS** (or `localhost`). Host the file using any static hosting provider:

- GitHub Pages
- Netlify
- Vercel
- Firebase Hosting

### 3. Install as an App

Open the hosted URL in Chrome (Android) or Safari (iOS):

- **Android (Chrome):** Menu → "Add to Home screen"
- **iOS (Safari):** Share → "Add to Home Screen"

The app will launch full-screen with its own icon and name, just like a native app.

## First-Time Setup

1. Create a 6-digit PIN and confirm it
2. Set up a security question + answer (used for PIN recovery)
3. Start adding apps and credentials

## Usage

- Tap **+** to add a new app/service
- Tap an app card to view its credentials
- Tap **+** inside an app to add a username/password pair
- Use the eye icon to reveal a password (auto-hides after 10 seconds)
- Use the copy icons to copy username/password to clipboard
- Edit or delete any credential from its detail view

## Settings

- **Change PIN** — verify current PIN, then set a new one (vault is re-encrypted automatically)
- **Change Security Q&A** — update your recovery question/answer
- **Enable/Disable Fingerprint Authentication** — toggle biometric unlock (requires HTTPS)
- **Lock Vault** — manually re-lock without closing the app
- **Sign in with Google** — back up your encrypted vault to the cloud
- **Sign Out** — disconnect from Google account (local guest data remains)

## Security Notes

- Your PIN is never stored in plaintext — only an encrypted verification string
- Your security answer is stored as a SHA-256 hash
- Vault data is AES-256 encrypted before being saved locally or to Firestore
- Fingerprint unlock works by encrypting your PIN with a key derived from your device's biometric credential — the PIN can only be recovered after successful biometric verification

## License

Personal project — for individual use.
