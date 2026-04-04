# Mastodon Proof

Verify your Mastodon identity by adding your PGP fingerprint to your profile.

## 1. Add Your Fingerprint to Your Mastodon Profile

Go to your Mastodon instance's profile settings and add a metadata field linking to your Scry profile:

- **Label:** `PGP` (or anything you like)
- **Value:** `https://scry.thurin.id/#/pgp/YOUR_FINGERPRINT`

You can use either your full 40-character fingerprint or your 16-character key ID in the URL. Both work.

Alternatively, you can add the Scry link to your bio.

## 2. Add the Notation to Your PGP Key

Add your Mastodon profile URL as a notation ([GnuPG guide](/scry/gnupg)):

```bash
gpg --edit-key YOUR_FINGERPRINT
uid 1
notation proof@thurin.id=https://INSTANCE/@USERNAME
save
```

For example:

```bash
notation proof@thurin.id=https://mastodon.social/@alice
```

## 3. Upload Your Updated Key

```bash
gpg --keyserver hkps://keys.openpgp.org --send-keys YOUR_FINGERPRINT
```

## What Scry Checks

1. Extracts the instance and username from the Mastodon profile URL
2. Fetches the account via the Mastodon API (`/api/v1/accounts/lookup?acct=USERNAME`)
3. Searches profile metadata fields and bio for the fingerprint (full 40-char or last-16 key ID)
4. Shows a green checkmark if the fingerprint matches

## What It Looks Like in Scry

```
✓  MASTODON  @username@instance  [proof]
```

The handle links to your Mastodon profile. The `[proof]` link also opens your profile (since the proof lives there, not in a separate post).

## Requirements

- Your profile metadata or bio must contain your fingerprint (typically via a Scry URL)
- The notation URL must match `https://INSTANCE/@USERNAME`
- Do not remove the fingerprint from your profile — Scry re-verifies on each lookup

## Notes

- Unlike other providers, Mastodon proofs check your **profile**, not a specific post — so there's nothing to accidentally delete
- Verification uses the public Mastodon API (no authentication required)
- Works with any Mastodon-compatible instance (Mastodon, Hometown, etc.)
- The Mastodon API strips HTML from field values before checking, so links and formatting are handled automatically
