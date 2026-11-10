# Firebase setup and troubleshooting

## Current app behavior

`index_2.html` uses the Firebase web config and attempts Firestore realtime sync. If Firestore is unavailable or rejects access, the app should show a clear status and keep working with browser `localStorage`. Local data is per browser/device and does not sync to other devices.

The app's current family-code/name screen is **not Firebase Authentication**. Do not use permissive Firestore rules such as `allow read, write: if true`; a family code is not proof of identity.

## Apply the Firestore rules

1. Open Firebase Console → **Firestore Database** → **Rules**.
2. Copy the contents of `firestore.rules` from this repository into the rules editor.
3. Publish the rules.
4. Enable Firebase Authentication (for example, Email/Password) and add a trusted sign-in/membership provisioning flow before expecting the current app to sync. The rules require a signed-in Firebase user and an administrator-created document at `families/{familyId}/members/{uid}` with `active: true`.
5. Never allow the browser app to create its own membership document. Create membership documents through a trusted Admin SDK backend or manually in the Firebase Console for a controlled test.

Until Firebase Auth and membership provisioning are wired into the app, Firestore will correctly deny writes and the app must use local storage. Do not weaken rules to make the sync indicator turn green.

## Verify / troubleshoot

- Serve the page over HTTP/HTTPS, not by opening it as `file://`. GitHub Pages works for static hosting.
- Confirm Firestore Database exists in project `planning-with-ai-38ae5`.
- Open browser DevTools → Console and Network, reload, and inspect Firebase errors.
- `permission-denied` means the user is not authenticated or has no active family membership; check the Auth setup and membership doc.
- `unavailable` / network errors can mean offline, blocked requests, or a Firebase service issue; local data remains in that browser.
- The Firebase web API key is a client identifier, not an authorization mechanism. Firestore Security Rules are the access boundary. Restrict the key by referrer in Google Cloud if appropriate, and never put a Gemini secret or service-account key in this HTML.
