# Technical Case Study

## Scope

Two Flutter apps (phone and Android TV) around one idea: stream and loop curated ambient videos with minimal friction. About 35k lines of Dart in total plus about 1.5k lines of Kotlin.

## Project structure (both targets)

```
lib/
  main.dart            splash-only first frame, then deferred init, named routes
  screens/             home, explore/scenes, mood player, settings, setup flow, privacy
  dialogs/             premium, premium-feature, location, weather sync, speed warning
  services/            weather, pexels, video downloader/cache, network speed,
                       preferences, premium/subscription, rewarded unlock, billing bridge
  state/ providers/    Riverpod state: settings, location, weather, defaults
  models/              weather category (free, night, premium)
  ads/ analytics/      AdMob (phone) or Unity Ads (TV); event wrapper
  theme/               shared dark design system and responsive helpers
android/               Kotlin: MainActivity channels, billing, TV screensaver service
```

## Phone versus TV

| Concern | Phone | TV |
|---|---|---|
| Navigation | Bottom floating nav with Home, Explore and Settings tabs in an `IndexedStack`-style shell, plus named routes | Linear screens driven by focus and D-pad keys |
| Ads | Google AdMob (interstitial with 90 s cooldown, rewarded) | Unity Ads |
| Backend | Firebase code compiled out by flags (`kFirebaseEnabled = false`) | Firebase Auth, Firestore (premium record per user), Analytics, Crashlytics |
| Native | Billing bridge, 4K probe, keep-awake | Same, plus screensaver service and leanback manifest entries |

## Playback and content

- Scenes map to lists of curated Pexels video IDs per category and resolution tier (720p, 1080p, 1440p, 4K). The player resolves a video ID to a file URL through the Pexels API.
- Weather condition strings from the weather API (dozens of variants such as "patchy light rain with thunder") are normalised into a small set of categories, with separate day and night maps.
- The downloader keeps a priority queue, downloads to a temp file, renames atomically, tracks videos by ID, and trims old files globally.
- A fallback clip is bundled in assets and copied to the cache so the first launch can play immediately.
- Network speed is measured to choose a tier; a dialog warns when the connection is too slow for the chosen quality.
- A Kotlin channel answers "can this device hardware-decode 4K HEVC" using `MediaCodecList` and returns decoder-fallback settings.

## State and persistence

Riverpod providers hold display mode (manual or shuffle), night mode, clock and weather overlays, dimming level, default mood and location source. `shared_preferences` persists them. Purchased product IDs and premium status are stored locally; the TV build can also read a Firestore user document.

## Monetisation plumbing

A Kotlin method channel wraps Google Play Billing for subscriptions (monthly and yearly base plans) and one-time products; Dart maps feature names to product IDs. A rewarded-ad flow grants one hour of access to a premium category and is stored with an expiry.

## Testing and tooling

A default widget test exists in the phone project. Helper scripts in each project pick the smallest Pexels file per video ID, used when curating the catalogue. Verification for this case study was done by running both builds on Android emulators.
