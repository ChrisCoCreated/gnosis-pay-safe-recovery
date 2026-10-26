# Gnosis Pay Safe Recovery Tool (Zodiac Delay Bypass)

A standalone, privacy-preserving recovery interface for users with funds stuck in legacy Gnosis Pay Safes on Gnosis Chain.

## The Problem

Legacy Gnosis Pay accounts often hit a dead end due to two overlapping issues:
1. **Unannounced Token Migration:** Monerium migrated the GBPe token contract from its legacy address (`0x5cb9...`) to the current contract (`0x8E34bfEC4f6Eb781f9743D9b4af99CD23F9b7053`). Official web apps checking the old contract display a 0 balance and disable withdrawals.
2. **Sunsetted Relayers (HTTP 404):** Official frontends rely on backend relayers for gasless submission (`/relayer/v1/enqueue`). When these endpoints are deprecated, transactions fail silently or throw JSON parse errors.

## The Solution: Direct Zodiac Delay Execution

Gnosis Pay Safes use **Zodiac Delay Modifiers** (deployed as EIP-1167 minimal proxies). In these deployments, the user EOA is registered directly as an authorized module on the Delay contract.

No relayer, API, or EIP-712 signature collection is required. Recovery uses a native 2-step flow:
1. **Step 1 (Enqueue):** Calls `execTransactionFromModule` to queue the ERC-20 transfer.
2. **Cooldown:** Contract enforces a mandatory on-chain wait (180 seconds).
3. **Step 2 (Execute):** Calls `executeNextTx` to trigger the Safe to release tokens directly to your wallet.

## How to Use

1. Serve the directory locally:
   npx serve .
2. Open http://localhost:3000 in your Web3 browser (Rainbow, MetaMask).
3. Connect your wallet (set network to Gnosis Chain, chainId 100).
4. Enter your Safe address and click "Detect Modules & Balances".
5. Run Step 1 (Enqueue), wait out the 180s timer, then run Step 2 (Execute).

## Token Addresses (Gnosis Chain)

- GBPe (Current): 0x8E34bfEC4f6Eb781f9743D9b4af99CD23F9b7053
- GBPe (Legacy): 0x5cb907e5b85a1147a21665a39eb874d1c10712d9
- EURe: 0xcB444e90D8198415266c6a2724b7900fb12FC56E

## License
MIT
