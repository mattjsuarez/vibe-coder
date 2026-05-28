# LitHound — Product Requirements Document

## 1. Overview

LitHound is an iOS app for book hunters, collectors, and avid readers who frequent thrift
stores, library sales, and used book shops. Users take a single photo of a bookshelf and
LitHound instantly identifies every visible title, then cross-references them against the
user's wishlists — both native to the app and imported from external services like Google
Books and Goodreads. When a match is found, the app celebrates the find and shows where
else the book can be purchased online for price comparison. Users can also share finds with
friends, see if a book is on a friend's wishlist, and rate books they've read. The name
"LitHound" combines literature with the tracking instincts of a hound — always on the
scent of the next great find.

---

## 2. Problem Statement

Used book venues — thrift stores, estate sales, library sales — contain thousands of
unsorted, unindexed books. Readers who maintain wishlists have no efficient way to check
whether any books in a given pile match what they're looking for. The current options
(manually scanning barcodes one at a time, or relying on memory) are slow and unreliable.
LitHound solves this by letting a user photograph an entire shelf and receive a match
report in seconds.

---

## 3. Target Users

**Primary: The Book Hunter**
Avid readers who regularly visit thrift stores, Savers, Goodwill, library sales, and used
book shops. They maintain active wishlists on Goodreads, StoryGraph, or Google Books.
They want to move quickly through a store and not miss a find. Technically comfortable
with smartphones; not necessarily developers.

**Secondary: The Collector**
Seeks specific titles, editions, or authors. May have a large, well-organized wishlist.
Values price-comparison features and condition awareness. Willing to pay for a premium tier.

**Tertiary: The Casual Reader**
Less frequent thrift store visitors. May use the app occasionally. More likely to be on
the free tier. Acquired through friend referrals or social sharing of finds.

---

## 4. Goals & Success Metrics

| Goal | Metric | Target (6 months) |
|------|--------|-------------------|
| Core feature adoption | % of registered users who complete ≥1 scan | 70% |
| Wishlist connection | % of users who connect ≥1 external wishlist or add ≥5 native books | 50% |
| Retention | Day-30 retention | 25% |
| Monetization | Paid tier conversion | 5% of active users |
| Organic growth | % of new installs from share links / friend invites | 30% |
| Abuse prevention | API cost as % of revenue | < 15% |

---

## 5. User Flows

### 5.1 Onboarding

1. User downloads LitHound from the App Store
2. Splash screen shown briefly (LitHound logo + tagline)
3. Two guest scans are available without an account — app prompts: "Try it first. No
   account needed." with a "Scan Now" CTA
4. After 2 guest scans (or user taps "Save my results"), sign-up prompt appears
5. User signs in with Apple or Google
6. App explains scan limits and wishlist connection benefits (brief, skippable)
7. Optional: connect Google Books, import Goodreads CSV, or build native wishlist
8. User lands on home screen with scan CTA prominent

### 5.2 Core Scan Flow

1. User taps the scan button (camera icon, prominent on home screen)
2. Camera opens in-app
3. User points at bookshelf and captures photo (or selects from photo library)
4. **On-device pre-validation** (Apple Vision framework, instantaneous):
   - Does image contain text/print? → pass
   - No text detected → "No books detected. Try moving closer or improving lighting."
   - Scan does not count against limit; no backend call made
5. Image hash generated and checked against recent scans — duplicate detected → "You
   already scanned this shelf. View previous results?" with option to force re-scan
6. Image added to scan queue (visible UI with thumbnail + status)
7. Backend receives image, validates session, checks scan count, decrements count,
   proxies to OpenAI GPT-4o Vision
8. GPT-4o returns list of identified titles and authors
9. Backend cross-references against user's wishlists (native + imported)
10. Results returned to app:
    - **Matches found** → celebration state (see Section 9.2)
    - **No matches, books identified** → list of identified books + recommendation
      prompt (post-MVP)
    - **Zero titles identified (bad image)** → scan count NOT decremented; tips shown
    - **Non-book image detected** → scan count NOT decremented; logged server-side
      for abuse tracking
