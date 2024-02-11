# Android

---

Building The Playground App for Android using **IOTA SDK and identity.rs**.

The steps to follow are entirely analogous to those outlined in the chapters

- [Building a Comprehensive App -> Init Flutter App and Setup FRB](../building-a-comprehensive-app/init-flutter-app-and-setup-frb.md)
- [Building a Comprehensive App -> Building for Android](../building-a-comprehensive-app/building-for-android.md)

This is a chapter for advanced users. If it is moving too quickly for you, I would like to refer you to the previous chapters where all the steps were explained.

---

## Clone the GitHub Repository

<a href="https://github.com/iota-for-flutter/playground_app" target="_blank">👉 &nbsp; GitHub Repo - Playground App (Flutter only)</a>

In a terminal, execute:

```
% git clone https://github.com/iota-for-flutter/playground_app.git
% cd playground_app
% code .
```

### Correct the libraries in pubspec.yaml

I forgot the 2 dependencies meta and uuid. So please add in pubspec.yaml:

```
dependencies:
  ...
  meta: ^1.9.1
  uuid: ^4.0.0
```

You might also encounter a message asking you to update the dependencies. You can execute updates by typing `flutter pub upgrade`. There is one exception regarding the dev_dependency ffi_gen: The version number should be

```
dev_dependencies:
  ...
  ffigen: ^9.0.1
  ...
```

### Fix the problem with missing gradle files

- Start the Android Emulator
- Run the Flutter app with `flutter run`
- Stop the Flutter app
- Restart VSCode

### Fix the GradleException error

In _android/app/build.gradle_, replace GradleException by FileNotFoundException.

The error should disappear.

---

## Setup the Flutter Rust Bridge

```
% cargo new --lib rust
% cargo install flutter_rust_bridge_codegen
% flutter pub add --dev ffigen && flutter pub add ffi
% flutter pub add flutter_rust_bridge
% flutter pub add -d build_runner
% flutter pub add -d freezed
% flutter pub add freezed_annotation
```

---

## Add the Rust Code

This is a brief list of the specific versions I utilized:

- flutter_rust_bridge: 1.82.5
- iota_sdk: 1.1.3
- iota_stronghold: 2.0.0
- identity_core: 1.0.0
- rocksdb: 0.21.0

### Cargo.toml

```rust,ignore
[package]
name = "rust"
version = "0.1.0"
edition = "2021"

[dependencies]
flutter_rust_bridge = "1"

iota-sdk = { version = "1.1.3", default-features = false, features = [
    "client",
    "wallet",
    "tls",
    "rocksdb",
    "stronghold",
] }

identity_iota = { version = "1.0.0", features = ["memstore"] }

tokio = { version = "1.34.0", features = ["full"] }
anyhow = { version = "1.0.75" }

serde = { version = "1.0.193", default-features = false, features = ["derive"] }
serde_json = { version = "1.0.108", default-features = false }

lazy_static = "1.4.0"
once_cell = "1.19.0"

[lib]
crate-type = ["staticlib", "cdylib"]
```

> Make sure to include the necessary features for utilizing Stronghold and RocksDB. Forgetting any feature might render certain code inaccessible. If you're interested, simply download the library code from GitHub and check it out.

### Create api.rs

1. As usual, create an empty file called `api.rs`, at the same level as `lib.rs`.

2. Include it as module in `lib.rs`:

   ```rust, ignore
   mod api;
   ```

### Add the content of api.rs

