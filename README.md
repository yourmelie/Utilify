<div align="center">

# ◆ &nbsp;Utilify&nbsp; ◆

<img width="1890" height="960" alt="{44FA1ACB-A793-4978-86A3-C87BCA84C7FB}" src="https://github.com/user-attachments/assets/60e7a9f6-003c-42c4-b6d5-9e7e218abd52" />


<br>

**A comprehensive userscript suite for [KoGaMa](https://www.kogama.com)**

*Visual modernization · Privacy tools · Profile customization · Plugin system*

<br>

[![Version](https://img.shields.io/badge/version-3.2.0-white?style=flat-square&labelColor=111)](https://github.com/lappisu/Utilify)
[![Grant](https://img.shields.io/badge/grant-none-white?style=flat-square&labelColor=111)](https://github.com/lappisu/Utilify)
[![License](https://img.shields.io/badge/license-community-white?style=flat-square&labelColor=111)](https://github.com/lappisu/Utilify)
[![Platform](https://img.shields.io/badge/platform-ScriptCat%20%7C%20Tampermonkey-white?style=flat-square&labelColor=111)](https://scriptcat.org/)

</div>

<br>

## Installation

**Recommended manager:** [ScriptCat](https://scriptcat.org/) has the best compatibility. Tampermonkey is also supported.

**[◆ &nbsp;Install Utilify v3.2.0](https://github.com/lappisu/Utilify/raw/refs/heads/main/Script/Rewrite/Utilify.user.js)**

> [!NOTE]
> Utilify runs with `@grant none`. It requires no special userscript permissions and operates entirely within the page context.

<br>

## Compliance

Utilify is a third-party extension. Users are solely responsible for compliance with KoGaMa's Terms of Service.

The following guidelines are observed by the developer:

- No violation of other users' privacy
- No spam-polling of API endpoints
- No interference with the game client

> [!WARNING]
> Features marked **UAOR** (Use At Your Own Risk) interact with the API in ways that edge these guidelines. They are disabled by default and must be opted into explicitly from the settings panel.

<br>

## Changelog

### v3.2.0 &nbsp;·&nbsp; Noir Celestial

> The largest visual overhaul since the rewrite, accompanied by a serious stability pass targeting the root cause of random idle-time crashes.

<br>

**◆ &nbsp;Settings panel redesign**

The panel has been rebuilt from the ground up with a new visual identity:

- Deep `#080808` base with a two-layer SVG fractal noise texture; the grain layer drifts slowly via CSS animation for subtle depth without static flatness
- Live star field rendered on a `<canvas>` inside the panel: twinkling dots of varying size and phase, with occasional sparkle bursts that float upward and fade out
- Spring-physics open/close animation with backdrop blur overlay
- Travelling shimmer line on the panel header, animated `✦` markers on the active sidebar tab
- Navbar settings button replaced with an SVG gear icon that rotates 90° on hover
- Update notification redesigned to match: same noise texture, same monochrome palette, compact two-row layout with no overlapping buttons

<br>

**◆ &nbsp;Hue-relative accent color**

Colored text that previously used a hardcoded `#4adeb7` teal (feed author names, some button labels, linkified imgur URLs) is now driven by the **Hue** slider in the Styles tab.

Because stylesheet `!important` declarations cannot be overridden by other stylesheets regardless of specificity, the implementation uses a JS enforcer: `element.style.color` is stamped directly on each matching element (inline styles win the cascade unconditionally), and a `MutationObserver` keeps newly rendered elements in sync as the page updates.

<br>

**◆ &nbsp;Paste screenshots directly into chat**

Paste any image from your clipboard into any text field or textarea on the site. The image uploads automatically to Imgur and the resulting URL is inserted at the cursor. Works with `contenteditable` fields as well as standard `<textarea>` and `<input>` elements.

<br>

**◆ &nbsp;Imgur link preview**

Hover any imgur URL in a wall post, comment, or DM message to see a floating image preview card. Scroll the tooltip to resize it between 80px and 640px wide. The trigger is context-sensitive: it only fires from `<a>` tags with imgur hrefs, your own linkified URLs, or known safe text containers. It will not fire from DM compact preview rows or ambient background text. Hover delay is 280ms to prevent accidental triggers when the mouse passes through text quickly.

<br>

**◆ &nbsp;Stability: DOM crash fixes**

Five categories of crashes were identified and resolved. All shared the same root cause: userscript code touching React-managed DOM nodes either during React's reconciliation pass, or using stale node references captured before a re-render.

| Location | Problem | Fix |
|---|---|---|
| Three `insertBefore` call sites | Stored `firstChild` reference then used it after React replaced the subtree | Validates `refNode.parentNode === container` before inserting; falls back to `appendChild` on failure |
| `guard()` MutationObserver | Called `removeReactBits()` synchronously mid-reconcile | Debounced 50ms; `document.contains()` liveness check on every node before mutation |
| `linkifyNode` | Called `parentNode.replaceChild()` on nodes collected by a TreeWalker; React can detach them before the loop reaches them | Two liveness guards flanking the replace, wrapped in `try/catch` |
| Linkify MutationObserver | Fired synchronously inside React's reconciliation batch | Debounced 60ms with a `Set`-based queue; rapid bursts coalesced into one pass |
| `checkForUpdates` async chain | No top-level error handling; rejected promises surfaced as uncaught console errors | Full `try/catch` wrapper; update checks are non-critical and fail silently |

<br>

**◆ &nbsp;Other fixes**

- `getInstalledVersion`, `parseVersion`, `compareVersions`, and `fetchRemoteVersion` were accidentally removed during a previous edit and have been restored
- Global CSS block updated: added `.zUJzi ._2BvOT ._375XK textarea` transparency fix; removed the hardcoded teal color rule, now handled by the JS enforcer
- Incoming message bubble background opacity reduced for improved readability
- `window.UtilifyUpdateChecker.testNotification()` now works correctly from the console; a commented-out test line is present in `init()` for local testing

<br>

### v3.1.0

- **Profile bookmarks.** Bookmark any profile from its page, pin up to 2 to the friends sidebar, manage the full list from a collapsible panel
- **Add Friend button restored.** Re-injects the button on profiles where you are not already friends, using the same MUI class names as KoGaMa's own buttons
- **Seamless description box.** Replaces KoGaMa's truncated bio display with a scrollable full-text box that decodes HTML entities, Unicode escapes, and `\n` literals
- **Leaderboard fix.** Rewrites the `around_me` endpoint to use the actual profile UID from the page URL rather than the hardcoded value in KoGaMa's own code

### v3.0.x

Initial rewrite. Plugin system, UAOR tab, friend activity monitor, lazy streak keeper, avatar marketplace finder, faster friends panel, feed manager, background effects engine.

<br>

## Features

### Visual Overhaul

Utilify applies a suite of CSS corrections on page load:

- Removes the badge row, streak counter, level widget, and footer silently
- Neutralizes the orange notification badges
- Applies glassmorphic blur to avatar card title overlays
- DM textarea background set to transparent for cleaner layering with the frosted glass panel

The entire DM panel is restyled: frosted glass background, accent border on the active conversation, blurred avatars in the list (revealed on hover), styled message bubbles with "Them / You" micro-labels.

<br>

### Settings Panel

Accessible via the gear button injected into the site navigation. The panel is draggable, animates open with a spring curve, and persists all settings to `localStorage`.

| Tab | Contents |
|---|---|
| **Gradient** | Two color pickers, angle slider, direct CSS input. Applied live with a 150ms debounce. Copy CSS button included. |
| **Privacy** | Hide friendslist, blur sensitive content (hover to reveal), blur comments (hover to reveal) |
| **Styles** | Glass panels with configurable radius, hue, and alpha. Hue slider also drives all accent-colored text site-wide. Online CSS URLs. Custom CSS. |
| **Fonts** | System default, Roboto, Comfortaa, or any Google Fonts `<link>` href |
| **Plugins** | External plugin loader, see [Plugin System](#plugin-system) |
| **UAOR** | Opt-in risky features, see [UAOR Features](#uaor-features) |
| **About** | Credits and contributors |

<br>

### Background Effects Engine

Triggered by a syntax string placed anywhere in your **Profile Description**.

```
Background: SOURCE, filter: EFFECTS
```

**Sources**

| Format | Example | Result |
|---|---|---|
| Imgur ID | `i-aBcDeFg` | `https://i.imgur.com/aBcDeFg.*` (extension auto-detected) |
| KoGaMa game ID | `1234567` | Game cover image fetched via KoGaMa API |

**Effects**

| Keyword | Description |
|---|---|
| `rain` | Canvas rain streaks, 60 particles with depth simulation |
| `snow` | Rotating SVG snowflakes with gentle sway, 80 particles |
| `fireflies` | Floating glowing dots that pulse, 50 particles |
| `roses` | Falling rose SVGs with slow rotation, 35 particles |
| `sparkles` | Fading four-point star bursts, 40 particles |
| `blur` | Applies `blur(4px)` to the background image |
| `dark` | Applies `brightness(0.7)` to the background image |

Effects can be combined freely. Typing `filter:` in the profile description textarea shows a floating tooltip listing all available keywords.

**Examples**

```
Background: i-aBcDeFg, filter: rain, blur
Background: 1234567, filter: snow, dark
Background: i-aBcDeFg, filter: roses, sparkles
```

<br>

### Profile Bookmarks

A **Bookmark** button is injected on every profile page. Bookmarks appear as a collapsible bar inside the friends sidebar.

- Up to 50 bookmarks stored in `localStorage`
- Pin up to 2 profiles to the top of the bar for quick access
- Full list accessible via the manage panel: click to visit, star to pin, remove as needed

<br>

### Add Friend Button

Injects an **Add Friend** button on profiles where you are not already friends and it is not your own profile. Clicking sends a friend request; clicking again cancels it.

<br>

### Imgur Link Preview

Hover any imgur URL in a wall post, comment, or DM message to see a floating preview. Scroll the card to resize it. The trigger is deliberately conservative: it only activates from link elements and known safe text containers, not from ambient text or compact DM preview rows.

<br>

### Paste to Imgur

Paste any clipboard image (screenshot, copied photo) into any text field. It uploads to Imgur automatically and inserts the URL at the cursor. A small toast confirms the upload or reports failure.

<br>

### Seamless Description

Replaces KoGaMa's truncated bio with a scrollable full-text box. Decodes HTML entities (up to 10 passes), Unicode escapes (`\uXXXX`), literal `\n` newlines, and Braille space characters. Box height adapts to the surrounding layout. A debounced MutationObserver prevents React from re-rendering the original truncated elements back in.

<br>

### React-Compatible Input Decoder

On focus or click, Utilify decodes HTML entities in any input or textarea and fires the synthetic React events required for the change to register. Without this, edited bios fail to save because React does not observe the value change.

<br>

### Smart Dot Obfuscation

Replaces `.` with `%2E` in text inputs and paste events, bypassing KoGaMa's URL filter. YouTube and Google Fonts URLs are whitelisted and pass through unmodified.

<br>

### Profile Info Chips

Replaces the "Joined KoGaMa on..." span with a row of expandable chips: account creation date, last seen time (relative by default, full date and timezone on click), last played game (own profile only, sourced from `localStorage` cache), and an Internet Archive link for the current profile URL.

<br>

### Compact Level Display

Injects a small "Level N" label below the username on profile pages. The native oversized level widget is hidden globally via CSS.

<br>

### Leaderboard Fix

Rewrites outgoing requests to `/api/leaderboard/around_me/` to use the profile UID from the current page URL. KoGaMa's own code sends the wrong UID for this endpoint, breaking the "Around Me" leaderboard view. Also highlights your own row in the table. Credit for the idea: **Zpayer**.

<br>

### Feed Manager

Appears as a card on your own profile page only. Opens a full-screen overlay listing your feed posts with per-post delete and a Delete All option (requests are spaced 500ms apart to avoid hammering the API). Posts animate out on removal.

<br>

### Faster Friends Panel

On the friends page, replaces the default paginated list with a single-request panel fetching up to 555 entries. On your own profile: Friends, Incoming Requests, and Sent Requests in separate sections. On other profiles: their Friends and your Mutual Friends. Live search, alphabetical sort, draggable and collapsible, Escape key to toggle.

<br>

### Avatar Marketplace Finder

On avatar list pages, avatar names render as animated gradient links. Clicking one searches the marketplace by name and image hash, displaying results in a searchable grid panel. Uses up to 5 concurrent requests across 50 pages. A matched avatar is highlighted and auto-opened in the marketplace.

<br>

### Friend List Search

Injects a search input into the friends list toolbar. Filtering is instant, and hidden elements retain their original DOM position so they re-appear in the correct order when the filter is cleared.

<br>

## UAOR Features

> [!CAUTION]
> These features are disabled by default and must be enabled explicitly in the **UAOR** tab. They may interact with the KoGaMa API in ways that edge or violate the Terms of Service. Use them at your own risk.

**Appear Offline** intercepts all `POST /user/ID/pulse/` requests via both `fetch` and `XMLHttpRequest` and drops them silently, making you appear offline to other users.

**Friend Activity Monitor** polls `/user/ID/friend/chat/` every 30 seconds, resolves game and project IDs to human-readable names, and updates the status text displayed in friend list entries.

**Player Type Display** injects a small chip next to the game title on game pages showing current Members and Tourists counts.

**Lazy Streak Keeper** sends automated DMs to the streak bot account ([670350173](https://www.kogama.com/profile/670350173/)) on a 7-hour interval. Requires friending that account first.

<br>

## Auto-Update Checker

Checks GitHub for a newer `@version` header at most once per 24 hours. A floating notification appears at the top of the page when an update is available, styled to match the panel. It auto-dismisses after 45 seconds.

```js
// Force a check immediately
window.UtilifyUpdateChecker.check()

// Preview the notification UI without waiting for a real update
window.UtilifyUpdateChecker.testNotification()
```

<br>

## Plugin System

Install external JavaScript via the **Plugins** tab. Paste a raw GitHub URL and click **Add Plugin**. The code is fetched, stored in `localStorage`, and injected as an inline `<script>` tag on every page load.

### Metadata

Utilify reads structured metadata from comment lines at the top of the plugin file using the format `// Key * Value`:

```js
// Title * My Plugin Name
// Desc  * A short description
// Ver   * 1.0.0
// Date  * 19 Feb 2026
// Auth  * YourName
```

All fields are optional. Plugins without them load normally but appear as "Unnamed Plugin" in the UI.

### Limitations

> [!IMPORTANT]
> Utilify runs with `@grant none`, which means Greasemonkey / Tampermonkey privileged APIs are unavailable in the page context. Plugins are injected as plain `<script>` tags and share this limitation.
>
> Use `fetch` instead of `GM_xmlhttpRequest`, and `localStorage` instead of `GM_getValue` / `GM_setValue`. Prefix your storage keys to avoid collisions with Utilify or other plugins.

### Template

```js
// Title * My Plugin
// Desc  * Does something on KoGaMa
// Ver   * 1.0.0
// Date  * 19 Feb 2026
// Auth  * YourName

(function () {
    'use strict';

    async function fetchData(url) {
        const res = await fetch(url);
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
    }

    function getSetting(key, fallback) {
        try { return JSON.parse(localStorage.getItem('myplugin_' + key)) ?? fallback; }
        catch { return fallback; }
    }

    function setSetting(key, value) {
        localStorage.setItem('myplugin_' + key, JSON.stringify(value));
    }

    console.log('[My Plugin] Loaded');
})();
```

<br>

## Quick Reference &nbsp;·&nbsp; Background Effects

```
Background: i-IMGUR_ID                           →  Imgur image, no effects
Background: GAME_ID                              →  KoGaMa game cover, no effects
Background: i-IMGUR_ID, filter: rain             →  Rain overlay
Background: i-IMGUR_ID, filter: snow, dark       →  Snow and darkened image
Background: GAME_ID, filter: fireflies, blur     →  Fireflies and blurred image
Background: i-IMGUR_ID, filter: roses, sparkles  →  Two particle effects combined
```

`rain` · `snow` · `fireflies` · `roses` · `sparkles` · `blur` · `dark`

<br>

## Contributors

| Name | Contribution |
|---|---|
| **Simon** | Lead developer, core maintainer |
| **Death Wolf.** | Primary motivation behind the project |
| **Sorry** | Testing, feedback, CSS design |
| **Zpayer** | Leaderboard fix concept and implementation; various suggestions |
| **Awoi** | Porting features from KoGaMaBuddy |
| **Idealism** | Feedback, avatar search |
| **Selene** | Feedback, avatar search |
| **Snowy** | Feedback and bug reports |
| **Comenxo** | Testing and feedback |
| **Raptor** | Testing and feedback |
| **ReZa** | Testing and feedback |
| **Tungsten** | Feedback |
| **ValDon** | Feedback |

<br>

---

<div align="center">

*Utilify is an independent community project.*
*It is not affiliated with or endorsed by KoGaMa.*

</div>
