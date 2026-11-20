# Fiber notes

What I tried on Fiber, how long it took, and what broke. Anything that looks like a Fiber bug gets filed upstream and linked here.

## Week 0: first testnet payment (2026-11-20)

Setup: Intel Mac, fnn v0.10.1 (`d9b6bbd`, x86_64 darwin portable), ckb-cli v2.0.0. Two local nodes: A = vendor/payer (RPC 8227), B = OjaSnap/receiver (RPC 8238 P2P, 8237 RPC). Started from the [Public Nodes manual](https://github.com/nervosnetwork/fiber/blob/main/docs/public-nodes.md).

Rough time: about 1.5 hours end to end on the first try, including setup mistakes (13:00 to 17:47 Lagos with breaks).

| Step | Result | Notes / errors |
| --- | --- | --- |
| Download fnn and ckb-cli | Done | Safari auto-extracted the archives (`.tar.gz` became `.tar`, the ckb-cli zip became a folder). macOS Gatekeeper blocked the binaries until `xattr -d com.apple.quarantine`. |
| Create keys | Done | Two ckb-cli accounts, private key exported into each node's `ckb/key`. |
| Fund from faucet | Done | faucet.nervos.org gave 10,000 test CKB to each address. |
| Start nodes | Done | Both nodes came up and connected to 2 bootnodes each. |
| Open channel A to node1 / B to node2 (as in the manual) | **Failed** | `Peer not found`. The manual's testnet node1 (`02b6d4e3...`) and node2 (`0291a657...`) were not in the network graph (35 nodes) for fnn v0.10.1. |
| Open channel A to onyxia bootnode (`0313dcf9...`) | ChannelReady | 499 CKB, single-funded: local 399, remote 0 (99 CKB reserve). |
| Open channel B to thrall bootnode (`024714ca...`) | ChannelReady | Same: local 399, remote 0. |
| Pay A to B through the relays | Not attempted | Relays put no balance on their side, so B had no inbound liquidity from thrall. |
| Open direct channel A to B | ChannelReady | Connected by multiaddr `/ip4/127.0.0.1/tcp/8238/p2p/<B peer id>`. |
| 1 CKB payment A to B | **Success** | First attempt failed with `no path found` because I sent it before the channel was ready. Second invoice paid. |
| Close channels | Not yet | |

**Evidence**

- Channel funding tx, A to onyxia: [`0xecea166f0eb57d95267b46121dfff75fee55b6a9eef5848d04993b56c876dd22`](https://testnet.explorer.nervos.org/transaction/0xecea166f0eb57d95267b46121dfff75fee55b6a9eef5848d04993b56c876dd22)
- Channel funding tx, B to thrall: [`0xeef057c0d0c8c12881765331a2baf7b8552f32c9af32d2042006c23855393cc7`](https://testnet.explorer.nervos.org/transaction/0xeef057c0d0c8c12881765331a2baf7b8552f32c9af32d2042006c23855393cc7)
- Channel funding tx, A to B: [`0xc5bd20ab5b0095f87f772ecdcda56131c62646b74a7b059723eb43030eb87017`](https://testnet.explorer.nervos.org/transaction/0xc5bd20ab5b0095f87f772ecdcda56131c62646b74a7b059723eb43030eb87017)
- Successful payment hash: `0x58a2c64ac4a36e1f8856b02b90c546dcfef5925334c156718bbb42f17c7d6f14` (1 CKB, invoice description "OjaSnap test caption", status `Success`, fee 0)
- Settlement time: 67 ms between payment `created_at` and `last_updated_at` (2026-11-20 16:46:25 UTC)
- Balances A to B channel: before local 399 / remote 0, after local 398 / remote 1
- Terminal screenshot of the whole run, ending in `Success`: [week0-payment-success.png](evidence/week0-payment-success.png)
- Failed attempt (kept for honesty): `0x542670e37aebb71914ebf8619ca5f40d8cfdcfab813fff5629374c0cb812d8dc`, `no path found`, sent before ChannelReady

**What this means for OjaSnap**

- A payment-gated caption needs the vendor to have outbound liquidity and OjaSnap to have inbound liquidity. Single-funded channels to public relays do not give a receiver inbound liquidity, so the pilot design must plan for it (e.g. OjaSnap opens channels to vendors, or a relay that dual-funds).
- `get_payment` returning `Success` with a `payment_preimage` is the signal OjaSnap can use to release a caption.

**What broke or confused me**

- When I started setting up, the testnet relay nodes in the Public Nodes manual (node1 02b6d4e3..., node2 0291a657...) weren't on the network with fnn v0.10.1. connect_peer kept returning "Peer not found". I listed the network graph myself and ended up opening channels to the onyxia and thrall bootnodes instead.
- The relays accepted my channels but put 0 CKB on their side, so B had no inbound liquidity and A couldn't pay B through them. I only understood this after reading the balances. I opened a direct A to B channel to get a working payment. For OjaSnap, this means I have to plan who funds the receiving side.
- I sent my first payment before the A to B channel reached ChannelReady and got "no path found". That payment is marked Failed for good, so I had to create a new invoice.
- On macOS, Safari auto-extracted the downloads (.tar.gz became .tar, and the zip became a folder), and Gatekeeper blocked fnn and ckb-cli until I removed the quarantine flag.
- As someone new to CKB: amounts are in hex shannons (0xb9e459300 = 499 CKB), and each side keeps a 99 CKB reserve, which is why 499 funded showed as 399 usable.

**Upstream issues filed**

- [ ] Public Nodes manual: testnet node1/node2 pubkeys not reachable with fnn v0.10.1 (check whether an issue already exists before filing)
