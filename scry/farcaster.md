# Farcaster Proof

Verify your Farcaster identity by publishing a cast containing your PGP fingerprint.

## 1. Publish a Proof Cast

Post a cast on Farcaster (via Farcaster or any client). The cast must contain your fingerprint — the rest is up to you.

Example:

```
Verifying my identity with @thurinlabs

https://scry.thurin.id/#/pgp/YOUR_FINGERPRINT
```

The Scry URL contains your fingerprint, so it doubles as both a clickable link and the verification token. Anyone who sees the cast can click through to your Scry profile.

You can also use the explicit format:

```
thurin-id=openpgp4fpr:YOUR_FINGERPRINT
```

Scry only looks for `openpgp4fpr:` followed by your fingerprint anywhere in the cast text.

## 2. Add the Notation to Your PGP Key

Copy the cast URL from Farcaster and add it as a notation ([GnuPG guide](/scry/gnupg)):

```bash
gpg --edit-key YOUR_FINGERPRINT
uid 1
notation proof@thurin.id=https://farcaster.xyz/USERNAME/0xCASTHASH
save
```

## 3. Upload Your Updated Key

```bash
gpg --keyserver hkps://keys.openpgp.org --send-keys YOUR_FINGERPRINT
```

## What Scry Checks

1. Extracts the cast hash from the Farcaster URL
2. Fetches the cast via a Farcaster Hub REST API
3. Searches the cast text for `openpgp4fpr:FINGERPRINT`
4. Shows a green checkmark if the fingerprint matches

## What It Looks Like in Scry

```
✓  FARCASTER  @username  [proof]
```

The username links to your Farcaster profile. The `[proof]` link opens the specific cast.

## Requirements

- The cast must be public (not a direct cast or channel-restricted)
- The cast URL must match `https://farcaster.xyz/:username/0x:hash`
- Do not delete the cast — Scry re-verifies on each lookup

## Notes

- Your proof cast is permanent on the Farcaster protocol — even if deleted from a client, it may persist on hubs
- Verification is client-side via the [Neynar](https://neynar.com) Farcaster Hub API
