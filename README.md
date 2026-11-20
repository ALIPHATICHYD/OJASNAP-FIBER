# OjaSnap Fiber

AI captions for student vendors, where each caption is generated, held, and only released once a small [Fiber](https://github.com/nervosnetwork/fiber) payment for it settles.

**Status:** proposal stage for the Nervos CKB Spark Program. Testnet only. Week 0 notes are in [`notes/FIBER_NOTES.md`](notes/FIBER_NOTES.md).

- Spark proposal: [link to the Nervos Talk post, once posted]
- OjaSnap (the existing app this builds on): https://github.com/ALIPHATICHYD/ojasnap

## What this repo will contain

| Folder | What goes there |
| --- | --- |
| `notes/` | `FIBER_NOTES.md`: what I tried on Fiber, what broke, and links to upstream issues |
| `survey/` | Anonymised summary of the 17-vendor survey |
| `server/` | Payment-gated caption release (generate, hold, invoice, release on payment) |
| `web/` | Browser client using the official fiber-js node |
| `logs/` | Published pilot logs (hashed vendor IDs only) |
| `report/` | Field report and the script that rebuilds every number from `logs/` |

## How it works (planned)

1. A vendor uploads a product photo.
2. OjaSnap generates captions with Gemma and holds them on the server.
3. The server creates a Fiber invoice for that generation.
4. The vendor's browser pays it over a Fiber channel.
5. The captions are released only once the payment settles. Failed generations are never invoiced.

## Out of scope

Mainnet, real money, stablecoins, mobile apps, and business work.

## License

MIT
