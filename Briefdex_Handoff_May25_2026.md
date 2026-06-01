# BRIEFDEX — COMPLETE TECHNICAL HANDOFF
## Version: May 25 2026 · Paste this into a new Claude chat to continue exactly where we left off

---

## WHAT BRIEFDEX IS

Briefdex Finance is an AI-powered daily audio finance briefing app for New Zealand. Every weekday at 6am NZT, an automated backend pipeline scrapes financial data, generates scripts via Claude AI, converts them to audio via OpenAI TTS (switching to ElevenLabs), and delivers 5 audio channels to users via a React Native iOS app.

**Listener:** NZ finance professionals and serious retail investors who want to be across markets before their first meeting of the day.

---

## TWO REPOS ON GITHUB (patricklong123)

- `briefdex-app` — React Native (Expo) iOS app → `~/Desktop/briefdex-app`
- `briefdex-backend` — Node.js pipeline → `~/Desktop/briefdex-backend`

---

## APP STRUCTURE (briefdex-app)

```
src/
├── components/
│   ├── ChannelTile.tsx         — home screen channel tiles with progress bars
│   ├── HeroCard.tsx            — Daily Wrap hero card
│   ├── ProgressBar.tsx         — reusable progress bar
│   ├── ScrubBar.tsx            — player scrub bar
│   └── Icons.tsx
├── screens/
│   ├── HomeScreen.tsx          — main screen
│   ├── PlayerScreen.tsx        — full screen audio player
│   ├── ProfileScreen.tsx       — stats + settings menu (has DEV reset button — REMOVE BEFORE SUBMISSION)
│   ├── NotificationsScreen.tsx
│   ├── SubscriptionScreen.tsx
│   ├── AnnualPlanScreen.tsx
│   ├── SettingsScreen.tsx
│   └── onboarding/
│       ├── WelcomeScreen.tsx
│       ├── ValueScreen1.tsx    — "The Problem" / drowning in finance news
│       ├── ValueScreen2.tsx    — Time cost (38 mins/day, 165 hrs/year, 4 working weeks)
│       ├── ValueScreen3.tsx    — Built on primary sources (NZX, RBNZ, Stats NZ etc)
│       ├── ValueScreen4.tsx    — Trans-Tasman coverage
│       ├── SocialProofScreen.tsx — Beta tested by KPMG, PwC, Macquarie, Jarden, Milford
│       ├── Survey1-6Screen.tsx — 6 survey questions
│       ├── LoadingScreen.tsx   — "Analysing your responses..." with steps
│       ├── BrandScreen.tsx     — "Built for New Zealanders"
│       ├── AuthScreen.tsx      — Sign up (full name + email + password, Supabase auth)
│       ├── PaywallScreen.tsx   — Hard paywall, 7-day free trial, $14.99/month
│       ├── FirstUseScreen.tsx  — Post-paywall first listen screen
│       └── OnboardingNavigator.tsx
├── services/
│   ├── audioService.ts         — expo-av audio playback
│   ├── episodeService.ts       — fetches episodes from API
│   ├── progressService.ts      — AsyncStorage progress tracking
│   ├── statsService.ts         — day streak, mornings ahead, pages digested
│   ├── notificationsService.ts
│   ├── subscriptionService.ts  — RevenueCat checkEntitlement('premium')
│   └── supabaseClient.ts       — Supabase client (real keys already added)
├── hooks/
│   └── useChannelProgress.ts
├── navigation/
│   └── AppNavigator.tsx        — routes based on Supabase auth state
├── theme/
│   └── tokens.ts               — ALL colors, fonts, spacing
└── types/
    └── index.ts
```

---

## BACKEND STRUCTURE (briefdex-backend)

