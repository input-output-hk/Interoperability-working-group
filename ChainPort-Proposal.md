# Chainport Proposal

Chainport (https://www.chainport.io/) is working on a Cardano bridge and they are planning to Go-Live on the20th of October.

They’ve just finished an 8 weeks long audit by Certik of the contracts+backend of this integration, but they actually caught issues that Certik didn't. Therefore some additional pair of eyes on this would be very beneficial.

**More details on Chainport audits:**

1. we just finished an 8 engineering week audit by TrailOfBits, and
2. a similar length audit by certik. The Certik one was fully focused on the Cardano integration.

### Initial feedback from WG

Some additional documentation would be good.

User went through their public documentation which seem rather lacking on technical details about how the bridge actually works (or maybe user looked in the wrong places).

Most of the messaging is focused around how the extra tokens are held https://docs.chainport.io/chainport/security, which doesn't say much about how the contracts work (mpcs? and nodes).

Their roadmap seems a bit out of date https://docs.chainport.io/chainport/road-map as it ends at q2 2022 with cardano support listed for q2.
**Chainport roadmap** this can be found at this end of this page chainport.io

Did certik do the cardano side of audits, or that was for the ETH side still?

### More comments from Chainport

* ChainPort is a full service SaaS product, and will be available to all users to port from all our supported chains into Cardano (and back) once we hit production in a week or so.
* Regarding testing env, it’s been running on test env since July, then the fork on testnet broke everything, then we reconnected it after everyone got things working again on all sides.
* Staging is currently being deployed. Once it passes our regression on staging you’ll be invited to play with it as well on staging.
* Regarding architecture etc.. the bridge is a centralized custodian level bridge, and we don’t publicly discuss architecture for security reasons. But we do have internal documentation for auditors we can probably share with you depending on your specific needs.
* Regarding decentralization, we don’t believe this is currently a sound approach, and basically all of the decentralized bridging protocols have been hacked as part of a de-facto battle-testing on the backs of users, and we are looking to be more careful with user funds.
* We do have a lot of security mechanisms built in, and we are working to distribute and decentralize the protocol, but it will be a very gradual approach.
* The next module to be distributed further is what we call the bridge balance monitor, this is a process which counts deposits vs mints on all networks, and is capable to freeze both minting and the bridge itself in case of discrepancies. We will work in the nearest future to enable other parties to run this as well to help decentralize the security of the bridge.
* As for cross-chain messaging in a more decentralized manner, we have POC’d a Starknet based messaging system, but it will still need more battle-testing and decentralization on their side before it will be able to offer better security and acceptable performance.