```rust,ignore
use anyhow::Result;
use tokio::runtime::Runtime;

use iota_sdk::{
    client::secret::stronghold::StrongholdSecretManager as WalletStrongholdSecretManager,
    client::secret::SecretManager as WalletSecretManager,
    client::utils::request_funds_from_faucet,
    client::Client,
    types::block::{address::Bech32Address, output::AliasOutput},
};

use std::{env, path::PathBuf, u32};

use identity_iota::{
    iota::{IotaClientExt, IotaDocument, IotaIdentityClientExt, NetworkName},
    storage::{JwkDocumentExt, JwkMemStore, KeyIdMemstore, Storage},
    verification::{jws::JwsAlgorithm, MethodScope},
};

mod wallet_singleton;

#[derive(Debug, Clone)]
pub struct NetworkInfo {
    pub node_url: String,
    pub faucet_url: String,
}

#[allow(dead_code)]
pub fn get_node_info(network_info: NetworkInfo) -> Result<String> {
    let rt = Runtime::new().unwrap();
    rt.block_on(async {
        let node_url = network_info.node_url;

        // Create a client with that node.
        let client = Client::builder()
            .with_node(&node_url)?
            .with_ignore_node_health()
            .finish()
            .await?;

        // Get node info.
        let info = client.get_info().await?.node_info;

        Ok(serde_json::to_string_pretty(&info).unwrap())
        //Ok(info.node_info.base_token.name)
    })
}

#[allow(dead_code)]
pub fn generate_mnemonic() -> String {
    let mnemonic = Client::generate_mnemonic();
    mnemonic.unwrap().to_string()
}

#[derive(Debug, Clone)]
pub struct WalletInfo {
    pub alias: String,
    pub mnemonic: String,
    pub stronghold_password: String,
    pub stronghold_filepath: String,
    pub last_address: String,
}

#[allow(dead_code)]
pub fn create_wallet_account(network_info: NetworkInfo, wallet_info: WalletInfo) -> Result<String> {
    wallet_singleton::create_wallet_account(network_info, wallet_info)
}

#[allow(dead_code)]
pub fn generate_address(wallet_info: WalletInfo) -> Result<String> {
    wallet_singleton::generate_address(wallet_info)
}

#[allow(dead_code)]
pub fn request_funds(network_info: NetworkInfo, wallet_info: WalletInfo) -> Result<String> {
    let rt = Runtime::new().unwrap();
    rt.block_on(async {
        let stronghold_filepath = wallet_info.stronghold_filepath;
        let last_address = wallet_info.last_address;
        env::set_current_dir(&stronghold_filepath).ok();

        let faucet_url = network_info.faucet_url;

        // Convert given address (BECH32 string) to Address struct
        let address = Bech32Address::try_from_str(&last_address)?;

        // Use the function iota_wallet::iota_client::request_funds_from_faucet
        let faucet_response = request_funds_from_faucet(&faucet_url, &address).await?;

        Ok(faucet_response.to_string())
    })
}

#[derive(Debug, Clone)]
pub struct BaseCoinBalance {
    /// Total amount
    pub total: u64,
    /// Balance that can currently be spent
    pub available: u64,
}

#[allow(dead_code)]
pub fn check_balance(wallet_info: WalletInfo) -> Result<BaseCoinBalance> {
    wallet_singleton::check_balance(wallet_info)
}

type MemStorage = Storage<JwkMemStore, KeyIdMemstore>;
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
        let last_address = wallet_info.last_address;

        env::set_current_dir(&stronghold_filepath).ok();

        let mut path_buf_snapshot = PathBuf::new();
        path_buf_snapshot.push(&stronghold_filepath);
        path_buf_snapshot.push("wallet.stronghold");
        let path_snapshot = PathBuf::from(path_buf_snapshot);

        // Create a new client to interact with the IOTA ledger.
        let client: Client = Client::builder()
            .with_primary_node(&node_url, None)?
            .finish()
            .await?;

        // Create a new secret manager backed by a Stronghold.
        let secret_manager: WalletSecretManager = WalletSecretManager::Stronghold(
            WalletStrongholdSecretManager::builder()
                .password(stronghold_password)
                .build(path_snapshot)?,
        );

        // Convert given address (BECH32 string) to Address struct
        let address = Bech32Address::try_from_str(&last_address)?;

        // Get the Bech32 human-readable part (HRP) of the network.
        let network_name: NetworkName = client.network_name().await?;

        // Create a new DID document with a placeholder DID.
        // The DID will be derived from the Alias Id of the Alias Output after publishing.
        let mut document: IotaDocument = IotaDocument::new(&network_name);

        // Insert a new Ed25519 verification method in the DID document.
        let storage: MemStorage = MemStorage::new(JwkMemStore::new(), KeyIdMemstore::new());
        document
            .generate_method(
                &storage,
                JwkMemStore::ED25519_KEY_TYPE,
                JwsAlgorithm::EdDSA,
                None,
                MethodScope::VerificationMethod,
            )
            .await?;

        // Insert a new Ed25519 verification method in the DID document.
        let storage: MemStorage = MemStorage::new(JwkMemStore::new(), KeyIdMemstore::new());
        document
            .generate_method(
                &storage,
                JwkMemStore::ED25519_KEY_TYPE,
                JwsAlgorithm::EdDSA,
                None,
                MethodScope::VerificationMethod,
            )
            .await?;

        // Construct an Alias Output containing the DID document, with the wallet address
        // set as both the state controller and governor.
        let alias_output: AliasOutput = client.new_did_output(*address, document, None).await?;

        // Publish the Alias Output and get the published DID document.
        let document: IotaDocument = client
            .publish_did_output(&secret_manager, alias_output)
            .await?;
        Ok(document.to_string())
    })
}

#[allow(dead_code)]
pub fn bin_to_hex(val: String, len: usize) -> String {
    let n: u32 = u32::from_str_radix(&val, 2).unwrap();
    format!("{:01$x}", n, len * 2)
}
```

