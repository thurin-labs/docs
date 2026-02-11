# Solidity Integration

Solidity library and interface for on-chain Thurin verification.

## Installation

### Foundry

```bash
forge install thurinlabs/contracts
```

### npm

```bash
npm install @thurinlabs/contracts
```

## Quick Start

```solidity
import { ThurinSBT } from "@thurinlabs/contracts/interfaces/ThurinSBT.sol";

contract MyDapp {
    function doThing() external {
        require(ThurinSBT.isValid(msg.sender), "Not verified");
        // User is a verified unique human
    }
}
```

That's it. One import, one line.

## API Reference

### `ThurinSBT` Library

The library wraps the canonical Thurin SBT contract address, so you don't need to manage addresses.

---

### `isValid(user)`

Check if a user has a valid (non-expired) SBT.

```solidity
function isValid(address user) internal view returns (bool)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `user` | `address` | User's wallet address |

**Returns:** `true` if user has valid SBT

---

### `getExpiry(user)`

Get the expiry timestamp for a user's SBT.

```solidity
function getExpiry(address user) internal view returns (uint256)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `user` | `address` | User's wallet address |

**Returns:** Expiry timestamp (0 if no SBT)

---

### `getMintPrice()`

Get the current mint price.

```solidity
function getMintPrice() internal view returns (uint256)
```

**Returns:** Price in wei

---

### `points(user)`

Get a user's points balance.

```solidity
function points(address user) internal view returns (uint256)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `user` | `address` | User's wallet address |

**Returns:** Points balance

## Interface

If you prefer to use the interface directly with a custom address:

```solidity
import { IThurinSBT, THURIN_SBT } from "@thurinlabs/contracts/interfaces/IThurinSBT.sol";

contract MyDapp {
    function doThing() external {
        require(IThurinSBT(THURIN_SBT).isValid(msg.sender), "Not verified");
    }
}
```

### `IThurinSBT`

```solidity
interface IThurinSBT {
    function isValid(address user) external view returns (bool);
    function getExpiry(address user) external view returns (uint256);
    function getMintPrice() external view returns (uint256);
    function points(address user) external view returns (uint256);
}
```

## Examples

### Access Control

```solidity
import { ThurinSBT } from "@thurinlabs/contracts/interfaces/ThurinSBT.sol";

contract GatedContent {
    mapping(address => bool) public hasAccess;

    function unlockContent() external {
        require(ThurinSBT.isValid(msg.sender), "Verify at app.thurin.id");
        hasAccess[msg.sender] = true;
    }

    function viewContent() external view returns (string memory) {
        require(hasAccess[msg.sender], "Unlock first");
        return "Secret content";
    }
}
```

### Modifier Pattern

```solidity
import { ThurinSBT } from "@thurinlabs/contracts/interfaces/ThurinSBT.sol";

contract MyDapp {
    modifier onlyVerified() {
        require(ThurinSBT.isValid(msg.sender), "Not verified");
        _;
    }

    function protectedAction() external onlyVerified {
        // Only verified humans can call this
    }
}
```

### Check Expiry

```solidity
import { ThurinSBT } from "@thurinlabs/contracts/interfaces/ThurinSBT.sol";

contract MyDapp {
    function timeUntilExpiry(address user) external view returns (uint256) {
        uint256 expiry = ThurinSBT.getExpiry(user);
        if (expiry == 0 || expiry < block.timestamp) {
            return 0;
        }
        return expiry - block.timestamp;
    }
}
```

## Contract Addresses

Thurin SBTs live on Ethereum mainnet. Query mainnet from any chain.

| Chain | ThurinSBT Address |
|-------|-------------------|
| Ethereum Mainnet | TBD |
| Sepolia (testnet) | `0x03812ef2AEF6666c14ce23EfDbF55bd4662BFbDf` |

## Gas Estimates

| Operation | Gas |
|-----------|-----|
| `isValid()` | ~3,000 |
| `getExpiry()` | ~3,000 |
| `getMintPrice()` | ~3,000 |
| `points()` | ~3,000 |
