# Thurin Proofs

Thurin Proofs are a decentralized identity verification system that links your PGP key to your online accounts. By adding cryptographic proofs to your PGP key and publishing verification tokens on supported platforms, you create a verifiable chain of identity that anyone can check using [Scry](https://scry.thurin.id).

## How It Works

Thurin Proofs use a **bidirectional linking** model:

1. **Your PGP key points to your account** — A `proof@thurin.id` notation in your PGP key contains a URL to a proof on a platform (a GitHub gist, a DNS TXT record, a Farcaster cast, a Codeberg repo, a Mastodon profile).

2. **Your account points back to your key** — The proof contains your PGP fingerprint in `openpgp4fpr:FINGERPRINT` format.

Anyone can independently verify both directions, confirming that the same person controls both the PGP key and the account.

```
┌──────────────┐    proof@thurin.id notation    ┌────────────────────┐
│              │ ─────────────────────────────→ │    Platform        │
│   PGP Key    │    (URL to proof post)         │  (GitHub, DNS,     │
│              │                                │  Farcaster,        │
│              │ ←───────────────────────────── │  Codeberg, etc.)   │
└──────────────┘    openpgp4fpr:FINGERPRINT     └────────────────────┘
```

## Prerequisites

1. **A PGP key** registered on [Signet](https://signet.thurin.id) (on-chain identity claim)
2. **GnuPG** installed locally to edit your key ([managing notations guide](/scry/gnupg))
3. **A keyserver account** on [keys.openpgp.org](https://keys.openpgp.org) — after adding proof notations, you must upload your updated key so Scry can read the new notations

## Supported Providers

| Provider | Proof Method | Notation Example |
|---|---|---|
| [Codeberg](/scry/codeberg) | Repo description | `proof@thurin.id=https://codeberg.org/user/repo` |
| [DNS](/scry/dns) | TXT record | `proof@thurin.id=dns:example.com?type=TXT` |
| [Farcaster](/scry/farcaster) | Public cast | `proof@thurin.id=https://farcaster.xyz/user/0xhash` |
| [GitHub](/scry/github) | Public gist | `proof@thurin.id=https://gist.github.com/user/id` |
| [Mastodon](/scry/mastodon) | Profile metadata | `proof@thurin.id=https://mastodon.social/@user` |

## Proof Content Format

All proofs must contain your PGP fingerprint using the `openpgp4fpr:` token:

```
openpgp4fpr:FINGERPRINT
```

Where `FINGERPRINT` is your full 40-character hex PGP fingerprint (case-insensitive).

The recommended format for proof content is:

```
thurin-id=openpgp4fpr:FINGERPRINT
```

This makes the purpose of the proof clear to anyone who sees it. The `thurin-id=` prefix is optional — Scry only checks for the presence of `openpgp4fpr:` followed by a matching fingerprint.

## Verification Flow

When Scry looks up an identity:

1. Fetches the PGP public key from `keys.openpgp.org` (by fingerprint from the on-chain attestation)
2. Parses `proof@thurin.id` notations from the key
3. For each notation, identifies the platform and fetches the proof content
4. Checks that the proof contains `openpgp4fpr:FINGERPRINT` matching the key
5. Displays a green checkmark for verified proofs, or an X with a reason for failures

All verification happens client-side in the browser. No backend or API keys are needed.
