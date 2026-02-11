# JavaScript SDK

Minimal SDK for checking Thurin SBT status from JavaScript/TypeScript.

## Installation

```bash
npm install @thurinlabs/sdk viem
```

## Quick Start

```typescript
import { ThurinSBT } from '@thurinlabs/sdk';
import { createPublicClient, http } from 'viem';
import { mainnet } from 'viem/chains';

// Always query mainnet (even if your dApp is on an L2)
const client = createPublicClient({
  chain: mainnet,
  transport: http()
});

// Create Thurin instance
const thurin = new ThurinSBT(client);

// Check if user is verified
const isVerified = await thurin.isValid(userAddress);
if (isVerified) {
  console.log('User has valid Thurin SBT');
}
```

## API Reference

### `ThurinSBT`

The main class for interacting with the Thurin SBT contract.

```typescript
const thurin = new ThurinSBT(client, address?)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `client` | `PublicClient` | A viem PublicClient |
| `address` | `Address` | Optional. Contract address (defaults to canonical address) |

---

### `isValid(user)`

Check if a user has a valid (non-expired) SBT.

```typescript
const isValid = await thurin.isValid('0x...');
// true or false
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `user` | `Address` | User's wallet address |

**Returns:** `Promise<boolean>`

---

### `getExpiry(user)`

Get the expiry timestamp for a user's SBT.

```typescript
const expiry = await thurin.getExpiry('0x...');
// 1735689600n (Unix timestamp as bigint)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `user` | `Address` | User's wallet address |

**Returns:** `Promise<bigint>` - Expiry timestamp (0 if no SBT)

---

### `getMintPrice()`

Get the current mint price in wei.

```typescript
const price = await thurin.getMintPrice();
// 1000000000000000n (0.001 ETH)
```

**Returns:** `Promise<bigint>` - Price in wei

---

### `getPoints(user)`

Get a user's points balance.

```typescript
const points = await thurin.getPoints('0x...');
// 500n
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `user` | `Address` | User's wallet address |

**Returns:** `Promise<bigint>` - Points balance

---

### `totalSupply()`

Get total number of minted SBTs.

```typescript
const supply = await thurin.totalSupply();
// 1234n
```

**Returns:** `Promise<bigint>` - Total supply

---

### `getStatus(user)`

Get full SBT status for a user in one call.

```typescript
const status = await thurin.getStatus('0x...');
// { isValid: true, expiry: 1735689600n, points: 500n }
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `user` | `Address` | User's wallet address |

**Returns:** `Promise<SBTStatus>`

```typescript
interface SBTStatus {
  isValid: boolean;
  expiry: bigint;
  points: bigint;
}
```

## Examples

### React Hook

```typescript
import { useEffect, useState } from 'react';
import { ThurinSBT } from '@thurinlabs/sdk';
import { usePublicClient } from 'wagmi';

function useThurinStatus(address?: string) {
  const client = usePublicClient();
  const [isVerified, setIsVerified] = useState(false);

  useEffect(() => {
    if (!client || !address) return;

    const thurin = new ThurinSBT(client);
    thurin.isValid(address).then(setIsVerified);
  }, [client, address]);

  return isVerified;
}
```

### Gate Content

```typescript
const thurin = new ThurinSBT(client);

async function handleAction(userAddress: string) {
  const isVerified = await thurin.isValid(userAddress);

  if (!isVerified) {
    throw new Error('Please verify at app.thurin.id first');
  }

  // Proceed with action...
}
```

## Testing (Sepolia)

For development, use the Sepolia testnet deployment:

```typescript
import { ThurinSBT } from '@thurinlabs/sdk';
import { createPublicClient, http } from 'viem';
import { sepolia } from 'viem/chains';

const SEPOLIA_SBT = '0x03812ef2AEF6666c14ce23EfDbF55bd4662BFbDf';

const client = createPublicClient({
  chain: sepolia,
  transport: http()
});

const thurin = new ThurinSBT(client, SEPOLIA_SBT);
```
