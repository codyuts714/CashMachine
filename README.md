# MONEY OS — UTS

Personal finance command center. Fixed expenses with due dates, debt destruction engine, collections tracker, income splits, runway. Black/gold, self-contained, Firebase-synced.

## Repo setup (same pattern as Daily OS)

1. New **private** repo → `money-os` (this is financial data — do NOT make it public with your config in it; the config isn't a secret key, but there's no reason to advertise your project)
2. Drop `money-os.html` in, rename to `index.html`
3. Settings → Pages → deploy from `main` → done

## Firebase setup (~5 min, reuse an existing project or make a new one)

**1. Realtime Database**
Firebase Console → Build → Realtime Database → Create. Then paste these rules
(Rules tab) so only YOU can read your money data:

```json
{
  "rules": {
    "moneyOS": {
      "$uid": {
        ".read": "auth != null && auth.uid === $uid",
        ".write": "auth != null && auth.uid === $uid"
      }
    }
  }
}
```

**2. Google sign-in**
Build → Authentication → Sign-in method → enable **Google**.

**3. Authorize your domain**
Authentication → Settings → Authorized domains → add `YOURUSERNAME.github.io`.
(Without this, the sign-in popup gets blocked on Pages.)

**4. Paste your config**
Project settings → General → Your apps → Web app → copy the `firebaseConfig`
object → paste it into the marked block at the top of the HTML file
(replacing the `PASTE_...` values). Make sure `databaseURL` is included —
grab it from the Realtime Database page if the config snippet omits it.

## How sync behaves

- **Opened as a local file (file://):** localStorage only. Sign-in popups don't
  work off file://, so the pill says "Local only" or sign-in will fail — expected.
- **Opened from GitHub Pages:** sign in once per device → full sync.
- First signed-in device seeds the cloud from its local data.
- Every write is tagged with a client ID; devices ignore echoes of their own
  writes. No sync loops.
- Last write wins. Don't edit on two devices in the same second and you'll
  never notice.
- Export backup still works everywhere — do one weekly, habit-stack it with
  your balance update.

## Namespacing

Data lives at `moneyOS/{your-uid}` — separate branch from Daily OS / Client OS
paths, so nothing collides even in the same Firebase project.
