# Onchain POAPs Studio

A **zero-dependency, single-file** frontend for the [OnchainPOAPs](https://github.com/jvaleskadevs/onchain-poaps) ERC-1155 contract — works as a standalone website **and** as a Farcaster Mini App.

**Live app:** https://kordireal.github.io/onchain-poaps-studio/
**Contract (Base Sepolia):** [`0xC3249356a483fbe17d5355D39105D2eA666d9de6`](https://sepolia.basescan.org/address/0xC3249356a483fbe17d5355D39105D2eA666d9de6)

## What it does

| Area | Features |
|---|---|
| **Create** | Register a POAP with full `registerEvent()` support: name, description, event date, location, external URL, soulbound/public flags, optional allowlist root. Built-in SVG optimizer + live gas estimate + preview. |
| **Mint** | Public mint · Merkle allowlist mint (paste the creator's proofs JSON *or* the raw address list — the tree is rebuilt locally and your proof is found automatically) · signature mint (paste or open a signed link). |
| **Manage** (creator) | Allowlist builder (tree preview → set root onchain → download proofs JSON), public mint open/close, batch mint to ≤101 recipients, **Signature Studio** (gasless `personal_sign` authorizations → signed links → printable QR sheet). |
| **Gallery** | All events, read straight from the chain via `eth_call`; artwork and metadata are decoded from the fully-onchain base64 `uri()`; "owned" badges via `balanceOf`. |
| **Verify** | Pure onchain audit of any event: metadata, creator, CAIP-2 multichain ID, claim state. |
| **Docs** | In-app documentation covering every mechanism, deadline and restriction. |

Everything is implemented from scratch in the single `index.html` — **no npm, no CDN, no backend, no indexer**:

- keccak-256 (FIPS-202)
- ABI encoder/decoder for the contract's types
- OpenZeppelin-compatible Merkle tree builder (sorted-pair, `keccak256(packed(address))` leaves)
- QR encoder (MIT-licensed `qrcode-generator` by Kazuhiko Arase, inlined)
- SVG minifier (SVGO-style cleanup for cheap SSTORE2 registration)
- Farcaster Mini App support (manifest at `/.well-known/farcaster.json`, SDK `ready()` handshake when embedded)

## Run it

Static file — no build step:

```bash
# any static server works, or just open index.html
python3 -m http.server 8000
# → http://localhost:8000
```

Deployed on GitHub Pages; the Mini App manifest is served at
`/.well-known/farcaster.json`.

## Configuration

The app targets the Base Sepolia deployment by default. To point it at another
deployment of the same contract:

- use the **Verify** tab's contract override (persisted in `localStorage`), or
- edit `CONTRACT_DEFAULT` in `index.html`.

Chains: Base Sepolia (84532) is preconfigured; Base (8453) and Ethereum (1) are
built in — the header pill switches networks via `wallet_switchEthereumChain`.

## Security notes

- No private keys, seeds or secrets are handled. Signing uses the injected
  wallet (`personal_sign` for signature-mint authorizations — free, gasless).
- Allowlist proofs are computed locally; nothing about your list leaves the page.
- All writes are explicit wallet transactions the user confirms.

## License

MIT — see [LICENSE](LICENSE).
