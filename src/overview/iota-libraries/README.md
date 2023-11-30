# 📚 IOTA libraries

---

How should one approach IOTA's Rust libraries? What sources of information are available? How can one discover the information needed for their work?

> **Note from 30/11/2023** : Please note that the **iota.rs and wallet.rs libraries have been deprecated**. The recommended approach for development is now using the new **IOTA SDK**, which consolidates iota.rs and wallet.rs into a single library.
>
> It's important to be aware that, despite this being the preferred choice, the tutorial primarily utilizes the older libraries in most chapters. This decision is based on the fact that the tutorial and accompanying videos were initially created using the deprecated libraries.
>
> While the workflow remains unchanged, there are slight differences in dependencies within Cargo.toml and the Rust backend code. To maintain consistency, the tutorial retains the use of the older libraries. However, an additional chapter will be included for the Playground App, where the Rust code based on the IOTA SDK will be provided.

---

To approach IOTA's Rust libraries, there are several sources of information available to help you understand and work with them effectively:

**Official Wiki Documentation**: Start by referring to the official documentation provided by IOTA. It typically includes guides, tutorials, and API references specific to the Rust libraries. The documentation will give you an overview of the available functionality and important concepts to consider. You will also find some ...

**... Examples**: Look for examples that demonstrate the usage of IOTA's Rust libraries. These resources can provide hands-on guidance and practical insights into integrating the libraries into your own projects. I will use some of them in the later sections where I build the "Playground" app.

<a href="https://wiki.iota.org/shimmer/iota.rs/welcome/" target="_blank">👉 &nbsp; Wiki - IOTA's Client Library</a>

<a href="https://wiki.iota.org/shimmer/wallet.rs/welcome/" target="_blank">👉 &nbsp; Wiki - IOTA's Wallet Library</a>

<a href="https://wiki.iota.org/shimmer/identity.rs/introduction/" target="_blank">👉 &nbsp; Wiki - IOTA's Identity Framework Guide</a>

**GitHub Repositories**: Visit the GitHub repositories for the IOTA Rust libraries. It serves as the central hub for code ("single source of truth"), issue tracking, and community discussions. Explore the repositories to access the source code, documentation files, and discussions related to the libraries. You can also open issues or participate in discussions to seek clarification or contribute to the project.

<a href="https://github.com/iotaledger/iota.rs" target="_blank">👉 &nbsp; GitHub - iota.rs</a>

<a href="https://github.com/iotaledger/wallet.rs" target="_blank">👉 &nbsp; GitHub - wallet.rs</a>

<a href="https://github.com/iotaledger/identity.rs" target="_blank">👉 &nbsp; GitHub - identity.rs</a>

**Communication Channels**: Stay connected with the IOTA community through official communication channels like the IOTA Discord server, where you can interact with developers, ask questions, and receive support. The Discord server is often a great place to connect with fellow developers and learn from their experiences.

---

In the following sections I will provide you with some additional starter information:

- [What is the structure of iota.rs, wallet.rs, and identity.rs?](iota-wallet-identity.md)
- [Which library versions are the right ones for which purpose?](library-versions.md)