```
src/
├── scrapers/
│   ├── index.js                — runs all 17 scrapers in parallel
│   ├── yahoofinance.js         — RapidAPI authenticated (52 symbols)
│   ├── nzx.js                  — FIXED: now fetches full announcement body text not just headlines
│   ├── beehive.js              — beehive.govt.nz RSS
│   ├── rba.js                  — rba.gov.au RSS
│   └── [14 more scrapers]
├── services/
│   ├── scriptGenerator.js      — Daily Wrap V2.1 prompt + Claude API
│   ├── nzMarketsGenerator.js   — NZ Markets V1.1 prompt
│   ├── globalMarketsGenerator.js — Global Markets V1.1 prompt
│   ├── transTasmanGenerator.js — Trans-Tasman V2.1 prompt
│   ├── macroRBNZGenerator.js   — Macro & RBNZ V2.1 prompt
│   ├── audioGenerator.js       — OpenAI TTS HD (onyx voice) — TO BE REPLACED WITH ELEVENLABS
│   ├── storage.js              — Cloudflare R2 upload
│   └── database.js             — Supabase save/read
├── pipeline.js                 — orchestrates all 5 channels
├── api.js                      — Express API server
└── index.js                    — entry point
```

---

## 5 AUDIO CHANNELS

| Channel | Length | Word Count | Schedule |
|---------|--------|------------|----------|
| Daily Wrap | 7 min | 900-1050 words | Every weekday |
| NZ Markets | 5 min | 650-780 words | Every weekday |
| Global Markets | 4 min | 560-680 words | Every weekday |
| Trans-Tasman | 3 min | 380-480 words | Every weekday |
| Macro & RBNZ | 1-4 min | 150-640 words | Every weekday |

**Auto-play order:** Daily Wrap → NZ Markets → Global Markets → Trans-Tasman → Macro & RBNZ

---

## ALL CREDENTIALS & KEYS

### Supabase
- URL: `https://ghrioxzlrltjhumzlphu.supabase.co`
- Service key: in `~/Desktop/briefdex-backend/.env`
- Anon key: in `~/Desktop/briefdex-app/src/services/supabaseClient.ts`
- Dashboard: supabase.com → project ghrioxzlrltjhumzlphu
- **IMPORTANT:** Email confirmation is currently OFF for testing — turn back ON before App Store submission (Authentication → Providers → Email → Confirm email toggle)

### RevenueCat
- Dashboard: app.revenuecat.com
- Project: Briefdex
- Entitlement name: `premium` (lowercase, exact)
- API key: in `~/Desktop/briefdex-app/App.tsx` (already wired)
- **NOTE:** Real App Store product IDs not yet connected — needs App Store Connect setup

### Cloudflare R2
- Bucket: `briefdex-audio`
- Public URL: `https://pub-670294c205cd49d1a5cc75f314f2b371.r2.dev`
- Credentials: in `~/Desktop/briefdex-backend/.env`

### OpenAI
- Used for TTS audio generation (onyx voice, HD)
- Key: in `~/Desktop/briefdex-backend/.env`
- **TO BE REPLACED:** Switching to ElevenLabs for better NZ/AU accent — this is the next task

### Anthropic (Claude)
- Used for script generation (claude-opus-4-7 model)
- Key: in `~/Desktop/briefdex-backend/.env`

### RapidAPI (Yahoo Finance)
- Key: in `~/Desktop/briefdex-backend/.env`
- Used for 52 market symbols (NZX50, ASX200, S&P500, NZD/USD etc)

### Render
- Dashboard: dashboard.render.com
- `briefdex-pipeline` — cron job, runs 6am NZT daily (`0 18 * * *` UTC)
- `briefdex-api` — web service, live at `briefdex-api.onrender.com`

---

## KEY API ENDPOINT

```
https://briefdex-api.onrender.com/api/episodes/latest?channel=CHANNEL_NAME
```

Channel names: `daily-wrap`, `nz-markets`, `global-markets`, `trans-tasman`, `macro-rbnz`

---

## INFRASTRUCTURE

| Service | Purpose | URL |
|---------|---------|-----|
| Render | Pipeline + API hosting | dashboard.render.com |
| Supabase | User accounts + episode metadata | supabase.com |
| Cloudflare R2 | Audio file storage | cloudflare.com |
| GitHub | Code repos | github.com/patricklong123 |
| RevenueCat | Subscription management | app.revenuecat.com |
| App Store Connect | iOS app submission | appstoreconnect.apple.com (not yet set up) |
| GoDaddy | Domain registrar for briefdex.com | godaddy.com |
| Google Workspace | Email (patrick@briefdex.com, hello@briefdex.com, privacy@briefdex.com) | workspace.google.com |
| GitHub Pages | Website hosting | briefdex.com |

---

## WEBSITE (briefdex.com)

