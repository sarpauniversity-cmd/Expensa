# 🔒 Security Guide for Expensa

## Understanding Firebase Web App Security

**Important:** Firebase configuration in web apps is designed to be public. The API key identifies your Firebase project, but **does NOT grant access to your data**.

### How Firebase Security Actually Works

Your data is protected by **Firebase Security Rules**, not by hiding the config. Here's what you need to set up:

---

## 1. Firestore Security Rules

Go to **Firebase Console → Firestore Database → Rules** and add these rules:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Users can only access their own data
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
      
      // User's expenses
      match /expenses/{expenseId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
      
      // User's settings
      match /settings/{settingId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
      
      // User's reports
      match /reports/{reportId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
    }
    
    // Admin access (replace with your admin email)
    match /users/{userId} {
      allow read: if request.auth != null && 
                     request.auth.token.email == 'palerugopi@gmail.com';
    }
  }
}
```

---

## 2. Restrict API Key (Recommended)

1. Go to **Google Cloud Console** → **APIs & Services** → **Credentials**
2. Find your Firebase API key
3. Click **Edit**
4. Under **Application restrictions**, select **HTTP referrers**
5. Add your allowed domains:
   - `localhost`
   - `127.0.0.1`
   - `yourdomain.com/*`
   - `*.yourdomain.com/*`
6. Click **Save**

---

## 3. Enable Firebase App Check (Optional but Recommended)

1. Go to **Firebase Console** → **App Check**
2. Register your app with **reCAPTCHA v3**
3. Enforce App Check for Firestore

---

## 4. Authentication Settings

1. Go to **Firebase Console** → **Authentication** → **Settings**
2. Under **Authorized domains**, only keep domains you'll use
3. Remove any unnecessary domains

---

## 5. Enable Only Required Auth Providers

1. Go to **Firebase Console** → **Authentication** → **Sign-in method**
2. Enable only:
   - Email/Password
   - Google (if needed)
3. Disable all others

---

## What the Config Contains

| Field | Purpose | Risk Level |
|-------|---------|------------|
| `apiKey` | Identifies project | Low (restricted by rules) |
| `authDomain` | Auth redirect URL | Low |
| `projectId` | Project identifier | Low |
| `storageBucket` | Storage location | Low |
| `messagingSenderId` | Push notifications | Low |
| `appId` | Analytics tracking | Low |

---

## Summary

✅ **Security comes from Firestore Rules, NOT from hiding config**
✅ **Restrict API key to your domains**
✅ **Enable App Check for production**
✅ **Keep Auth providers minimal**

Your Firebase config being visible is **normal and expected** for web apps. Focus on proper Security Rules!
