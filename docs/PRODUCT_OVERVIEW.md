# Product Overview

## Purpose

Mood Screen turns a screen that would otherwise sit idle into an ambient display: looping, high-quality scenes (weather, sky, nature, fire, ocean) with an optional clock overlay. It targets two situations: a TV used as a calm background, and a phone used as a bedside or desk display.

## Platforms

| Target | Evidence in the project |
|---|---|
| Android TV | Separate Flutter project; manifest declares the leanback feature, a TV banner and a LEANBACK launcher category; a screensaver (Daydream) service; every screen uses focus nodes and key handling. Run on the Android TV (1080p) emulator for the screenshots. |
| Android phone | Separate Flutter project with portrait and landscape orientations and a three-tab shell (Home, Explore, Settings). Run on a Pixel 4a emulator. |
| iOS | The phone project contains an iOS runner and an App Store listing reference document. It was not run for this case study, and the Firebase-based features are switched off by a build flag there. |

No release or store availability is claimed here.

## Experience

1. **Start**: splash, then (phone, free users) a startup interstitial ad, then the app shell.
2. **Choose a mood**: pick one scene, shuffle all, shuffle free only, or shuffle premium only.
3. **Optionally sync with weather**: current conditions and day or night select a category.
4. **Play**: full-screen video with a clock overlay; a small control menu offers back, refresh and settings (and pause on TV).
5. **Customise**: overlays, screen dimming, default mood, night mode, location.

## Scene catalogue

Free: Clear, Sunny, Cloudy, Rain, Snow, Fog (with night variants for most).
Premium: Night Sky and Stars, Moon Phases, Nature, Galaxy Drift, Fireplace, Marine Life.

## Monetisation model

- Subscription with monthly and annual plans (presented in a plan-selection dialog).
- One-time purchases for individual premium scene packs.
- Rewarded ad that unlocks a premium category for one hour.
- Interstitial ads for free users with a cooldown.

## Screens

See the gallery in the [README](../README.md).
