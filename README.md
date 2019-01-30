Typhon: trustless Bitcoin sidechain protocol
===

*Dr Maxim Orlovsky, [BICA Labs](http://bicalabs.org) & [Pandora Project](https://manifesto.ai)*

**NB: This protocol draft is the work in progress. Feel free to propose PR's and discuss using GitHub issues**

Typhon is Bitcoin second-layer technology – the same kind as Lightning network. You can think of it as of massively-scalable multiparty payment channels. The protocol defines the process of sidechain formation and operation on top of the main Bitcoin blockchain. It is censorship-resistant, permissionless and agnostic to the particular consensus and blockchain formation protocol used by sidechain implementations. 

Name Typhon comes from Greek mythology, it's a kind of multi-coiled snake or dragon that is able to bring storms:  "multiple coils" serve as an analogy to multiple sidechains, while "storms" are the analogy for supreme qualities of this 2nd layer technology as compared to simple payment channels.

*Lightning⚡️ brings thunderbolt🌩 – Typhon🐉 brings typhoon🌪*

Motivation / Cover Letter
---

*TL;DR: Lightning has some limitations, existing sidechain 2-way peg technologies requires soft or hardforks, and currently can operate only in trusted mode as federation. We need more scalable 2-nd layer technologies without these limitations.*

The idea for the protocol originated from my research on the lack of deterministic finality in PoW consensus which makes very hard to design trustless sidechains without decreasing the security of the main chain with a new soft- or hard-forks. At the same time the absence of a finality property in PoW consensus is an important trade-off decision allowing to run the main chain in censorship-resistant manner, so we can't do anything with it.

So why do we need sidechains at all if we have the Lightning network? A lot of reasons; one of them is that there is a class of potential bitcoin applications that require global state (which Lightning technology obviously does not have) – like [proof of computing work protocol](https://www.slideshare.net/pandoraboxchain/proof-of-computing-work-protocol-by-pandora-boxchain) for scaling high-load computing in censorship-resistant way previously designed by my team. Such solutions can’t be put onto the main PoW chain because of its scalability problems. But what if, instead of 2-way peg sidechains (which with the current bitcoin protocol version can be only federated, i.e. not censorship-resistant), we use the same technology to take bitcoins off the main chain like in the Lightning network, but instead of bringing them into the "payment channel" we will leave them locked ("locked stake") with some CLTV/HTLC-kind of a contract? You can think of it as a "Lightning sidechain" – or, simply, typhoon (something much larger than just a lightning). Yes, the lightning itself does not need a "chain" (since it does not need a global state), but as I told earlier, many other apps on top of bitcoin will need such thing.

Such sidechains can run any non-PoW consensus which (a) would not produce inflation, operating only on commissions, (b) will take "block producers" from that HTLC-like commitment transactions on the main bitcoin PoW chain, which will guarantee censorship resistance even if the sidechain protocol is not censorship-resistant by itself (kind of "PoW externality for censorship-resistance"); it can be used to build lightning channels on top of it – i.e. be a kind of generic sidechain protocol without drawbacks of existing 2-way peg type sidechains. As for bitcoin itself, it will render bitcoin as a platform for many kinds of applications and use cases it can’t be used today, increasing its adoption outside of SoV/MoE + micropayments paradigms in such domains as high load computing, data synchronisation etc – everything that requires global state that can't fit into the 1st layer or be held in existing 2-nd layer stack in a trustless manner.

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

### Differences to Existing 2nd Layer Technologies
+ The protocol does not require to lock the funds in order to transfer them off-chain, instead it relies on atomic swapps with the main chain for liquidity provision
+ The funds locked in the *commitment transactions* are just time-locked and can't be used off-chain like with  2-way pegs or Lightning payment channel HTLC-contracts.
+ No other party is required to become participant of the Typhon sidechain, while Lightning protocol requires to find the other party for payment channel setup and 2-way-pegged sidechain requires federation for the multisig peg contract.
+ Quick on-chain settlement/finalization, while the closing of Lightning channels or releasing funds from the peg takes much more time.
+ No complex multisig or multiparty unsigned contracts required: each participants enters and leaves the Typhon sidechain with a much simple P2WSH/P2SH transaction with only one output. The commitment transaction occupies much smaller on-chain size than multisig transactions from sidechain federation or Lightning payment channel.

Protocol Details
---

### Apophis protocol

*TBD*

The protocol results in creation and revealing of ECDSA public keys `P(i)` specific to each party `i` and the creation of corresponding party-specific unknown private keys `x(i)`. Private keys are unknown because they are kept in distributed way by all protocol participants in a form of shared secrets under threshold signatures algorithm so each of them can't be revealed without agreement from the *honest majority* of the participants.

*Apophis is another snake from Greek (and initially Egyptian) mythology, a predecessor of the Typhon. It hides in a dark abyss in the underworld, like the private keys from the protocol above.*

### Commiting to a sidechain

**Commitment transaction**

*Commitment transaction* is published by a *commiter* – the party interested in joining the Typhoon consensus
participants.

Let `A` be a un-tweaked public key of the *committer* and `H(A)` the `RIPMD160` hash value derived from it. Then the `ScriptPubKey` of the commitment transaction should contain the following script:

```
// This is one of the options for the commiter to specify sidechain id so the transaction can be 
// attributed to the specific sidechain
<SidechainID>
OP_DROP

// Now at the top of the stack we have content provided by ScriptSig unlocking transaciton, so we
// proceed with CLTV-enhanced P2PKH scripts
OP_DUP
OP_HASH160

// Branch used by the commiter if there were no Byzantine fault discovered by the honest majority.
// This is normal P2PKH transaction enhanced with CLTV script to enable suffiicient time until
// the epoch was finalized + another epoch for the honest majority to reach the agreement that
// there were no Byzantine faults
IF
    <H(A)> 
    OP_EQUALVERIFY
    <Time-for-two-epochs> 

// Branch used by the honest community if it have agreed upon Byzantine fault of the committer
// In such case they reveal the hidden private key x_i corresponding to the public key P_i
// by running reveal stage of the threshold secret sharing protocol
ELSE
    <H(P_i)>
    OP_EQUALVERIFY
    <Time-for-one-epoch + some additional time> 

ENDIF

// At top of the stack we have lock time value, so let's check it
OP_CHECKLOCKTIMEVERIFY
OP_DROP

// In both cases we still need to check that the spending transaction is signed with the proper
// private key
OP_CHECKSIG
```

This script reveals no more private information about the commiter or any other party participating sidechain than a normal P2PKH transaction. In fact it is composed of two P2PKH branches enhanced with CLTV part.

**Unlocking transactions**

Let `ECDSA(*)` be a signature with some private key `*`. According to the notation from the previous sections, `x_i` is the private key that can be only discovered by the *hones majority* in case they can reach the agreement that the *commiter* (party `i`) had performed a Byzantine fault within the epoch time scope corresponding to the original *commitment transaction*. `P_i` is the public key of the committer revealed as a result of the *Apophis* protocol; `y` and `A` are the normal private and public keys of the committer.

Once the `x_i` becomes revealed any participant of the honest majority can construct and publish *slashing transaction* spending UTXO from the *commitment transaction* to the output that can be used by slashing transaction originator. This transaction will containt the following `SigScript`: `<ECDSA(x_i)> <P_i>`.

This script will become valid only after the CLTV time from the second branch of the *commitment transaction* will pass, so other participants of the *honest majority* have an opportunity to publish their versions with the same unlocking script, but spending the locked amount to different UTXOs, but with a higher miner fee. This will lead to the fee race, effectively resulting in Nash equilibrium when practically all of the locked amount is spent for the mining fee, i.e. the money will be transferred to the miner who will include the slashing transactions into the blockchain, guaranteeing fast and efficient slashing before the other CLTV lock will expire. This also keeps economic incentives of the honest majority intact: they win nothing by cooperating against other participants, so the Nash equilibrium for the sidechain consensus protocol is not distorted.

If there were no withessed Byzantine fault from the committer, he will be able to unlock its funds at the end of the ceond epoch with the usual `SigScript`: `<ECDSA(y)> <A>` – and nobody else will be able to spend the UTXO of the *commitment transaction*.

### Running sidechain epoch

*TBD*

### Handling sidechain Byzantine faults

*TBD*

### Moving bitcoins to and from sidechain

*TBD*
