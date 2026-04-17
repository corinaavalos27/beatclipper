# BeatClipper — Deployment & App Store Guide

## What You Have

Your project is a **Progressive Web App (PWA)** with a Node.js/Express server. This means:
- It runs as a website (deployable in minutes)
- It installs like a native app on iOS and Android via PWA
- It can be wrapped into a **true native app** for the App Store using Capacitor

---

## PART 1 — Deploy the Web App (Live in ~10 minutes)

### Option A: Vercel (Recommended — Free)

1. **Install Vercel CLI**
   ```bash
   npm install -g vercel
   ```

2. **Push your project to GitHub**
   ```bash
   cd beatclipper
   git init
   git add .
   git commit -m "initial commit"
   # Create a repo at github.com, then:
   git remote add origin https://github.com/YOUR_USERNAME/beatclipper.git
   git push -u origin main
   ```

3. **Deploy**
   ```bash
   vercel
   # Follow prompts — choose your GitHub repo
   # Framework: Other
   # Build command: (leave blank)
   # Output directory: public
   ```

4. **Add a vercel.json** in your project root for correct headers:
   ```json
   {
     "headers": [
       {
         "source": "/(.*)",
         "headers": [
           { "key": "Cross-Origin-Opener-Policy", "value": "same-origin" },
           { "key": "Cross-Origin-Embedder-Policy", "value": "require-corp" }
         ]
       }
     ]
   }
   ```

5. Your app is live at `https://beatclipper.vercel.app` (or custom domain)

---

### Option B: Railway (Full Node server — Free tier)

1. Go to **railway.app** → New Project → Deploy from GitHub
2. Connect your GitHub repo
3. Railway auto-detects `package.json` and runs `npm start`
4. Click **Generate Domain** → your app is live

---

### Option C: Render (Free tier)

1. Go to **render.com** → New → Web Service
2. Connect your GitHub repo
3. Build command: `npm install`
4. Start command: `node server.js`
5. Click **Create Web Service**

---

### Option D: Self-host with Docker

```bash
# Build image
docker build -t beatclipper .

# Run locally
docker run -p 3000:3000 beatclipper

# Deploy to any VPS (DigitalOcean, Linode, AWS EC2)
# Then add Nginx reverse proxy + SSL via Let's Encrypt
```

---

## PART 2 — Add a Custom Domain

1. Buy a domain at Namecheap, GoDaddy, or Cloudflare Domains
2. In Vercel/Railway/Render: Settings → Custom Domain → Add `beatclipper.com`
3. Update your DNS records as instructed (usually a CNAME record)
4. SSL is automatic — takes 5–10 minutes to propagate

---

## PART 3 — iOS App Store

### Step 1: Set Up Your Environment

**Requirements:**
- Mac computer (required for iOS builds)
- Xcode 15+ (free from Mac App Store)
- Apple Developer Account ($99/year at developer.apple.com)
- Node.js 18+

**Install Capacitor:**
```bash
cd beatclipper
npm install @capacitor/core @capacitor/cli @capacitor/ios
npm install @capacitor/splash-screen @capacitor/status-bar
npx cap init
```

When prompted:
- App name: `BeatClipper`
- App ID: `com.yourname.beatclipper`
- Web dir: `public`

### Step 2: Build for iOS

```bash
# Add iOS platform
npx cap add ios

# Sync your web files to the native project
npx cap sync ios

# Open in Xcode
npx cap open ios
```

### Step 3: Configure Xcode

Inside Xcode:

1. **Signing & Capabilities tab**
   - Team: Select your Apple Developer account
   - Bundle Identifier: `com.yourname.beatclipper`
   - Enable "Automatically manage signing"

2. **Info.plist — add these keys** (for microphone/audio access):
   ```xml
   <key>NSMicrophoneUsageDescription</key>
   <string>BeatClipper uses audio to analyze beats</string>
   <key>UIFileSharingEnabled</key>
   <true/>
   <key>LSSupportsOpeningDocumentsInPlace</key>
   <true/>
   ```

3. **App Icons** — In Assets.xcassets → AppIcon:
   - Create icons at these sizes: 20, 29, 40, 58, 60, 76, 80, 87, 120, 152, 167, 180, 1024px
   - Use a tool like **appicon.co** to generate all sizes from one 1024x1024 image

4. **Launch Screen** — Set background color `#0a0a0f` to match your app

### Step 4: Test on Device

```bash
# Build for simulator
npx cap run ios

# Or in Xcode: select your device → press the Play button
```

### Step 5: Archive & Submit

1. In Xcode: **Product → Archive**
2. Wait for build to complete
3. Click **Distribute App → App Store Connect → Upload**
4. Go to **appstoreconnect.apple.com**

### Step 6: App Store Connect Setup

1. **Create New App:**
   - Platform: iOS
   - Name: BeatClipper
   - Primary Language: English
   - Bundle ID: com.yourname.beatclipper
   - SKU: beatclipper-v1