- Repo: `github.com/patricklong123/briefdex.com`
- Local folder: `~/Desktop/Briefdex`
- Privacy policy: `briefdex.com/privacy-policy.html` ✅ LIVE
- Terms of service: `briefdex.com/terms-of-service.html` ✅ LIVE
- To push changes: `cd ~/Desktop/Briefdex && git add . && git commit -m "message" && git push origin main`

---

## EMAIL

- Primary: patrick@briefdex.com
- General: hello@briefdex.com (alias — lands in patrick@ inbox)
- Privacy: privacy@briefdex.com (alias — lands in patrick@ inbox)
- Provider: Google Workspace Starter on briefdex.com domain
- hello@ and privacy@ are aliases — may still be propagating (set up today, takes up to 24hrs)

---

## DESIGN TOKENS (tokens.ts)

```
Background: #0a1a10 (g900)
Surface: #0f2318
Gold accent: #c9a84c
White: #ffffff
Dim text: rgba(255,255,255,0.5)
Very dim: rgba(255,255,255,0.35)
Border: rgba(255,255,255,0.08)
Fonts: Playfair Display (headings), DM Sans (body)
```

---

## TERMINAL COMMANDS

```bash
# Start app (iOS simulator)
cd ~/Desktop/briefdex-app && npx expo start --ios

# Start app (Expo Go QR code)
cd ~/Desktop/briefdex-app && npx expo start

# Run full pipeline manually
cd ~/Desktop/briefdex-backend && npm run run-pipeline

# Push backend changes
cd ~/Desktop/briefdex-backend
git add . && git commit -m "message" && git push

# Push app changes
cd ~/Desktop/briefdex-app
git add . && git commit -m "message" && git push

# Push website changes
cd ~/Desktop/Briefdex
git add . && git commit -m "message" && git push origin main

# Open Claude Code for app
cd ~/Desktop/briefdex-app && claude

# Open Claude Code for backend
cd ~/Desktop/briefdex-backend && claude
```

---

## HOW CLAUDE CODE WORKS

- Open terminal, cd to the relevant folder, run `claude`
- Press 2 to allow all edits without asking
- Paste prompts for changes
- It commits and pushes automatically
- When it asks about PRs, you can type "merge it" to merge
- GitHub CLI (gh) is installed and authenticated

---

## WHAT WAS COMPLETED IN THIS SESSION (May 22-25 2026)

✅ Skip interval setting fixed — now reads from AsyncStorage, no longer hardcoded to 15s
✅ NZX scraper fixed — now fetches full announcement body text, not just headlines
✅ All 5 channel prompts updated — percentage change only, no index points unless record/milestone
✅ Google Workspace set up — patrick@briefdex.com working
✅ Privacy policy written and live at briefdex.com/privacy-policy.html
✅ Terms of service written and live at briefdex.com/terms-of-service.html
✅ Privacy policy and T&C linked in app (Settings screen + onboarding)
✅ RevenueCat SDK integrated — react-native-purchases installed, configured in App.tsx
✅ Supabase auth integrated — real signup/login/forgot password working
✅ Login screen built — standalone screen with email/password, forgot password, sign up link
✅ Full name field added to signup — stored in Supabase metadata + AsyncStorage
✅ Name personalisation throughout app — paywall says "Your briefing is ready, [Name]"
✅ Profile screen shows initials in avatar circle
✅ Apple/Google sign up buttons removed from auth screen
✅ All onboarding screens restyled to match premium HTML design reference
✅ Social proof screen updated — KPMG, PwC, Macquarie Bank, Jarden, Milford Asset Management
✅ Source chips updated — Bloomberg removed, replaced with real free sources only
✅ Time cost screen — updated to "4 full working weeks"
✅ Loading screen — emoji removed, steps updated, no "smart alarm" mention
✅ Delivery time updated throughout to 6:30am
✅ DEV ONLY reset onboarding button on Profile screen (MUST REMOVE BEFORE SUBMISSION)
✅ DEV ONLY skip paywall link (MUST REMOVE BEFORE SUBMISSION)
✅ GoDaddy DNS fixed — Parked A record deleted, HTTPS enforced on GitHub Pages
✅ briefdex.com now loads correctly with SSL

---

## WHAT STILL NEEDS TO BE DONE (in priority order)