### Create a new file `wallet_singleton.rs`

Create a folder named `api` next to `api.rs`. Inside of this folder, create a new file `wallet_singleton.rs` and copy/paste this content in it:

```rust,ignore
use crate::api::{BaseCoinBalance, NetworkInfo, WalletInfo};
use anyhow::{Error, Result};
use iota_sdk::{
    client::constants::SHIMMER_COIN_TYPE,
    client::secret::stronghold::StrongholdSecretManager as WalletStrongholdSecretManager,
    client::secret::SecretManager as WalletSecretManager,
    crypto::keys::bip39::Mnemonic,
    wallet::{ClientOptions, Wallet},
};
use std::env;
use std::fs;
use std::path::PathBuf;
use std::sync::{Mutex, Once};
use tokio::runtime::Runtime;

struct WalletSingleton {
    network_info: NetworkInfo,
    wallet_info: WalletInfo,
    wallet: Option<Wallet>,
}

impl WalletSingleton {
    fn new(network_info: NetworkInfo, wallet_info: WalletInfo) -> Result<Self> {
        let mut wallet_singleton = Self {
            network_info,
            wallet_info,
            wallet: None,
        };

        wallet_singleton.create_wallet()?;
        Ok(wallet_singleton)
    }

    fn create_wallet(&mut self) -> Result<String> {
        let rt = Runtime::new().unwrap();
        rt.block_on(async {
            let node_url = &self.network_info.node_url;

            let stronghold_password = self.wallet_info.stronghold_password.clone();
            let stronghold_filepath = self.wallet_info.stronghold_filepath.clone();
            let mnemonic_string: String = self.wallet_info.mnemonic.clone();
            let mnemonic = Mnemonic::from(mnemonic_string);

            // Create the needed directory according to the given path
            let mut path_buf = PathBuf::new();
            path_buf.push(&stronghold_filepath);
            let path = PathBuf::from(path_buf);
            fs::create_dir_all(path).ok();

            // THIS NEXT STEP IS CRUCIAL:
            // Point the "current working directory" to the given path
            env::set_current_dir(&stronghold_filepath).ok();

            // Create the Rust file for the stronghold snapshot file
            let mut path_buf_snapshot = PathBuf::new();
            path_buf_snapshot.push(&stronghold_filepath);
            path_buf_snapshot.push("wallet.stronghold");
            let path_snapshot = PathBuf::from(path_buf_snapshot);

            let secret_manager = WalletStrongholdSecretManager::builder()
                .password(stronghold_password)
                .build(path_snapshot)?;

            // Storing the mnemonic is ONLY REQUIRED THE FIRST TIME
            // calling it TWICE THROWS AN ERROR
            secret_manager.store_mnemonic(mnemonic).await?;

            // Create a ClientBuilder (= client_options in wallet.rs)
            // See wallet.rs:
            // -> src/lib.rs
            // -> line "pub use iota_client::ClientBuilder as ClientOptions"
            let client_options = ClientOptions::new().with_node(&node_url)?;

            // Create the account manager with the secret_manager
            // and client_options (= ClientBuilder).
            // The Client itself is created in the AccountManagerBuilder's finish() method.
            // See wallet.rs:
            // -> src/account_manager/builder.rs
            // -> line "let client = client_options.clone().finish()?;"

            self.wallet = Some(
                Wallet::builder()
                    .with_secret_manager(WalletSecretManager::Stronghold(secret_manager))
                    .with_client_options(client_options)
                    .with_coin_type(SHIMMER_COIN_TYPE)
                    .finish()
                    .await?,
            );

            Ok("Wallet Account was created successfully.".into())
        })
    }

    fn create_account(&self, wallet_info: WalletInfo) -> Result<String> {
        let wallet_singleton = self;
        let result = Runtime::new().unwrap().block_on(async {
            if let Some(ref wallet) = wallet_singleton.wallet {
                let wallet_alias = wallet_info.alias;
                let _account = wallet
                    .create_account()
                    .with_alias((&wallet_alias).to_string())
                    .finish()
                    .await?;
                Ok("Account was created successfully.".into())
            } else {
                Err(Error::msg("No wallet set."))
            }
        });
        result
    }

    fn generate_address(&self, wallet_info: WalletInfo) -> Result<String> {
        let wallet_singleton = self;
        let rt = Runtime::new().unwrap();
        rt.block_on(async {
            if let Some(ref wallet) = wallet_singleton.wallet {
                let stronghold_password = wallet_info.stronghold_password;
                let wallet_alias = wallet_info.alias;
                let account = wallet.get_account((&wallet_alias).to_string()).await?;

                wallet.set_stronghold_password(stronghold_password).await?;

                let addresses = account.generate_ed25519_addresses(1, None).await?;
                Ok(addresses[0].address().to_string())
            } else {
                Err(Error::msg("No wallet set."))
            }
        })
    }

    fn check_balance(&self, wallet_info: WalletInfo) -> Result<BaseCoinBalance> {
        let wallet_singleton = self;
        let rt = Runtime::new().unwrap();
        rt.block_on(async {
            if let Some(ref wallet) = wallet_singleton.wallet {
                let stronghold_filepath = wallet_info.stronghold_filepath;
                env::set_current_dir(&stronghold_filepath).ok();

                let wallet_alias = wallet_info.alias;
                let account = wallet.get_account((&wallet_alias).to_string()).await?;

                // Sync and get the balance
                let account_balance = account.sync(None).await?;

                let base_coin_balance = BaseCoinBalance {
                    total: account_balance.base_coin().total(),
                    available: account_balance.base_coin().available(),
                };
                //let total = account_balance.base_coin.total;
                //println!("{:?}", account_balance);

                //Ok(total.to_string())
                Ok(base_coin_balance)
            } else {
                Err(Error::msg("No wallet set."))
            }
        })
    }
}

lazy_static::lazy_static! {
    static ref WALLET_SINGLETON: Mutex<Option<WalletSingleton>> = Mutex::new(None);
    static ref INIT: Once = Once::new();
}

fn create_wallet_singleton_if_needed(network_info: NetworkInfo, wallet_info: WalletInfo) {
    INIT.call_once(|| {
        if let Ok(wallet_singleton) = WalletSingleton::new(network_info, wallet_info) {
            let mut locked_wallet_singleton = WALLET_SINGLETON.lock().unwrap();
            *locked_wallet_singleton = Some(wallet_singleton);
        } else {
            // Handle the error
            // You can log an error, panic, or choose an appropriate action.
            panic!("Error creating wallet singleton");
        }
    });
}

pub fn create_wallet_account(network_info: NetworkInfo, wallet_info: WalletInfo) -> Result<String> {
    create_wallet_singleton_if_needed(network_info, wallet_info.clone());
    let locked_wallet_singleton = WALLET_SINGLETON.lock().unwrap();
    let wallet_singleton = locked_wallet_singleton.as_ref().unwrap();
    wallet_singleton.create_account(wallet_info)
}

pub fn generate_address(wallet_info: WalletInfo) -> Result<String> {
    let locked_wallet_singleton = WALLET_SINGLETON.lock().unwrap();
    let wallet_singleton = locked_wallet_singleton.as_ref().unwrap();
    wallet_singleton.generate_address(wallet_info)
}

pub fn check_balance(wallet_info: WalletInfo) -> Result<BaseCoinBalance> {
    let locked_wallet_singleton = WALLET_SINGLETON.lock().unwrap();
    let wallet_singleton = locked_wallet_singleton.as_ref().unwrap();
    wallet_singleton.check_balance(wallet_info)
}
```

