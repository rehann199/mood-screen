# Engineering Challenges

Design problems in this product and how they were approached, plus current boundaries of the project.

## 1. Playing video well on very different hardware

TV boxes and phones differ widely in decoders. The app asks native code whether 4K HEVC can be decoded, prefers a safe decoder configuration, and picks a resolution tier from measured network speed. The catalogue holds separate ID lists per tier so a weak device is never handed a stream it cannot play.

## 2. A fast, resilient start

The first frame is a splash-only widget; everything else initialises afterwards. A bundled fallback video is copied into the cache so playback can begin before any download completes, and an offline retry timer re-attempts every 30 seconds.

## 3. Turning weather text into scenes

Weather APIs return many free-text condition strings. They are mapped to a small category set, with different sets for day and night (for example no "Sunny" at night), then to curated video lists.

## 4. Storage that stays bounded

The downloader writes to temp files then renames, tracks files by video ID, and applies a global retention limit while protecting the fallback clip.

## 5. Monetisation across free and paid scenes

Free scenes, subscription, one-time packs and a one-hour rewarded unlock all have to resolve to one answer: "which categories may this user play now". That logic sits in the video service and persistence layer, and the UI shows locks and PRO labels from the same source.

## 6. Two form factors from one codebase family

Phone and TV are separate projects that share most domain code. The cost is duplication of several files; the benefit is that TV focus handling and ad SDKs do not leak into the phone build.

## Current boundaries

- The product is not publicly released.
- The phone project's Firebase features are switched off by a build flag, and ads use Google test identifiers in the debug build used for screenshots.
- The iOS target exists but was not exercised here.
- Video and weather content depend on third-party APIs, so playback needs network access on first use.
- Shared logic is duplicated between the two projects rather than extracted into a package.
