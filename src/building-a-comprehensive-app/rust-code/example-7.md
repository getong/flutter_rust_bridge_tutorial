# Example 7

---

Rust adjustments for Example "Create Decentralized Identifier".

---

## What adjustments do I need to make in Rust?

In summary, here are the steps you need to take to create the API function.

### Cargo.toml

Add this crate to the existing dependencies:

```toml
[dependencies]
identity_iota = { version = "0.7.0-alpha.5" }
```

Here, I've employed iota_identity's v0.7.0-alpha.5 release. If you intend to utilize a different version, kindly refer to the documentation for information on the paths used, and adapt them in the sample code provided below.

<a href="https://docs.rs/identity_iota/0.7.0-alpha.5/identity_iota/" target="_blank">👉 &nbsp; Crate iota_identity (v0.7.0-alpha.5)</a>

### api.rs - Used Paths

Add these two paths for _Address_ and _AliasOutput_ here:

```rust,ignore
use iota_wallet::{
    account_manager::AccountManager,
    iota_client::block::address::Address,    // <- Add this for Identity-Example
    iota_client::block::output::AliasOutput, // <- Add this for Identity-Example
    iota_client::constants::SHIMMER_COIN_TYPE,
    iota_client::request_funds_from_faucet,
    iota_client::Client,
    secret::stronghold::StrongholdSecretManager as WalletStrongholdSecretManager,
    secret::SecretManager as WalletSecretManager,
    ClientOptions,
};
```

And add this whole section:

```rust,ignore
use identity_iota::{
    crypto::KeyPair,
    crypto::KeyType,
    iota::IotaClientExt,
    iota::IotaDocument,
    iota::IotaIdentityClientExt,
    iota::NetworkName,
    verification::MethodScope,
    verification::VerificationMethod,
};
```

### api.rs - Function _create_decentralized_identifier()_

```rust,ignore
#[allow(dead_code)]
pub fn create_decentralized_identifier(
    network_info: NetworkInfo,
    wallet_info: WalletInfo,
) -> Result<String> {
    let rt = Runtime::new().unwrap();
    rt.block_on(async {
        let node_url = network_info.node_url;
        let stronghold_password = wallet_info.stronghold_password;
        let stronghold_filepath = wallet_info.stronghold_filepath;

        env::set_current_dir(&stronghold_filepath).ok();

        let mut path_buf_snapshot = PathBuf::new();
        path_buf_snapshot.push(&stronghold_filepath);
        path_buf_snapshot.push("wallet.stronghold");
        let path_snapshot = PathBuf::from(path_buf_snapshot);

        // THIS returns a StrongholdAdapter:
        // let mut secret_manager = StrongholdSecretManager::builder()
        //     .password(&stronghold_password)
        //     .build(path_snapshot)?;

        // THIS returns a StrongholdSecretManager:
        let secret_manager: WalletSecretManager = WalletSecretManager::Stronghold(
            WalletStrongholdSecretManager::builder()
                .password(&stronghold_password)
                .build(path_snapshot)?,
        );
        //Create a client with that node.
        // let client = Client::builder()
        //     .with_node(&node_url)?
        //     .with_ignore_node_health()
        //     .finish()?;

        // Create a new client to interact with the IOTA ledger.
        let client: Client = Client::builder()
            .with_primary_node(&node_url, None)?
            .finish()?;

        //let client: Client = Client::builder().with_primary_node(node_url, None)?.finish()?;
        // Get the Bech32 human-readable part (HRP) of the network.
        let network_name: NetworkName = client.network_name().await?;

        // Create a new DID document with a placeholder DID.
        // The DID will be derived from the Alias Id of the Alias Output after publishing.
        let mut document: IotaDocument = IotaDocument::new(&network_name);

        // Insert a new Ed25519 verification method in the DID document.
        let keypair: KeyPair = KeyPair::new(KeyType::Ed25519)?;
        let method: VerificationMethod = VerificationMethod::new(
            document.id().clone(),
            keypair.type_(),
            keypair.public(),
            "#key-1",
        )?;
        document.insert_method(method, MethodScope::VerificationMethod)?;

        // Construct an Alias Output containing the DID document, with the wallet address
        // set as both the state controller and governor.
        //let address: Address = client.get_addresses(&secret_manager).with_range(0..1).get_raw().await?[0];

        // Convert given address (BECH32 string) to Address struct
        let (_, address) = Address::try_from_bech32(&wallet_info.last_address)?;

        let alias_output: AliasOutput = client.new_did_output(address, document, None).await?;

        // Publish the Alias Output and get the published DID document.
        let document: IotaDocument = client
            .publish_did_output(&secret_manager, alias_output)
            .await?;
        Ok(document.to_string())

        //println!("Published DID document: {:#}", document);

        // let governor_address = alias_output.governor_address();
        // let result_string = governor_address.to_bech32(network_name.to_string());
        // Ok(result_string)

        //Ok(document.to_string() + &(address.is_ed25519().to_string()))
        //Ok(address.is_ed25519().to_string())

        //Ok(document.to_string())
    })
}
```
