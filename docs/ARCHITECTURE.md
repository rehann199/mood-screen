# Architecture

```mermaid
flowchart TD
  subgraph App["Flutter app (phone or TV)"]
    Splash["Splash-only first frame"] --> Init["Deferred init: prefs, ads, analytics"]
    Init --> Shell["Home / app shell"]
    Shell --> Browse["Explore or Browse Categories"]
    Shell --> Setup["Setup flow: location, weather model, night mode"]
    Shell --> Settings["Settings"]
    Browse --> Player["Mood player"]
    Setup --> Player
    Settings --> Dialogs["Premium and location dialogs"]
  end

  subgraph State["State (Riverpod + shared_preferences)"]
    SettingsState["Display mode, overlays, dimming, night mode"]
    LocationState["Location source and city"]
    Premium["Premium status, purchased packs, rewarded unlock"]
  end

  subgraph Services["Services"]
    Weather["Weather API + IP geolocation"]
    Mapper["Condition to category mapper (day / night)"]
    Catalog["Curated video ID catalogue by tier"]
    Pexels["Pexels video API client"]
    Speed["Network speed probe"]
    Downloader["Priority downloader + file cache + fallback clip"]
  end

  subgraph Native["Kotlin (Android)"]
    Billing["Play Billing bridge"]
    Probe["4K decode probe + decoder config"]
    Wake["Keep-awake channel"]
    Saver["Screensaver service (TV)"]
  end

  Player --> SettingsState
  Player --> Mapper
  Weather --> Mapper --> Catalog --> Pexels --> Downloader --> Player
  Speed --> Catalog
  Probe --> Catalog
  Player --> Wake
  Dialogs --> Billing --> Premium
  Premium --> Browse
  Saver -. launches .-> Shell
  Shell -. TV build .-> Firebase[("Firebase: Auth, Firestore, Analytics, Crashlytics")]
  Player -. ads .-> Ads["AdMob (phone) / Unity Ads (TV)"]
```

## Notes

- The player owns video lifecycle, overlays, ad scheduling and recovery timers (stuck video, offline retry, slow internet).
- Navigation: phone uses a tab shell plus named routes (`/home`, `/locationSource`, `/weatherModel`, `/nightMode`, `/experience`, `/moodPlayer`, `/settings`); TV uses the same routes with focus-driven screens.
- Firebase is wired only in the TV build; the phone build compiles it out with a feature flag.
- Secrets (API keys) are loaded from a bundled env file at runtime. Sensitive configuration exists and is excluded from this repository.