### 1. ElevenLabs — NEXT TASK
- Switch audioGenerator.js from OpenAI TTS to ElevenLabs
- Reason: OpenAI onyx voice mispronounces NZ decimals and sounds American
- Need a NZ or AU accented voice from ElevenLabs
- ElevenLabs API key needs to be added to .env
- Files to change: `~/Desktop/briefdex-backend/src/services/audioGenerator.js`
- Keep OpenAI as fallback in case ElevenLabs fails

### 2. Remove DEV-only items before submission
- Remove "DEV ONLY · Reset Onboarding" button from ProfileScreen.tsx
- Remove "Skip for testing" link from PaywallScreen.tsx
- Turn Supabase email confirmation back ON

### 3. App Store Connect setup
- Create app at appstoreconnect.apple.com
- Set up subscription product: monthly, $14.99 NZD, 7-day free trial
- Connect product ID to RevenueCat
- This is required before RevenueCat paywall actually works

### 4. TestFlight build
- Build with EAS: `eas build --platform ios`
- Upload to App Store Connect
- Install on iPhone via TestFlight
- Test full flow on real device (not simulator)

### 5. App Store submission assets
- Screenshots (6.5" iPhone required, 5.5" recommended)
- App description (need to write)
- App Store keywords
- App icon (check it meets Apple specs — no alpha channel, 1024x1024)

### 6. Investigate Sunday pipeline run
- Pipeline ran unexpectedly on Sunday May 25 despite cron set to weekdays only
- Check Render logs: dashboard.render.com → briefdex-pipeline → Logs
- Cron should be `0 18 * * 1-5` (Mon-Fri only) — verify this is set correctly

---

## BUSINESS CONTEXT

### Pricing
- Monthly: $14.99 NZD/month (after 7-day free trial)
- Annual: $119.99 NZD/year (save 33%) — screen built, not yet wired
- Corporate deals: $12 NZD/month per seat, invoiced directly (bypass Apple's cut)
- Target corporate clients: NZ banks, advisory firms, fund managers

### Corporate sales strategy
- Patrick is planning cold calling / in-person sales to large banks and organisations
- Corporate accounts created manually in Supabase, invoiced by bank transfer
- No App Store involvement for corporate deals — keeps 100% of revenue
- Target firms: ANZ, Westpac, BNZ, ASB, Jarden, Milford, Fisher Funds etc

### Future roadmap
- V1.1: AU expansion (decision made to launch NZ first)
- V1.1: Stock Spotlight channel — one NZX/ASX stock per episode, 3 mins, factual only
- V1.1: ElevenLabs already being done now
- V2: Stripe web subscription option to bypass Apple's 15% cut

### Legal
- FMA compliance: resolved — no FSLAA registration required
- IP/copyright question: being worked through Edwin Lim at Dentons (one floor above Patrick's workplace)
- Financial advisor: Max (investment banker at Macquarie) — friend and connector
- Legal firm: Dentons NZ

### Key contacts
- Edwin Lim — Dentons (IP/copyright question on AI-generated scripts)
- Max — Macquarie, investment banker, financial advisor for Briefdex
- Chris — Director at Experieco (NZ software dev company), Patrick's friend's father

---

## KNOWN ISSUES

1. **Sunday pipeline run** — ran unexpectedly on May 25 (Sunday). Render cron may need fixing to `0 18 * * 1-5`
2. **Forgot password deep link** — works in code but `briefdex://reset-password` deep link doesn't work in Expo Go (known limitation). Will work in native TestFlight build.
3. **hello@ and privacy@ aliases** — set up today, may take up to 24hrs to fully propagate in Google Workspace
4. **RevenueCat not connected to App Store products** — paywall shows but purchases won't process until App Store Connect product IDs are added to RevenueCat

---

## TESTING

- App currently runs via Expo Go (scan QR) or iOS Simulator (`npx expo start --ios`)
- NOT yet on App Store or TestFlight
- Test account created in Supabase during this session (lonpa440@student.otago.ac.nz)
- To get back to onboarding: use DEV reset button on Profile screen
- Supabase email confirmation is currently OFF — turn back ON before submission

---

*Briefdex Onboarding Flow · 16 screens · React Native / Expo · May 2026*
*patrick@briefdex.com · github.com/patricklong123*
