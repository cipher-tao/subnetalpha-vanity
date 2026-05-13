# SubnetAlpha Vanity Generator

A free, 100% client-side tool to generate Bittensor (TAO) vanity addresses.

🌐 **Live:** [subnetalpha.org/vanity](https://subnetalpha.org/vanity)
🤖 **Trade with:** [@SubnetAlphaBot](https://t.me/SubnetAlphaBot) on Telegram

## What it does

Generates a Bittensor wallet (SS58 prefix 42, sr25519) whose address matches a custom pattern — at the start, end, or anywhere inside.

Want your address to start with `5ALPHA...`? `5BITTENSOR...` somewhere in the middle? Done.

## Security model

- **100% client-side.** Every line of cryptographic work happens inside your browser. There is no server, no API, no logging.
- **Your mnemonic NEVER leaves the page.** There is no network code that transmits secrets. You can verify this by opening DevTools → Network and confirming no requests are made during search.
- **Open source.** The entire `vanity_tao.html` file is here in this repo. It is identical to what is served at `subnetalpha.org/vanity` (you can diff via View Source).
- **Offline mode.** For high-value addresses, disconnect from the internet before clicking Start. The trust badge will turn green to confirm. The generator works fully offline once the page is loaded.

## How it works (technical)

1. Generates a random 24-word (or 12-word) BIP39 mnemonic.
2. Derives the mini-secret via PBKDF2-SHA512 (2048 rounds).
3. Derives the sr25519 keypair.
4. Encodes the public key as SS58 with prefix 42.
5. Checks if the address matches your pattern. If not, loop.

The crypto stack uses `@polkadot/util-crypto`, the same library that powers `btcli`. Verification: the address you find will be identical to what `btcli wallet regen_coldkey --mnemonic "..."` produces.

## Difficulty

Realistic time-to-find on a modern laptop with 8 CPU cores (mnemonic mode, ~8000 attempts/sec):

| Pattern length (effective) | Expected time |
|---|---|
| 3 chars | < 1 minute |
| 4 chars | ~10–30 min |
| 5 chars | ~3–10 hours |
| 6 chars | days–weeks |
| 7+ chars | needs a Rust/GPU implementation |

Note: addresses for prefix 42 always start with `5`, so the leading `5` is "free" — it doesn't count toward difficulty.

## Running locally

git clone https://github.com/cipher-tao/subnetalpha-vanity.git
cd subnetalpha-vanity
python3 -m http.server 8000
# Open http://localhost:8000/vanity_tao.html

Or just open `vanity_tao.html` directly in a browser — but note: opening via `file://` may cause Web Workers to fail. The tool will automatically fall back to single-threaded main-thread mode if that happens (slower but works).

## Importing the generated wallet

# Coldkey (main wallet)
btcli wallet regen_coldkey --wallet.name <name> --mnemonic "word1 word2 ... word24"

# Hotkey (for mining / staking)
btcli wallet regen_hotkey --wallet.name <name> --wallet.hotkey <hk> --mnemonic "word1 word2 ... word24"

Always test with a small transfer first.

## License

MIT. See [LICENSE](LICENSE).

## Disclaimer

This tool is provided as-is with no warranty. The author is not responsible for lost funds, leaked seeds via your local environment (malicious extensions, compromised OS, screen capture, clipboard hijackers, etc.), or any other loss. **Use at your own risk.**

## Credits

Built by [@cipher_tao](https://x.com/cipher_tao) ([SubnetAlpha](https://subnetalpha.org)).

Crypto stack: [@polkadot/util-crypto](https://github.com/polkadot-js/common).