---

## Generate the Dart Interface

Use this command (you need to be in the root of your project):

```
flutter_rust_bridge_codegen \
--rust-input rust/src/api.rs \
--dart-output ./lib/bridge_generated.dart \
--dart-decl-output ./lib/bridge_definitions.dart \
--wasm
```

---

## Adjust the Dart Code

### Include the Rust library

Create a file `ffi.dart` next to `main.dart` and paste this content. As you can see it returns the _api_ variable.

```
// This file initializes the dynamic library and connects it with the stub
// generated by flutter_rust_bridge_codegen.

import 'dart:ffi';

import 'bridge_generated.dart';
import 'bridge_definitions.dart';
export 'bridge_definitions.dart';

// Re-export the bridge so it is only necessary to import this file.
export 'bridge_generated.dart';
import 'dart:io' as io;

const _base = 'rust';

// On MacOS, the dynamic library is not bundled with the binary,
// but rather directly **linked** against the binary.
final _dylib = io.Platform.isWindows ? '$_base.dll' : 'lib$_base.so';

final Rust api = RustImpl(io.Platform.isIOS || io.Platform.isMacOS
    ? DynamicLibrary.executable()
    : DynamicLibrary.open(_dylib));
```

### Adjust Example 1: Get Node Information

The source code for Example 1 can be found at `lib/examples/example_0.dart`.