11. Result screen shows: matched books highlighted, all identified books listed,
    Amazon price reference for each, option to add any identified book to wishlist
12. Each identified book card has a "Wrong book?" button — tapping it flags the
    result, removes the card from results, and logs the report server-side for
    future prompt improvement. No scan credit is refunded for misidentifications.

### 5.3 Simultaneous Scan Queue

1. User may submit multiple photos while previous scans are processing
2. Queue UI shows each submitted image as a card: thumbnail → "Processing…" spinner
   → result summary
3. Scans are deduplicated by image hash — same image in queue is rejected silently
4. Each scan result card is tappable to view full results
5. Scan button remains active while scans are in flight (multiple simultaneous scans
   allowed)
6. If a scan fails due to network error or API outage, the card shows "Failed — Tap
   to retry" and scan count is NOT decremented

### 5.4 Scan Limit Reached — Unlinked Free User

1. User attempts scan with 0 scans remaining and no external wishlist connected
2. Modal appears: "You've used your free scans"
3. Flow explains: "Connect your Goodreads or Google Books wishlist to unlock [benefit]"
   and "Upgrade to Standard for 250 scans/month"
4. Options: Connect wishlist (stays free), Upgrade to Standard ($5.99), or dismiss

### 5.5 Scan Limit Reached — Linked Free User

1. Same trigger as 5.4 but user has external wishlist connected
2. Modal focuses on paid tier benefits: scan count, elite tier, etc.
3. Options: Upgrade to Standard ($5.99), Upgrade to Elite ($9.99), or dismiss

### 5.6 Friends Flow

1. User navigates to Friends tab
2. Find friends via: username search, QR code scan, share link, SMS invite
3. Friend request sent → recipient receives push notification (if enabled)
4. Once friendship is accepted: **both users can see each other's wishlists** for
   scan-matching purposes (mutual visibility by default)
5. Either user can toggle "Hide my wishlist from friends' scans" in Settings — they
   remain connected but their wishlist is excluded from the other user's scan results
6. During scan: if an identified book appears on a friend's wishlist, it is flagged
   in results alongside user's own matches
7. Friends are NOT automatically notified when their book is found — notification
   is always manual and user-initiated
8. User taps friend's match → share sheet opens with pre-filled message:
   "Found [Title] at [store name/location] for $[price] — it's on your list!"
9. Share routes through iOS native share sheet (iMessage, Instagram, TikTok, WhatsApp,
   email, etc.)
10. Amazon link included so friend can buy a new copy if desired
11. No in-app transaction — purchase and physical handoff happen outside the app
12. When a friendship is removed: wishlist access is revoked for both users immediately

### 5.7 Location Sharing

1. Location permission is NOT requested at app launch
2. When user attempts to share a book find with a friend (including location):
   - If permission not yet granted: iOS system prompt appears with context
     "LitHound needs your location to share where you found this book"
   - If previously denied: in-app explanation shown with "Open Settings" button
3. Location is used only for the share action — it is not stored persistently without
   explicit user consent

### 5.8 Subscription & Upgrade Flow

1. Upgrade prompt accessible from: scan limit modal, profile/settings, and a subtle
   persistent prompt in the scan results screen for free users
2. Three tiers presented clearly side by side
3. Subscription managed through App Store (StoreKit) — Apple handles billing and
   cancellation; no custom cancel flow needed
4. Elite overage pack: when Elite user reaches 500 scans, prompt appears offering
   100-scan packs for $1.99 each

---

## 6. Feature Requirements

### 6.1 Must-Have (MVP)

