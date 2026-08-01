# Identity Kit

React SDK for embedding Thurin identity data. Drop-in components and hooks for displaying on-chain identity claims, PGP verification, social proofs, and EFP social graph data.

**npm:** [`@thurinlabs/identity-kit`](https://www.npmjs.com/package/@thurinlabs/identity-kit)
**Source:** [GitHub](https://github.com/thurinlabs/identity-kit)

## Install

```bash
npm install @thurinlabs/identity-kit
```

Peer dependencies: `react`, `react-dom`, `wagmi`, `viem`, `@tanstack/react-query`

## Quick Start

```tsx
import { IdentityKitProvider, ScryCard } from '@thurinlabs/identity-kit'
import '@thurinlabs/identity-kit/styles'

function App() {
  return (
    <IdentityKitProvider>
      <ScryCard ens="vitalik.eth" theme="thurin" />
    </IdentityKitProvider>
  )
}
```

## ScryCard

A self-contained identity card that fetches and displays all available identity data.

```tsx
<ScryCard ens="vitalik.eth" theme="thurin" />
<ScryCard address="0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045" theme="dark" />
```

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `ens` | `string` | — | ENS name to look up |
| `address` | `string` | — | ETH address to look up |
| `theme` | `'thurin' \| 'dark' \| 'light'` | `'thurin'` | Visual theme |

Displays: ENS avatar, name, address, Signet seal count, verified proof count, EFP follower count, proof provider badges, and a link to the full Scry profile.

## Provider

Wrap your app (or just the part using identity-kit) in `IdentityKitProvider`. If you already have a `WagmiProvider`, the SDK detects it and uses your existing config.

```tsx
// Zero config — uses public RPC, no Farcaster verification
<IdentityKitProvider>
  <ScryCard ens="vitalik.eth" />
</IdentityKitProvider>

// With options
<IdentityKitProvider
  rpcUrl="https://eth-mainnet.g.alchemy.com/v2/YOUR_KEY"
  neynarApiKey="YOUR_NEYNAR_KEY"
  scryBaseUrl="https://thurin.id"
>
  <ScryCard ens="vitalik.eth" />
</IdentityKitProvider>
```

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `rpcUrl` | `string` | publicnode | Ethereum RPC endpoint |
| `neynarApiKey` | `string` | — | Neynar API key for Farcaster proof verification |
| `scryBaseUrl` | `string` | `https://thurin.id` | Base URL for "View on Scry" links |

## Hooks

For custom UI, use the hooks directly instead of `ScryCard`.

### useScryIdentity

Combined identity data — ENS, Signet claims, PGP proofs, and EFP social graph.

```tsx
const identity = useScryIdentity('vitalik.eth')
// or
const identity = useScryIdentity('0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045')
```

Returns `ScryIdentity` with `address`, `ensName`, `ensAvatar`, `claims`, `totalClaims`, `activeClaims`, `currentFingerprint`, `pgpKeyInfo`, `proofs`, `efp`, `isLoading`, `error`.

### useSignetClaims

On-chain attestation data from the PGPRegistry contract.

```tsx
const { claims, totalClaims, activeClaims, currentFingerprint, isLoading } =
  useSignetClaims('0xd8dA...')
```

### useEFPGraph

EFP (Ethereum Follow Protocol) social graph data.

```tsx
const { efp, isLoading } = useEFPGraph('0xd8dA...')
// efp.followers, efp.following, efp.top8, efp.hasEfp
```

### usePGPProofs

PGP key info and verified social proofs from keyserver.

```tsx
const { keyInfo, proofs, isLoading } = usePGPProofs('03E53D807CE38C...')
// proofs[].provider, proofs[].status, proofs[].displayUrl
```

## Core Utilities

The verification and data logic is also exported as plain framework-agnostic functions — no React, no provider. This is the layer the `ScryCard`, the hooks, and the Scry explorer all build on, so a "verified" result is consistent everywhere. Use it directly when you need the validated data behind your own UI.

### Proofs

```ts
import { identifyProof, verifyProof, displayUrl, proofHref } from '@thurinlabs/identity-kit'

const proof = identifyProof({ name: 'proof@thurin.id', value: 'https://gist.github.com/alice/abc123' })
const result = await verifyProof(proof, fingerprint, neynarApiKey) // neynarApiKey only for Farcaster
// → { verified: boolean, reason?: string }
```

`verifyProof` runs the real per-provider check — for GitHub it confirms the gist is **owned** by the claimed user, so a proof can't be forged by pointing at someone else's gist.

### PGP

```ts
import { parsePgpKey, verifyAttestation, fetchKeyByFingerprint } from '@thurinlabs/identity-kit'

const keyInfo = await parsePgpKey(armoredKey)
// → { fingerprint, userIDs, algorithm, created, expires, notations, subkeys } | null

const verification = await verifyAttestation({ pgpPublicKey, pgpSignature, fingerprint, ethAddress })
// → { verified: boolean, reason?: string }
```

### EFP

```ts
import { fetchEFPGraph } from '@thurinlabs/identity-kit'

const graph = await fetchEFPGraph(address)
// → { followers, following, top8: string[], hasEfp } | null
```

### Contract constants

```ts
import { REGISTRY_ADDRESS, REGISTRY_ABI, CONTRACT_DEPLOY_BLOCK } from '@thurinlabs/identity-kit'
```

`REGISTRY_ABI` is read-only (events + `attestationCount` + `getAttestation`). Apps that write claims need their own ABI with `attest`/`revoke`.

## Themes

Three built-in themes: `thurin`, `dark`, `light`. All styles are scoped under `[data-scry-theme]` with `scry-` prefixed class names to avoid conflicts with your app's styles.

Import styles when using `ScryCard`:

```tsx
import '@thurinlabs/identity-kit/styles'
```

Hooks-only consumers don't need to import styles.

## Embed (No React Required)

For static sites, Jekyll blogs, WordPress, or any HTML page — use the standalone embed script. No React, no bundler, no config.

```html
<div data-scry-card="bendoubleu.eth" data-theme="thurin"></div>

<script src="https://cdn.jsdelivr.net/npm/@thurinlabs/identity-kit/dist/embed.global.js"></script>
```

| Attribute | Description |
|-----------|-------------|
| `data-scry-card` | ENS name or ETH address to look up (required) |
| `data-theme` | `thurin`, `dark`, or `light` (default: `thurin`) |

The script bundles everything internally. Cards render automatically on page load and for dynamically added elements.

## Card Image (No JavaScript Required)

For places where scripts can't run — GitHub READMEs, forum posts, emails — embed a server-rendered PNG identity card instead. Cards are 640×200, generated on the fly, and cached for an hour.

```
https://thurin.id/card/ens/:name
https://thurin.id/card/eth/:address
https://thurin.id/card/pgp/:fingerprint
```

A trailing `.png` on the identifier is accepted, which helps platforms that expect an image extension:

```markdown
[![Thurin identity](https://thurin.id/card/ens/bendoubleu.eth.png)](https://thurin.id/ens/bendoubleu.eth)
```

| Route | Looks up by |
|-------|-------------|
| `/card/ens/:name` | ENS name |
| `/card/eth/:address` | ETH address |
| `/card/pgp/:fingerprint` | PGP fingerprint |

The card shows the ENS avatar and name, address, Signet seal count, verified proof count, and EFP follower count — the same live data as `ScryCard`. Full-size 1200×630 share cards are also available at the matching `/og/ens/:name`, `/og/eth/:address`, and `/og/pgp/:fingerprint` routes; those are what social platforms receive automatically when a Scry link is shared, so you rarely need to link them directly.
