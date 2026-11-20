# Fiber notes

What I tried on Fiber, how long it took, and what broke. Anything that looks like a Fiber bug gets filed upstream and linked here.

## Week 0: first testnet payment ([date])

Setup: [Intel Mac, macOS version], fnn [version], ckb-cli [version]. Two local nodes (A = vendor/payer, B = OjaSnap/receiver) connected through the public testnet nodes, following the [Public Nodes manual](https://github.com/nervosnetwork/fiber/blob/develop/docs/public-nodes.md).

| Step | Time taken | Notes / errors |
| --- | --- | --- |
| Download fnn and ckb-cli | | |
| Create keys | | |
| Fund from faucet | | |
| Start nodes | | |
| Open channel A to node1 | | |
| Open channel B to node2 | | |
| 3 payments A to B (1 CKB each) | | |
| Close channels | | |

**Evidence**

- Channel funding tx (A): [hash, testnet explorer link]
- Channel funding tx (B): [hash, testnet explorer link]
- Payment hashes: [1], [2], [3]
- Balances before and after: [A], [B]; total relay fees: [amount]

**What broke or confused me**

- [ ]

**Upstream issues filed**

- [ ]