| Feature | Description | Acceptance Criteria |
|---------|-------------|-------------------|
| Camera scan | Capture shelf photo in-app or from library | Photo captured and submitted to scan pipeline |
| On-device pre-validation | Apple Vision framework checks for text before backend call | Non-book images rejected without backend call |
| GPT-4o book identification | Identify titles + authors from shelf photo | Returns structured list of title/author pairs |
| Image deduplication | Hash-based duplicate detection | Same image not processed twice unless network failure |
| Scan queue UI | Shows all in-flight and completed scans | Queue visible, each card tappable |
| Native wishlist | Add/remove books from in-app wishlist | Books saved to user's Supabase profile |
| Google Books integration | Connect Google Books "Want to Read" shelf via API | Live sync on each scan |
| Goodreads import | Upload exported Goodreads CSV to populate wishlist | CSV parsed and books imported |
| StoryGraph import | Upload exported StoryGraph CSV | CSV parsed and books imported |
| Wishlist matching | Cross-reference scan results against all wishlists | Matched books highlighted in results |
| 5-star book rating | Rate books within app | Rating saved to user profile |
| "Books read" list | Track books user has read | Separate from wishlist; shown in results |
| Amazon price reference | Show Amazon listing link + price for each identified book | Link opens in Safari or Amazon app |
| Friends (connect) | Find friends via username, QR code, share link, SMS | Friend connection stored in database |
| Friends (mutual wishlist) | Accepted friends see each other's wishlists for scan matching | Mutual visibility on accept; revoked on unfriend |
| Wishlist privacy toggle | "Hide my wishlist from friends' scans" in Settings | Hides wishlist from all friends' scan results without removing friendships |
| Friend share | Share a find with a friend via native share sheet | Pre-filled message with title, location, price, Amazon link |
| Misidentification report | "Wrong book?" button on each scan result card | Flags result, removes card, logs report server-side |
| Celebration state | Visual + audio celebration on wishlist match | Animation and sound play on match; toggleable in settings |
| Scan limits | Free: 5 start + 1/week (cap 5); Standard: 250/mo; Elite: 500/mo | Limits enforced server-side |
| Scan overage packs | Elite users can buy 100-scan packs for $1.99 | Purchasable via StoreKit |
| Apple + Google Sign In | Auth via Supabase | Users can sign in with either provider |
| Guest scans | 2 scans before account required | Scans work without auth; results lost on app close |
| Scan limit upsell flow | Modal shown when free tier exhausted | Distinct flows for linked vs. unlinked users |
| Plausible analytics | Page views and basic event tracking | Plausible script integrated; no cookie consent required |
| Abuse tracking | Log non-book image submissions per user | Flagging visible in admin/database |
| Rate limiting | 1 scan per 15 seconds per user | Enforced server-side |
| Apple App Attest | Verify requests come from legitimate app installs | Integrated into backend validation |

### 6.2 Nice-to-Have (Post-MVP)

| Feature | Description | Priority |
|---------|-------------|----------|
| Book recommendations | Suggest books from scan based on reading history | High |
| Android app | Port to Android via React Native | High |
| Web app | Browser-based companion | Medium |
| Barcode fallback | Single-book barcode scan for individual lookups | Medium |
| Store check-in | Tag a scan to a named location | Medium |
| Instagram/TikTok deep share | Direct share to specific platforms beyond share sheet | Low |
| Social feed | See friends' recent finds | Low |
| Book condition notes | Add notes about condition alongside a find | Low |
| Price history | Track price trends for wishlist books | Low |

---

## 7. Technical Architecture

- **Platform:** iOS (iPhone), React Native via Expo (leverages user's React experience;
  enables future Android and web ports from shared codebase)
- **Frontend:** React Native, Expo SDK, NativeWind (Tailwind-style styling for RN)
- **Backend:** Supabase
  - Auth: Apple Sign In + Google Sign In via Supabase Auth
  - Database: Postgres (Supabase)
  - Edge Functions: API proxy layer for OpenAI calls (keeps API key server-side);
    scan counting and validation logic
  - Storage: Supabase Storage for scan image caching (optional; for retry flows)