2. **App Information:**
   - Category: Music
   - Sub-category: Utilities
   - Age Rating: 4+

3. **Screenshots required:**
   - iPhone 6.7" (1290 × 2796): at least 3 screenshots
   - iPhone 6.5" (1242 × 2688): at least 3 screenshots
   - iPad Pro 12.9" (2048 × 2732): if supporting iPad
   - Use **Simulator** + Screenshot tool, or **ScreenshotCreator**

4. **App Description (example):**
   ```
   BeatClipper uses AI to analyze your beats and find the most 
   energetic, interesting sections automatically. Upload any audio 
   file, let the AI score every possible clip window, then export 
   the perfect 15, 30, or 60-second clip in seconds.

   FEATURES
   · AI-powered energy analysis
   · Visual waveform editor
   · BPM detection
   · One-tap WAV export
   · Loop and preview any section
   ```

5. **Keywords:** beat, music, clip, sample, BPM, audio, producer, hip hop, trap, loop

6. **Pricing:** Free (or set a price)

7. **Submit for Review** — Apple reviews take 1–3 business days

---

## PART 4 — Google Play Store (Android)

### Step 1: Set Up Android

**Requirements:**
- Android Studio (free, works on Mac/Windows/Linux)
- Google Play Developer Account ($25 one-time fee at play.google.com/console)

```bash
# Add Android platform
npx cap add android

# Sync files
npx cap sync android

# Open in Android Studio
npx cap open android
```

### Step 2: Configure Android Studio

1. **app/build.gradle** — set your app ID:
   ```gradle
   applicationId "com.yourname.beatclipper"
   versionCode 1
   versionName "1.0"
   minSdk 26
   targetSdk 34
   ```

2. **App icons** — replace files in `res/mipmap-*` folders
   - Use **Android Asset Studio** (romannurik.github.io/AndroidAssetStudio)

3. **AndroidManifest.xml — add permissions:**
   ```xml
   <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
   <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
   <uses-permission android:name="android.permission.RECORD_AUDIO"/>
   ```

### Step 3: Build Release APK

```bash
# In Android Studio:
# Build → Generate Signed Bundle / APK
# Choose: Android App Bundle (.aab) — required for Play Store
# Create a new keystore (SAVE THIS FILE — you need it forever)
# Enter keystore password, key alias, key password
# Build type: Release
```

### Step 4: Submit to Google Play

1. Go to **play.google.com/console**
2. Create New App → Add App Details
3. **Production → Create new release**
4. Upload your `.aab` file
5. Add release notes
6. **Store listing:**
   - Short description (80 chars): "AI finds your best beat sections instantly"
   - Full description (4000 chars)
   - Feature graphic: 1024 × 500px
   - Screenshots: at least 2, phone + tablet
   - Category: Music & Audio
7. **Content rating:** Complete questionnaire → Music app → No objectionable content
8. **Pricing:** Free or paid
9. **Review and publish** — Google reviews take a few hours to a few days

---

## PART 5 — PWA Install (Bonus — No App Store Needed)

Your app already works as an installable PWA. Users can install it directly from the browser:

- **iOS Safari:** Share button → "Add to Home Screen"
- **Android Chrome:** Menu → "Add to Home Screen" or browser install prompt
- **Desktop Chrome/Edge:** Address bar install icon

The `manifest.json` and `sw.js` are already configured for this.

---

## PART 6 — After Launch Checklist

- [ ] Set up **Google Analytics** or **Plausible** for usage tracking
- [ ] Add **Sentry.io** for error monitoring (free tier)
- [ ] Set up a **privacy policy page** (required for app stores) — use privacypolicygenerator.info
- [ ] Create a **support email** like support@beatclipper.com
- [ ] Add a **landing page** at your domain root
- [ ] Respond to **App Store reviews** within 24–48 hours
- [ ] Plan your **v1.1 features**: stems separation, cloud save, social sharing

---

## Project File Structure

```
beatclipper/
├── public/
│   ├── index.html        ← The full app
│   ├── manifest.json     ← PWA manifest
│   ├── sw.js             ← Service worker (offline support)
│   └── icons/            ← App icons (you need to add these)
│       ├── icon-192.png
│       └── icon-512.png
├── server.js             ← Express server
├── package.json
├── Dockerfile            ← Docker deployment
├── capacitor.config.ts   ← Native app config
└── .gitignore
```

---

## Quick Start (Local Test)

```bash
cd beatclipper
npm install
npm start
# Open http://localhost:3000
```

---

## Cost Summary

| Platform       | Cost               |
|----------------|--------------------|
| Vercel hosting | Free (hobby)       |
| Custom domain  | ~$12/year          |
| Apple Dev Acct | $99/year           |
| Google Play    | $25 one-time       |
| **Total Y1**   | **~$136**          |

---

*Built with Web Audio API + Capacitor. No third-party audio libraries required.*