At the top, add:

```dart
import 'package:flutter_rust_bridge/flutter_rust_bridge.dart';
import '../ffi.dart';
```

Replace the fake code of `_callFfiNodeInfo()` by:

```dart
  Future<void> _callFfiNodeInfo() async {
    String nodeUrl =
        Provider.of<AppProvider>(context, listen: false).currentNetwork.url;
    String faucetUrl = Provider.of<AppProvider>(context, listen: false)
            .currentNetwork
            .faucetApiUrl ??
        '';
    final NetworkInfo networkInfo =
        NetworkInfo(nodeUrl: nodeUrl, faucetUrl: faucetUrl);
    try {
      customOverlay.show(context);
      final receivedText = await api.getNodeInfo(networkInfo: networkInfo);
      if (mounted) {
        Provider.of<AppProvider>(context, listen: false).nodeInfo =
            receivedText;
        setState(() => exampleSteps[1].setOutput(receivedText));
      }
      customOverlay.hide();
    } on FfiException catch (e) {
      setState(() => exampleSteps[1].setOutput(e.message));
      customOverlay.hide();
    }
  }
```

### Adjust Example 2: Generate Mnemonics

The source code for Example 2 can be found at `lib/examples/example_1.dart`.

At the top, add:

```dart
import '../ffi.dart';
```

Replace the fake code of `_callFfiGenerateMnemonic()` by:

```dart
  Future<void> _callFfiGenerateMnemonic() async {
    customOverlay.show(context);
    final receivedText = await api.generateMnemonic();
    if (mounted) {
      setState(() {
        Provider.of<AppProvider>(context, listen: false).mnemonic =
            receivedText;
        exampleSteps[1].setOutput(receivedText);
      });
    }
    customOverlay.hide();
  }
```