- **Image pre-validation:** Apple Vision framework (on-device, no API cost)
- **Book identification:** OpenAI GPT-4o Vision (high-detail mode), ~$0.005–0.01/image
- **Book data enrichment:** Google Books API (cover images, metadata, wishlist sync)
- **Price reference:** Amazon Product Advertising API or affiliate deep links
- **Analytics:** Plausible (single script tag, no cookie consent required)
- **Payments:** StoreKit (iOS native in-app purchases + subscriptions)
- **Rate limiting:** Enforced in Supabase Edge Functions (per-user, per-session)
- **Abuse prevention:** Apple App Attest, image hash deduplication, non-book image
  logging, OpenAI dashboard spending caps

### Data Model (Key Entities)

```
users
  id, email, device_id, subscription_tier, scans_remaining,
  scans_reset_at, created_at

books
  id, title, author, isbn, cover_image_url, google_books_id, open_library_id

user_wishlist
  id, user_id, book_id, source (native | google_books | goodreads_csv |
  storygraph_csv), added_at

user_read_books
  id, user_id, book_id, rating (1–5), read_at

scans
  id, user_id, image_hash, submitted_at, status (processing | completed |
  failed), books_found_count, is_book_image (bool), counted_against_limit (bool)

scan_results
  id, scan_id, book_id, confidence_score, on_user_wishlist (bool),
  on_friend_wishlist (bool)

friendships
  id, requester_id, addressee_id, status (pending | accepted), created_at

user_settings
  id, user_id, wishlist_hidden_from_friends (bool, default false),
  celebration_sound_enabled (bool, default true),
  celebration_animation_enabled (bool, default true)

wishlist_integrations
  id, user_id, service, last_synced_at

misidentification_reports
  id, scan_result_id, user_id, reported_at
```

---

## 8. iOS-Specific Notes

- **Minimum iOS version:** iOS 16 (covers ~95%+ of active iPhones as of 2025)
- **Camera permission:** Requested at first scan attempt with clear context copy
- **Location permission:** Requested only at moment of friend share, not at launch
- **Push notification permission:** Requested after user's first successful wishlist match
- **Apple App Attest:** Integrated at backend to verify request origin
- **StoreKit 2:** Used for subscription and consumable (overage pack) purchases
- **Haptics:** Light haptic feedback on scan submission; strong haptic on wishlist match

---

## 9. Design Direction

- **Visual style:** Modern without being cold. Clean white/light surfaces, generous
  whitespace, sharp typography. Warm accent color (to be decided — amber or deep teal
  are strong candidates given the "lit" / book world associations).
- **Reference apps:** Literal.club (minimal, premium feel), StoryGraph (data-forward,
  modern), Instacart (clear primary actions, confident layout)
- **Mascot potential:** The "hound" name opens the door to a simple illustrated dog
  character for empty states, onboarding, and the celebration animation — without
  requiring it at MVP
- **Typography:** Serif display font for headers (book world association) paired with
  a clean sans-serif for UI text
- **Key UX principle:** Every screen has one obvious primary action. The scan button
  is always reachable in one tap from anywhere in the app.

### 9.1 Usability Commitments

Issues identified in Phase 2 review and agreed resolutions:

| Anti-Pattern | Resolution |
|---|---|
| Account wall before value | 2 guest scans allowed before sign-up is required |
| Google Analytics consent banner | Replaced with Plausible — no consent banner needed |
| Push notification on first launch | Requested after first successful wishlist match only |
| Double-tap scan submission | Scan button disabled while scan in flight; "in progress" state explicit |
| Failed scan decrementing limit | Scan count only decremented on successful identification of ≥1 book |
| API key in frontend code | All API keys (OpenAI, Google Books, etc.) live in Supabase Edge Functions only |
| Location permission on launch | Requested only when user initiates a friend share with location |
| IP-based account limits | Dropped in favor of device-based limits via Apple App Attest |
| Non-book image abuse | On-device Vision pre-filter + server-side abuse logging |
| OpenAI API outage | Graceful "scanning temporarily unavailable" message; scan not counted |

