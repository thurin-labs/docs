# Thurin

> Prove more. Reveal less.

Thurin is a privacy-preserving identity verification protocol. Users verify their identity once using a mobile driver's license (mDL), then receive a soulbound token (SBT) that proves they're a unique human—without revealing any personal information.

## How It Works

1. **User verifies** - Connect wallet, share mDL via Apple/Google Wallet
2. **ZK proof generated** - Proves validity without revealing PII
3. **SBT minted** - On-chain proof of unique human status
4. **dApps check** - One line of code to verify users

## Integrate Thurin

### JavaScript/TypeScript

```typescript
import { ThurinSBT } from '@thurinlabs/sdk';
import { createPublicClient, http } from 'viem';
import { base } from 'viem/chains';

const client = createPublicClient({ chain: base, transport: http() });
const thurin = new ThurinSBT(client);

// Check if user is verified
const isVerified = await thurin.isValid(userAddress);
```

### Solidity

```solidity
import { ThurinSBT } from "@thurinlabs/contracts/interfaces/ThurinSBT.sol";

contract MyDapp {
    function doThing() external {
        require(ThurinSBT.isValid(msg.sender), "Not verified");
        // User is a verified unique human
    }
}
```

## Links

- [App](https://app.thurin.id) - Get verified
- [Website](https://thurin.id) - Learn more
- [GitHub](https://github.com/thurin-labs) - Source code