### Adjust Example 3: Create Wallet Account

The source code for Example 3 can be found at `lib/examples/example_2.dart`.

At the top, add:

```dart
import 'package:flutter_rust_bridge/flutter_rust_bridge.dart';
import '../ffi.dart';
```

Replace the fake code of `_callFfiCreateWalletAccount()` by:

```dart
  Future<void> _callFfiCreateWalletAccount() async {
    String nodeUrl =
        Provider.of<AppProvider>(context, listen: false).currentNetwork.url;
    String faucetUrl = Provider.of<AppProvider>(context, listen: false)
            .currentNetwork
            .faucetApiUrl ??
        '';
    final NetworkInfo networkInfo =
        NetworkInfo(nodeUrl: nodeUrl, faucetUrl: faucetUrl);
    final WalletInfo walletInfo = WalletInfo(
      alias: exampleSteps[1].output ?? 'Account_1',
      mnemonic: Provider.of<AppProvider>(context, listen: false).mnemonic,
      strongholdPassword:
          exampleSteps[2].output ?? 'my_super_secret_stronghold_password',
      strongholdFilepath: _strongholdFilePath,
      lastAddress: "",
    );
    try {
      customOverlay.show(context);

      final receivedText = await api.createWalletAccount(
          networkInfo: networkInfo, walletInfo: walletInfo);
      if (mounted) {
        setState(() => exampleSteps[3].setOutput(receivedText));
      }
      customOverlay.hide();
    } on FfiException catch (e) {
      setState(() => exampleSteps[3].setOutput(e.message));
      customOverlay.hide();
    }
  }
```

### Adjust Example 4: Generate Address

The source code for Example 4 can be found at `lib/examples/example_3.dart`.

At the top, add:

```dart
import 'package:flutter_rust_bridge/flutter_rust_bridge.dart';
import '../ffi.dart';
```

Replace the fake code of `_callFfiGenerateAddress()` by:

```dart
  Future<void> _callFfiGenerateAddress() async {
    final WalletInfo walletInfo = WalletInfo(
      alias: exampleSteps[1].output ?? 'Account_1',
      mnemonic: Provider.of<AppProvider>(context, listen: false).mnemonic,
      strongholdPassword:
          exampleSteps[2].output ?? 'my_super_secret_stronghold_password',
      strongholdFilepath: _strongholdFilePath,
      lastAddress: Provider.of<AppProvider>(context, listen: false).lastAddress,
    );
    try {
      customOverlay.show(context);

      final receivedText = await api.generateAddress(walletInfo: walletInfo);
      if (mounted) {
        setState(() {
          Provider.of<AppProvider>(context, listen: false).lastAddress =
              receivedText;
          exampleSteps[3].setOutput(receivedText);
        });
      }
      customOverlay.hide();
    } on FfiException catch (e) {
      customOverlay.hide();
      setState(() => exampleSteps[3].setOutput(e.message));
    }
  }

```

### Adjust Example 5: Request Funds

The source code for Example 5 can be found at `lib/examples/example_4.dart`.

At the top, add:

```dart
import 'package:flutter_rust_bridge/flutter_rust_bridge.dart';
import '../ffi.dart';
```

Replace the fake code of `_callFfiRequestFunds()` by:

```dart
  Future<void> _callFfiRequestFunds() async {
    String nodeUrl =
        Provider.of<AppProvider>(context, listen: false).currentNetwork.url;
    String faucetUrl = Provider.of<AppProvider>(context, listen: false)
            .currentNetwork
            .faucetApiUrl ??
        '';
    final NetworkInfo networkInfo =
        NetworkInfo(nodeUrl: nodeUrl, faucetUrl: faucetUrl);
    final WalletInfo walletInfo = WalletInfo(
      alias: "",
      mnemonic: "",
      strongholdPassword: "",
      strongholdFilepath: _strongholdFilePath,
      lastAddress: Provider.of<AppProvider>(context, listen: false).lastAddress,
    );

    try {
      customOverlay.show(context);

      final receivedText = await api.requestFunds(
          networkInfo: networkInfo, walletInfo: walletInfo);
      if (mounted) {
        setState(() => exampleSteps[1].setOutput(receivedText));
      }
      customOverlay.hide();
    } on FfiException catch (e) {
      customOverlay.hide();
      setState(() => exampleSteps[1].setOutput(e.message));
    }
  }
```

