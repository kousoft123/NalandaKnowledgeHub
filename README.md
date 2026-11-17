# NalandaKnowledgeBook: Android release guide (PWA + Trusted Web Activity)

## 1. Host the site (HTTPS, a domain you control)
Upload this folder to Netlify, Cloudflare Pages, GitHub Pages or Firebase Hosting.
`/.well-known/assetlinks.json` must be reachable at https://YOURDOMAIN/.well-known/assetlinks.json
(GitHub Pages: add an empty `.nojekyll` file so the dot-folder is served.)
Replace REPLACE_DATE and REPLACE_EMAIL in privacy.html. Your privacy URL = https://YOURDOMAIN/privacy.html

## 2. Test the PWA first
Open the site in Chrome on Android, add a key, generate a plan. Run Lighthouse (Chrome DevTools) and confirm "Installable".
If a provider fails with a CORS error, test another provider (Gemini, Anthropic, Groq and OpenRouter allow browser calls).

## 3. Build the Android bundle with Bubblewrap
Needs Node 18+ (Bubblewrap installs JDK and Android SDK for you on first run).
  npm i -g @bubblewrap/cli
  edit twa-manifest.json: replace REPLACE_YOUR_DOMAIN.com (4 places)
  bubblewrap init --manifest=https://YOURDOMAIN/manifest.webmanifest
  bubblewrap build
This creates app-release-bundle.aab (upload this) and an APK for testing:
  adb install app-release-signed.apk

## 4. Digital Asset Links (removes the browser address bar)
Use Play App Signing. In Play Console: Setup > App signing > copy the SHA-256 of the *app signing key*
and paste it into .well-known/assetlinks.json, then redeploy. (Add the upload-key SHA-256 too, to test sideloaded builds.)
Verify: https://developers.google.com/digital-asset-links/tools/generator

## 5. Play Console
- Create developer account (US$25 one-time; new personal accounts must complete closed testing with 12+ testers for 14 days before production, check the current rule in Console).
- Create app "NalandaKnowledgeBook", upload the .aab to Internal testing first.
- Fill: privacy policy URL, Data safety form (data stays on device; text is sent to a user-chosen AI provider), Content rating, Target audience.
- Target audience: if you include children under 13 you fall under the Families Policy (stricter). Safer: target 13+ or "parent-managed".
- Declare AI-generated content features if asked; add an in-app way to report bad answers if you target broad audiences.
- Store listing: 512x512 icon, 1024x500 feature graphic, 2+ phone screenshots.

## Keep in mind
- API keys are stored in the app's local storage on the device. Fine for a personal key; not for a shared key.
- AI answers can be wrong; keep the disclaimer in the privacy policy and listing.

## Google sign-in (SSO) setup
1. Google Cloud Console > APIs & Services > Credentials > Create credentials > OAuth client ID > Web application.
2. Authorized JavaScript origins: https://YOURDOMAIN (and http://localhost:8080 for testing).
3. Paste the client ID into `const GOOGLE_CLIENT_ID=""` near the top of the script in index.html, redeploy.
4. Configure the OAuth consent screen (app name, privacy policy URL). Until it is published, only test users can sign in.
Notes: this identifies the student on the device only. There is no server, so the sign-in token is not verified by a backend and data does not sync between devices.

## Local folder
Desktop Chrome/Edge: Choose folder saves topics/ and chats/ as .md files under NalandaKnowledgeBook/. Android browsers cannot write to a chosen folder; use Export backup there.
