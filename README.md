Typhon: trustless Bitcoin sidechain protocol
===

Typhon is Bitcoin second-layer technology – the same kind as Lightning network. You can think of it as of massively-scalable multiparty payment channels. The protocol defines the process of sidechain formation and updates that require commits to the main Bitcoin blockchain. It is censorship-resistant, permissionless and agnostic to the particular consensus and blockchain formation protocol used by particular sidechain implementations. 

Name Typhon comes from Greek mythology, it's a kind of multi-coiled snake or dragon that is able to bring storms:  "multiple coils" serve as an analogy to multiple sidechains, while "storms" are the analogy for supreme qualities of this 2nd layer technology as compared to simple payment channels.

*Lightning ⚡️ brings thunderbolt 🌩 – Typhon 🐉 brings typhoon🌪*

Motivation
---

*TBD. Briefly: Lightning has some limitations, existing sidechain 2-way peg technologies requires soft or hardforks, and currently can operate only in trusted mode as federation. We need more scalable 2-nd layer technologies without these limitations.*

General Design
---

Typhon is a **non-2-way peg** trustless sidechain solution that can be implemented using existing Bitcoin Script functionality, i.e. without any soft- or hardforks.

### Design Principles
+ **Censorship resistance**: the protocol must be permissionless and allow at least the same level of censorship resistance as the main Bitcoin PoW chain
+ **Native to Bitcoin**: the protocol should allow fast Bitcoin transfers to and from sidechain
+ **Nothing more then Bitcoin**: to operate the protocol should not require any new/special tokens or coins. Economical incentives can be commission/fee based and are not part of this specification
+ **Conseusus agnosticism**: the protocol should not depend on a particular consensus and blockchain-formation protocols used in sidechain
+ **High Byzantine tolerance**: the protocol should tolerate up to 50%-1 Byzantine faults
+ **Off-chain**: most of the protocol communications should be performed outside of Bitcoin blockchain

### Core Assumptions
+ Elliptic curve discrete logarithm problem (ECDLP) is a NP-problem having no feasible deterministic solution.
+ The sidechains are operated by a **honest majority** (i.e. the most of the block-producing nodes are non-Byzantine).
+ The sidechains run any kind of 50%+1 Byzantine fault-tolerant consensus and blockchain formation protocol.
+ A sidechain consensus must have a concept of **epochs**, within which the state of the sidechain reaches finality. 
+ Each epoch must have a predictable duration known before the start of the epoch, i.e. there should be an ability to deterministically compute the time of the next final state for the sidechain before the epoch begins.

### Workflow

Any can commit to participate in a Typhon-based sidechain by creating and adding a special **sidechain commit transaction** (or simply **commitment transaction**) to the Bitcoin blockchain that locks some pre-defined per-sidechain stake in Bitcoins that secures the sidechain from Byzantine faults. This commitment is time-limited (the transaction is a very special form of CLTV-transaction) and must be longer than the duration of two next *epochs* of the sidechain.

To construct the *commitment transaction* committer needs to participate a special distributed shared secret protocol named **Apophis**, which leads to the creation of public-private ECDSA key pair in such a way that the public key is known to all of the participants while the private key can be revealed only with a threshold signature from the honest majority of the *Apophis* protocol (which later becomes a honest majority of the sidechain epoch).

The sidechain is blockchain-based replicating state machine with a shared global state. Its state can be updated within epoch by the **leader** of particular sidechain protocol such that each of the leaders should produce some amount of blocks strictly proportional to the stake locked in the *commitment transaction*. By the end of the epoch the sidechain leaders must reach finality in a consensus on the global sidechain state. Typhon protocol leaves other details of the sidechain implementation flexible and up to particular sidechain design.

At the end of each epoch the stakes of the *leaders* that have being found Byzantine-faulty by the sidechain *honest majority* are **slashed** to the main chain miner that will include the **slashing transaction** to the bitcoin blockchain while the rest of the *leaders* will be able to unlock their funds at the end of the next epoch.

Main chain bitcoins can be moved to and from the sidechain in a quick manner using atomic swap contracts between the main chain and sidechain participants.

Protocol Details
---

### Apophis protocol

### Commiting to a sidechain

### Running sidechain epoch

### Handling sidechain Byzantine faults

### Moving bitcoins to and from sidechain