### Adjust Example 6: Check Balance

The source code for Example 6 can be found at `lib/examples/example_5.dart`.

At the top, add:

```dart
import 'package:flutter_rust_bridge/flutter_rust_bridge.dart';
import 'package:provider/provider.dart';
import '../coin_utils.dart';
import '../data/app_provider.dart';
import '../ffi.dart';
```

Replace the fake code of `_callFfiCheckBalance()` by:

```dart
  Future<void> _callFfiCheckBalance() async {
    String coinCurrency =
        Provider.of<AppProvider>(context, listen: false).currentNetwork.coin;
    final WalletInfo walletInfo = WalletInfo(
      alias: exampleSteps[0].output ?? 'Account_1',
      mnemonic: "",
      strongholdPassword: "",
      strongholdFilepath: _strongholdFilePath,
      lastAddress: Provider.of<AppProvider>(context, listen: false).lastAddress,
    );

    try {
      customOverlay.show(context);

      final receivedBaseCoinBalance =
          await api.checkBalance(walletInfo: walletInfo);
      if (mounted) {
        String totalString =
            displayBalance(receivedBaseCoinBalance.total, coinCurrency);
        String availableString =
            displayBalance(receivedBaseCoinBalance.available, coinCurrency);
        String result =
            'Total:         $totalString\nAvailable:  $availableString';
        setState(() {
          exampleSteps[1].setOutput(result);
          Provider.of<AppProvider>(context, listen: false).balanceTotal =
              receivedBaseCoinBalance.total;
          Provider.of<AppProvider>(context, listen: false).balanceAvailable =
              receivedBaseCoinBalance.available;
        });
      }
      customOverlay.hide();
    } on FfiException catch (e) {
      customOverlay.hide();
      setState(() => exampleSteps[1].setOutput(e.message));
    }
  }
```

### Adjust Example 7: Create DID

The source code for Example 7 can be found at `lib/examples/example_6.dart`.

At the top, add:

```dart
import 'package:flutter_rust_bridge/flutter_rust_bridge.dart';
import '../ffi.dart';
```

Replace the fake code of `_callFfiCreateDecentralizedIdentifier()` by:

```dart
  Future<void> _callFfiCreateDecentralizedIdentifier() async {
    String nodeUrl =
        Provider.of<AppProvider>(context, listen: false).currentNetwork.url;
    final NetworkInfo networkInfo =
        NetworkInfo(nodeUrl: nodeUrl, faucetUrl: '');
    final WalletInfo walletInfo = WalletInfo(
      alias: "",
      mnemonic: "",
      strongholdPassword: 'my_super_secret_stronghold_password',
      strongholdFilepath: _strongholdFilePath,
      lastAddress: Provider.of<AppProvider>(context, listen: false).lastAddress,
    );

    try {
      customOverlay.show(context);

      final receivedText = await api.createDecentralizedIdentifier(
          networkInfo: networkInfo, walletInfo: walletInfo);
      if (mounted) {
        setState(() {
          exampleSteps[0].setOutput(receivedText);
        });
      }
      customOverlay.hide();
    } on FfiException catch (e) {
      customOverlay.hide();
      setState(() => exampleSteps[0].setOutput(e.message));
    }
  }
```

### Adjust my_drawer.dart

The source code can be found at `lib/widgets/my_drawer.dart`.

At the top, add:

```dart
import '../ffi.dart';
```

Replace the fake code in line 59 `receivedText = 'fake 0000 0000 0000';` by:

```dart
  receivedText = await api.binToHex(val: substr, len: 1);
```

Update the correct libs and versions, too. You'll find the right place.

