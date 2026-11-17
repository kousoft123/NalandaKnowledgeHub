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