### 9.2 Win / Success State

When a scan returns at least one wishlist match:

1. Results screen loads with matched book(s) displayed first, visually distinct
   (highlighted card, colored border or badge — "On Your List!")
2. **If celebration is enabled (default on, toggleable in Settings):**
   - A brief animation plays — proposed: illustrated hound "sniffing out" the book,
     or a simpler confetti/sparkle burst over the matched card
   - A short satisfying sound plays (muted if device is on silent or user has disabled
     sounds in Settings; mute toggle also accessible directly on the results screen)
   - Haptic feedback (strong impact) fires simultaneously
3. Match card shows: book cover, title, author, "On Your List" badge, Amazon price
   link, and a share button
4. If the match is also on a friend's wishlist: secondary badge appears — "Also on
   [Friend]'s list" — with a one-tap share option
5. User can add any other identified books to their wishlist directly from results

The mute toggle is displayed as a persistent icon in the top corner of the results
screen — not buried in Settings — so users in libraries or quiet spaces can silence
it in one tap without interrupting their flow.

---

## 10. Scope & Constraints

- **Timeline:** No hard deadline. Builder is available full-time. Goal is a polished
  MVP suitable for App Store submission and use as a portfolio/business project.
- **Budget:** Willing to use paid services where they provide meaningful value. Key
  recurring costs: Supabase (free tier initially), OpenAI API (~$0.005–0.01/scan),
  Plausible ($9/month), Apple Developer Program ($99/year).
- **Launch audience:** Public App Store release. Initial users likely from personal
  network and organic social sharing.
- **Monetization:** Subscription via StoreKit. Free tier is intentionally generous
  enough to drive organic growth and word-of-mouth, with clear upgrade path.
- **Known trade-offs:**
  - Goodreads and StoryGraph do not offer live API access — wishlist import is
    CSV-only (not live sync). Users must re-import when their external list changes.
  - GPT-4o Vision may misidentify books with worn, obscured, or sideways spines.
    Results screen should allow users to report a misidentification.
  - React Native introduces a thin abstraction over native iOS APIs. For the camera
    and Vision framework integration, native modules or Expo plugins will be required.

---

## 11. Security & Compliance Requirements

### 11.1 Data & Privacy

- **Data collected:** Email address, device ID, wishlist contents, scan history,
  book ratings, friend connections, location (only when shared, not stored persistently)
- **Legal basis:** Consent (users sign up explicitly)
- **Privacy policy:** Required before launch. Hand-crafted policy covering: what is
  collected, why, retention periods, third-party services (Supabase, OpenAI image
  processing, Google Books, Plausible, Apple/Google auth), user deletion rights.
- **Data retention:** Scan images are not stored beyond the processing window. Scan
  result metadata retained for the life of the account.
- **User deletion:** "Delete my account" in Settings must remove all user data from
  Supabase (cascade delete on all user-scoped tables). Required for GDPR compliance.
- **Applicable regulations:** GDPR (EU users possible), CCPA (California users),
  App Store privacy nutrition label required.
- **Contact page:** Required for privacy inquiries. Linked from app settings and
  any web presence. Use Formspree (free tier) if a web contact page is needed.

### 11.2 Secrets & API Keys

| Key | Lives In | Notes |
|-----|----------|-------|
| OpenAI API key | Supabase Edge Function env var | Never in app binary or client code |
| Google Books API key | Supabase Edge Function env var | Never in client |
| Amazon affiliate tag | Supabase Edge Function | Used for link generation only |
| Supabase anon key | App (this is public by design) | Row-level security enforces access |
| Supabase service role key | Edge Functions only | Never in client |