```dart
...
              Container(
                margin: const EdgeInsets.only(top: 20, bottom: 5),
                padding: const EdgeInsets.symmetric(horizontal: 20),
                alignment: Alignment.topLeft,
                height: 24,
                child: Text(
                  'Included Rust Libraries',
                  style: Theme.of(context).textTheme.titleLarge,
                ),
              ),
              Container(
                padding:
                    const EdgeInsets.symmetric(horizontal: 20, vertical: 2),
                child: const Row(
                  children: [
                    SizedBox(
                      width: 100,
                      child: Text(
                        'iota_sdk:',
                      ),
                    ),
                    Text("v1.1.3"),
                  ],
                ),
              ),
              Container(
                padding:
                    const EdgeInsets.symmetric(horizontal: 20, vertical: 2),
                child: const Row(
                  children: [
                    SizedBox(
                      width: 100,
                      child: Text(
                        'iota_stronghold:',
                      ),
                    ),
                    Text("v2.0.0"),
                  ],
                ),
              ),
              Container(
                padding:
                    const EdgeInsets.symmetric(horizontal: 20, vertical: 2),
                child: const Row(
                  children: [
                    SizedBox(
                      width: 100,
                      child: Text(
                        'identity_iota:',
                     ),
                    ),
                    Text("v1.0.0"),
                  ],
                ),
              ),
...
```

---

## Configure the Android setup

### Install the cargo-ndk for Android

```
% cd rust
% cargo install cargo-ndk
% cd ..
```

### Integrate `cargo build` into the Gradle build process

If you've had NO problem with the 3rd party library _libsodium_, add at the bottom:

```
[
        Debug: null,
        Profile: '--release',
        Release: '--release'
].each {
    def taskPostfix = it.key
    def profileMode = it.value
    tasks.whenTaskAdded { task ->
        if (task.name == "javaPreCompile$taskPostfix") {
            task.dependsOn "cargoBuild$taskPostfix"
        }
    }
    tasks.register("cargoBuild$taskPostfix", Exec) {
        workingDir "../../rust"  // <-- ATTENTION: CHECK THE CORRECT FOLDER!!!
        environment ANDROID_NDK_HOME: "$ANDROID_NDK"
        commandLine 'cargo', 'ndk',
                // the 2 ABIs below are used by real Android devices
                // '-t', 'armeabi-v7a',
                '-t', 'arm64-v8a',
                // the below 2 ABIs are usually used for Android simulators,
                // add or remove these ABIs as needed.
                // '-t', 'x86',
                // '-t', 'x86_64',
                '-o', '../android/app/src/main/jniLibs', 'build'
        if (profileMode != null) {
            args profileMode
        }
    }
}
```

### Enable the dynamic library loading

To enable dynamic library loading, you must place the `libc++_shared.so` file next to the `librust.so` (which is generated when you execute `flutter run`). The output directory _android/app/src/main/jniLibs/{abi}/_ is automatically created after the first execution, but not before.

I want to address this point before I run `flutter run` for the first time. Therefore, I create the directory _android/app/src/main/jniLibs/{abi}/_ manually and place the library `libc++_shared.so` in there.

Here are some download links from Android NDK 25 on macOS:

- [arm64-v8a/libc++\_shared.so](../assets/download/arm64-v8a/libc++_shared.so)
- [armeabi-v7a/libc++\_shared.so](../assets/download/armeabi-v7a/libc++_shared.so)
- [x86/libc++\_shared.so](../assets/download/x86/libc++_shared.so)
- [x86_64/libc++\_shared.so](../assets/download/x86_64/libc++_shared.so)

For example, place libc++\_shared.so for abi _arm64-v8a_ into the folder _android/app/src/main/jniLibs/arm64-v8a/_

---

## Start the app

- Launch the Emulator
- Run `flutter run`

## Video

<iframe 
    class="video"  
    src="https://www.youtube.com/embed/WGVJEaKGqzw" 
    title="Building the Playground App for Android using IOTA SDK and identity.rs" 
    frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    allowfullscreen>
</iframe>
