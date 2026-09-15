# Sand Timer

A big friendly hourglass for young children. Sand drains from the top bulb and
piles up as a cone in the bottom one, so a child can see how much time is left
without reading numbers. Set any length from 5 seconds to 99:59 with the − / +
buttons or by typing it on a number pad, and the layout rearranges itself when you
turn the phone sideways. The timer has a face: it watches the sand fall while it
runs, and when the time is up it closes its eyes, smiles, bounces, and stars pop
around it while a soft bell rings for ten seconds.

## Using it

| Action | What happens |
|---|---|
| Tap the timer | Start / pause (or silence the bell, if it's ringing) — the on-screen caption says "Tap timer to start" |
| Press and hold the timer (0.7s) | Start over from full |
| Tap **−** / **+** | Change the length in 30-second steps |
| Tap the time in the middle | Open a number pad and type any length |
| Tap **Stop the bell** | Silence the bell straight away |
| Tap the padlock | Child lock — taps and presets stop working |
| Hold the padlock (1.2s) | Unlock |

The bell stops by itself after ten seconds. The **Stop the bell** button works even
when the child lock is on. The screen is kept awake while the timer runs and while
the bell rings, and the chosen length is remembered between sessions.

## Putting it on your Android phone, using only your phone

Chrome will not install anything that isn't on a public HTTPS address, so the app
has to be uploaded somewhere first. Use the folder version (`sand-timer.zip`),
not the one-file version — Chrome's real *Install app* option works best when
`manifest.json` and `sw.js` are actual files sitting next to the page.

### Step 1 — get the files onto the phone

1. Tap download on `sand-timer.zip` in the chat. It lands in **Downloads**.
2. Open **Files by Google**, tap the zip, tap **Extract**. You'll get a folder with
   seven files in it.
   https://support.google.com/files/answer/9048509

### Step 2 — upload them somewhere (pick one)

**Netlify Drop** — open https://app.netlify.com/drop in Chrome and tap the drop
zone. If a file picker opens, select all seven extracted files. If tapping does
nothing, use GitHub instead; the page is built around drag-and-drop and its
behaviour on phones is inconsistent.

**GitHub Pages** — sign in at https://github.com in Chrome and create a new public
repository. On the empty repository page tap **uploading an existing file**,
select all seven files at once (long-press the first, tap the rest), and commit.
Then go to **Settings → Pages**, set Source to *Deploy from a branch*, branch
`main`, folder `/ (root)`, and save. Give it a minute, then open the address it
shows you.
https://docs.github.com/en/pages/quickstart

### Step 3 — install it

Open the address in Chrome. If Chrome considers the app installable, a green
**Install** button appears in the app's own top bar — tap that. It is the same
install, without hunting through menus, and if it never appears then Chrome is not
treating the app as installable (usually `manifest.json` or `sw.js` missing from
the site root). The menu route is **⋮ → Install and create shortcuts → Install**.

Next to the Install button is a small **×**. Tapping it hides the button for good
on that browser, for anyone who just wants to use the timer in a tab. The choice is
stored per device, so it only affects the person who dismissed it — everyone you
share the link with still sees the offer. To bring it back, clear the site's data
in the browser settings. Choose *Install*, not
*Add to Home screen*: install gives a standalone app with its own icon, splash
screen and task-switcher entry, while Add to Home screen only makes a bookmark
that reopens Chrome. Chrome's install criteria are a manifest with a name, 192px
and 512px icons, a `start_url` and `display: standalone`, all served over HTTPS —
this app ships all of that.
https://developer.chrome.com/blog/update-install-criteria

Once installed it works offline, so it keeps working in the car or anywhere with
no signal.

### On iPhone, for reference

Safari → Share → *Add to Home Screen* → turn on *Open as Web App*.
https://support.apple.com/guide/iphone/open-as-web-app-iphea86e5236/ios

## Which file to use

| File | Use it when |
|---|---|
| `sand-timer.zip` | **Use this on Android.** Extract it and upload the seven files. Offline support and full Chrome install. |
| `sand-timer-one-file.html` | A fallback if you can only manage one file. Rename it `index.html`. No offline support, and Chrome may only offer a bookmark rather than a real install. |
| the loose files | You want to read or edit the source. |

The one-file version has no service worker, because a service worker has to be
served as its own file. Everything else is identical: it needs a connection on
first load, and behaves the same afterwards.

## Customising

Everything lives in `index.html`, near the top of the `<script>` block.

- **How the sand moves** — `var SAND_EXPONENT = 1;`. At 1 the sand level tracks
  elapsed time evenly: half the time gone means half the level. Set it to 0.5 for
  the physically accurate version, where volume drains at a constant rate — true
  to a real hourglass, but the level barely moves early on and races at the end.
- **Step size for − / +** — `var STEP_SECONDS = 30;`.
- **Shortest and longest allowed** — `var MIN_SECONDS = 5;` and `var MAX_SECONDS`
  (99:59 by default).
- **How long the bell rings** — `var RING_MS = 10000;` (milliseconds).
- **Gap between rings** — `var RING_GAP = 1200;`. Lower it for a more insistent
  bell, raise it for a gentler one.
- **Colours** — the `:root` block at the top of the `<style>` section:
  `--sky-1/2/3` (background), `--coral` (the timer's body), `--sand`, `--leaf`
  (the Stop button), `--ink` (text).
- **The bell itself** — the `chime()` function; `[523.25, 659.25, 783.99]` are the
  note frequencies (C5–E5–G5), and `0.22` in the gain ramp is the volume.
- **Hold-to-restart delay** — the `700` in the `pointerdown` handler on the glass.

After editing, bump `const CACHE = "sand-timer-v9"` in `sw.js` to `v10`, or installed
phones will keep serving the old cached version.

## Known limitations

- **Vibration and screen-wake both work on Android.** The phone buzzes along with
  the bell, and the screen stays on while the sand falls. Chrome has supported the
  Screen Wake Lock API since version 84.
  https://developer.mozilla.org/en-US/docs/Web/API/Screen_Wake_Lock_API
- On iPhone neither is guaranteed: Apple has formally opposed the Vibration API,
  so `navigator.vibrate` does nothing, and wake lock inside an installed web app
  only works from iOS 18.4 onward. The code checks before calling either.
  https://web-platform-dx.github.io/web-features-explorer/features/vibration
- Phones only allow sound after you have tapped something, so the bell needs the
  timer to have been started by a tap in that session. Starting it the normal way
  satisfies this. On Android the bell follows your media volume, so check that
  isn't turned down.
- The timer only counts while the app is open and visible. There is no background
  notification; a backgrounded web app cannot reliably alert you.

## Files

```
index.html               the whole app (markup, styles, logic)
manifest.json            name, icons, colours for Home Screen installation
sw.js                    service worker — makes it work offline
icon-192.png             Home Screen icon
icon-512.png             Home Screen icon, large
icon-maskable-512.png    Android adaptive icon
```
