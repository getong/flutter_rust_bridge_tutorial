# Motivation

---

So, why do we even need this chapter, you ask? Well, buckle up, my advanced coding compadres!

---

I've decided to skip the nitty-gritty details this time around. No more elaborate tales of how and why I configure things the way I do. We're sticking to the essentials. If you're craving more info, do yourself a favor and flip through the previous chapters.

Now, why the IOTA SDK, you wonder? Picture this: I couldn't get rocksdb in the wallet.rs to play nice with iOS. Enter IOTA SDK to save the day!

<a href="https://github.com/iotaledger/iota-sdk" target="_blank">👉 &nbsp; Github - IOTA SDK</a>

<a href="https://wiki.iota.org/iota-sdk/welcome/" target="_blank">👉 &nbsp; Wiki - IOTA SDK</a>

And guess what? The IOTA SDK isn't just a random choice; it's the successor to the now-deprecated duo of iota.rs and wallet.rs. Team it up with identity.rs, and you've got yourself the future of Shimmer/IOTA libraries. Future-proofing, baby!

Quick observation: whipping up a wallet and accounts with the IOTA SDK takes way more time than the breezy wallet.rs. The mystery behind this? Still working on cracking that code.

Oh, and I did a bit of code spring cleaning. Pulled out all the wallet-related functions from the behemoth api.rs and corralled them into a cozy wallet_singleton module. The plan? One wallet object to rule them all. Don't call me out if this isn't your standard Rust wizardry; I'm just doing my best here.

Now, the next two chapters are your Android and iOS love stories. I'm walking you through building the entire shebang from scratch and tweaking all configurations for both platforms. These chapters are joined at the hip, so start with Android and then waltz into iOS territory.

---

Hope that captures the vibe you're aiming for!