- `.env` files added to `.gitignore` before first commit
- Run `git log --all --full-history -- .env` before any public repo push

### 11.3 Security Headers & Transport

- HTTPS enforced by default on all Supabase and Expo/EAS deployments
- If a web companion is built, run through [securityheaders.com](https://securityheaders.com)
  before launch and configure `vercel.json` headers (X-Frame-Options, X-Content-Type-Options,
  Referrer-Policy, Permissions-Policy, CSP)

### 11.4 Auth & Access Control

- Auth provider: Supabase Auth (Apple Sign In + Google Sign In)
- All database queries scoped to authenticated user via Supabase Row Level Security (RLS)
- RLS policies must be defined on every table before any data is written
- Friends' wishlist data: read-only access granted only to accepted friends
- No user can read, modify, or delete another user's data via direct ID manipulation (IDOR prevention enforced by RLS)

### 11.5 Rate Limiting & Abuse Prevention

| Operation | Limit | Enforcement |
|-----------|-------|-------------|
| Scan submission | 1 per 15 seconds per user | Supabase Edge Function |
| Scan monthly limit | Per tier (5 / 250 / 500) | Server-side counter in `users` table |
| Account creation | 3 per device per day | Apple App Attest + device fingerprint |
| Friend requests | 20 per day per user | Server-side counter |
| OpenAI spending | Hard daily + monthly cap | OpenAI dashboard setting |
| Non-book image abuse | Flag after 10 consecutive non-book submissions | Logged in `scans` table; manual review |

---

## 12. Production Readiness Commitments

| Area | Decision | Notes |
|------|----------|-------|
| Email provider | None at MVP | No transactional email required at launch |
| Analytics | Plausible | One script tag; no consent banner; $9/month |
| Error tracking | Sentry (Expo SDK available) | Integrate from day 1 |
| Cookie consent | Not required | Plausible is cookieless |
| Environments | Dev + Production | Staging added before any migration touches real user data |
| Cascade/delete behavior | Defined in schema (see data model) | All user tables cascade delete on user removal |
| Third-party failure handling | Graceful degradation defined per integration | OpenAI down → clean error, no scan count deducted |
| Backups | Supabase automatic backups (enabled by default on paid plan) | Verify restore procedure before launch |
| User-generated content | No direct UGC at MVP | Ratings and wishlist entries only |
| Terms of Service | Draft before App Store submission | Required for App Store review |
| DMCA policy | Not required at MVP | No user content uploads |

---

## 13. Open Questions

1. ~~**Misidentification reporting**~~ — **Resolved:** "Wrong book?" button on each
   result card. Flags and removes the result; logs to `misidentification_reports` for
   future prompt improvement. No scan credit refunded.

2. ~~**Scan image storage**~~ — **Resolved:** Ephemeral. Images not stored after
   the processing window closes.

3. **Goodreads live sync:** Goodreads API is deprecated. Monitor whether a third-party
   Goodreads sync solution (e.g., unofficial API wrappers) becomes viable or whether
   CSV-only import remains the only path.

4. **Amazon affiliate program:** Enrollment in Amazon Associates requires an active
   website and qualifying sales within 180 days. Default to direct Amazon search links
   at launch; swap in affiliate links once approved.

5. ~~**App name trademark check**~~ — **Resolved:** USPTO TESS search returned no
   conflicts for "LitHound." Clear to proceed.

6. ~~**React Native vs. Swift/SwiftUI**~~ — **Resolved:** React Native with Expo.
   Rationale: stronger AI code generation support, builder's JS/backend background,
   future cross-platform goal (Android + web from shared codebase).

7. ~~**Friends privacy model**~~ — **Resolved:** Mutual visibility by default on
   friendship acceptance. Either user can toggle "Hide my wishlist from friends' scans"
   in Settings. Notifications remain manual and one-directional.
