# BizDeck — Access Keys & Passwords

This file lists every hardcoded key and password found in `index.html`. These are **not** per-user secrets generated at signup — they're the fixed values baked into the app's source code that gate access (which category a signup unlocks, demo login, etc.).

⚠️ **Keep this file private.** Anyone with these keys can create accounts in your app. Delete or move this file somewhere secure after reviewing it — don't ship it inside the deployed app.

---

## Demo login

Used to sign in without creating a real account (see "Continue with demo" / demo sign-in flow).

| Field | Value |
|---|---|
| Email | `demo@bizdeck.app` |
| Password | `demo1234` |

## Signup key (general)

Required to create a real (non-demo) account, regardless of business category.

| Key | Value |
|---|---|
| `SIGNUP_KEY` | `BLYZED123` |

## Premium key

Works at signup for **any** business vertical and flags the account as premium (`universalAccess` internally), unlocking a module switcher in Account so it can move between every vertical on the same device.

| Key | Value |
|---|---|
| `PREMIUM_KEY` | `MASTERKEY-ALL` |

## Admin key

Unlocks the built-in "Multiple Business Access" premium vertical.

| Key | Value |
|---|---|
| `ADMIN_ACCESS_KEY` | `ADMIN-MASTER` |

## Hardcoded developer login

A second, separate backdoor login baked directly into the sign-in check (not the demo account) — grants full admin state (`state.isAdmin = true`) regardless of the access-key system above.

| Field | Value |
|---|---|
| Email | `mail.morrisok@gmail.com` |
| Password | `Blyzed123` |

## Per-category signup access keys

Each business category the welcome screen offers has its own required access key:

| Category | Access key |
|---|---|
| Multiple Business Access (premium) | `ADMIN-MASTER` |
| Tailoring / Fashion Design | `TAILOR-2026` |
| Restaurant / Food & Drinks | `FOOD-2026` |
| Salon & Spa / Barbing | `SALON-2026` |
| Retail / Boutique Store | `RETAIL-2026` |
| Real Estate / Property | `ESTATE-2026` |
| Event Planning / Rentals | `EVENTS-2026` |
| Laundry / Dry Cleaning | `LAUNDRY-2026` |
| Other business | `OTHERS-2026` |

---

## Where these live in the code

- `DEMO_EMAIL`, `DEMO_PASSWORD` — demo login credentials
- `SIGNUP_KEY` — general signup gate
- `PREMIUM_KEY` — cross-vertical premium key (was `UNIVERSAL_KEY`)
- `ADMIN_ACCESS_KEY` — premium/admin vertical gate
- `CATEGORY_CONFIG.<category>.accessKey` — per-vertical signup key
- the hardcoded developer login is inline in the `sign-in` case of the action handler, not a named constant — search for `mail.morrisok@gmail.com` to find it

All of these are plain constants near the top of the `<script>` block in `index.html` — search for `const DEMO_EMAIL` to find the section. Change any of them there before distributing the app if you want different values.

## Not included here: per-account keys

Once someone signs up for real, the app generates a unique account access key (`generateAccessKey()`) and lets the user set their own password. Those are created per install/account, stored on-device, and shown once in Account → "Sign-in key + password" (with a download-to-file option). They aren't fixed values, so they can't be listed here — this file only covers the constants built into the app itself.
