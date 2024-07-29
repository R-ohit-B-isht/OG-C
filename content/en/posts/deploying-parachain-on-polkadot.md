---
title: "Deploying a Parachain on Polkadot"
date: 2024-07-27T12:00:00Z
draft: false
tags: ["Polkadot", "Parachain", "Blockchain", "Web3"]
description: "A comprehensive guide to deploying a parachain on the Polkadot network, covering environment setup, parachain building, and registration process."
author: "Rohit Bisht"
---

# Deploying a Parachain on Polkadot

## Introduction

Polkadot is not just another blockchain; it's a revolutionary protocol that enables multiple specialized blockchains to interconnect and share security. At the heart of this ecosystem are parachains - custom, project-specific blockchains that run in parallel within the Polkadot network.

Deploying a parachain on Polkadot opens up a world of possibilities for your blockchain project. It allows you to leverage Polkadot's robust infrastructure, shared security, and cross-chain interoperability while maintaining the flexibility to design your chain for specific use cases.

In this comprehensive guide, we'll walk you through the process of deploying a parachain on the Polkadot network. We'll cover everything from setting up your development environment to registering your parachain on the network. By the end of this tutorial, you'll have a functioning parachain connected to the Polkadot ecosystem.

Let's embark on this exciting journey into the world of Polkadot parachains! 🚀

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Setting Up the Development Environment](#setting-up-the-environment)
4. [Building Your Parachain](#building-the-parachain)
5. [Registering Your Parachain](#registering-the-parachain)
6. [Deploying the Parachain](#deploying-the-parachain)
   - [Prepare the Deployment Environment](#1-prepare-the-deployment-environment)
   - [Launch the Collator Node](#2-launch-the-collator-node)
   - [Connect to the Relay Chain](#3-connect-to-the-relay-chain)
   - [Validate Parachain Functionality](#4-validate-parachain-functionality)
   - [Monitor and Maintain](#5-monitor-and-maintain)
7. [Troubleshooting](#troubleshooting)
8. [Conclusion](#conclusion)

## Prerequisites

Before we dive into the deployment process, make sure you have the following prerequisites in place:

1. **Basic understanding of blockchain technology and Polkadot**:
   - Familiarity with blockchain concepts
   - Understanding of Polkadot's architecture

2. **Rust programming language**:
   - Polkadot's parachain development is primarily done in Rust
   - If you're new to Rust, don't worry - we'll guide you through the installation process

3. **Command-line interface (CLI) proficiency**:
   - Comfortable using the command line
   - Ability to execute various commands throughout the tutorial

4. **Git**:
   - Version control system
   - Used for downloading necessary repositories

5. **A Unix-based operating system**:
   - Tutorial designed for Unix-based systems (Linux or macOS)
   - Windows users should consider setting up a virtual machine or using Windows Subsystem for Linux (WSL)

6. **Sufficient hardware resources**:
   - At least 8GB of RAM
   - Multi-core processor

Now that we've covered the prerequisites, let's move on to setting up our development environment.

## Setting Up the Environment

To deploy a parachain, we need to set up a robust development environment. Let's go through this process step-by-step.

### 1. Install Rust

Rust is the primary programming language used for parachain development in the Polkadot ecosystem. Follow these steps to install Rust:

- Open your terminal and run the following command:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

This command downloads and runs the official Rust installation script.

- Follow the on-screen prompts to complete the installation. When asked to choose an installation option, select the default option (1).

- After the installation is complete, restart your terminal or run the following command to update your current shell session:

```bash
source $HOME/.cargo/env
```

- Verify your Rust installation by checking the version:

```bash
rustc --version
```

You should see output similar to this:

```
rustc 1.XX.X (XXXXXXXX 20XX-XX-XX)
```

- Install the nightly toolchain and set it as the default:

```bash
rustup default nightly
rustup target add wasm32-unknown-unknown
```

Great! You now have Rust installed and configured for parachain development. In the next section, we'll install the Polkadot SDK and set up a local Substrate node.

### 2. Install the Polkadot SDK

The Polkadot SDK provides the necessary tools and libraries for parachain development. Follow these steps to install it:

- Clone the Polkadot repository:

```bash
git clone https://github.com/paritytech/polkadot.git
cd polkadot
```

- Checkout the latest release:

```bash
git checkout $(git tag | sort -V | tail -n 1)
```

- Install the required dependencies:

```bash
./scripts/init.sh
```

- Build the Polkadot binary:

```bash
cargo build --release
```

This process may take some time, depending on your system's capabilities.

### 3. Set Up a Local Substrate Node

Substrate is the framework used to build parachains. Setting up a local Substrate node will allow you to test your parachain before deploying it to the Polkadot network.

- Clone the Substrate Node Template:

```bash
git clone https://github.com/substrate-developer-hub/substrate-node-template.git
cd substrate-node-template
```

- Checkout the latest release:

```bash
git checkout $(git tag | sort -V | tail -n 1)
```

- Build the node:

```bash
cargo build --release
```

- Run the node:

```bash
./target/release/node-template --dev
```

You should now see your local Substrate node producing blocks.

Congratulations! You've successfully set up your development environment for parachain deployment. In the next section, we'll start building our parachain.

## Building the Parachain

Once your environment is set up, you can start building your parachain. This section will cover the steps to create and configure your parachain.

### 1. Create a New Parachain Project

To create a new parachain project, we'll use the Substrate Parachain Template. This template provides a solid foundation for building your custom parachain.

- Clone the Substrate Parachain Template:

```bash
git clone https://github.com/substrate-developer-hub/substrate-parachain-template.git
cd substrate-parachain-template
```

- Checkout the latest release:

```bash
git checkout $(git tag | sort -V | tail -n 1)
```

### 2. Configure the Parachain

Now that we have our parachain project, let's configure it to suit our needs.

- Open the `runtime/src/lib.rs` file in your favorite text editor. This file contains the core configuration of your parachain.

- Modify the `construct_runtime!` macro to define your parachain's modules and their order of execution. For example:

```rust
construct_runtime!(
    pub enum Runtime where
        Block = Block,
        NodeBlock = opaque::Block,
        UncheckedExtrinsic = UncheckedExtrinsic
    {
        System: frame_system,
        Timestamp: pallet_timestamp,
        Balances: pallet_balances,
        TransactionPayment: pallet_transaction_payment,
        Sudo: pallet_sudo,
        // Add your custom pallets here
    }
);
```

- Configure your parachain's parameters in the `node/src/chain_spec.rs` file. This includes setting the initial validators, defining the token distribution, and other chain-specific settings.

### 3. Build the Parachain

With the configuration in place, it's time to build your parachain.

- Build the parachain node:

```bash
cargo build --release
```

- Build the WebAssembly runtime:

```bash
cargo build --release --features runtime-benchmarks
```

- Generate the chain specification:

```bash
./target/release/parachain-template-node build-spec --chain local > customspec.json
```

- Convert the chain spec to raw format:

```bash
./target/release/parachain-template-node build-spec --chain customspec.json --raw > customspec_raw.json
```

## Registering the Parachain

After building the parachain, you need to register it on the Polkadot network. This section will guide you through the registration process, including the crucial step of participating in a parachain slot auction.

### 1. Understand Parachain Slot Auctions

Before registering your parachain, it's essential to understand the concept of parachain slot auctions:

- Parachain slots are limited and in high demand.
- Auctions determine which parachains can connect to the Polkadot Relay Chain.
- Auctions typically run for seven days, with a ending period to prevent last-minute bidding wars.

### 2. Prepare for the Auction

To participate in a parachain slot auction, you need to:

- Ensure you have sufficient DOT tokens to place a competitive bid.
- Create a crowdloan campaign to gather additional DOT from the community (optional but recommended).
- Prepare your parachain's WebAssembly runtime and genesis state.

### 3. Submit the Parachain Registration

Follow these steps to register your parachain:

- Connect to the Polkadot network using the Polkadot.js Apps interface (https://polkadot.js.org/apps/).
- Navigate to the "Network" > "Parachains" tab.
- Click on "Parathreads" and then "Register".
- Upload your parachain's WebAssembly runtime and genesis state.
- Set your initial bid amount for the auction.
- Submit the registration transaction and wait for it to be included in a block.

### 4. Participate in the Auction

Once registered, participate in the auction:

- Monitor the current auction status in the "Auctions" tab.
- Place bids during the auction period.
- Optionally, adjust your bids based on the competition.

### 5. Verify the Registration

After the auction ends:

- Check the auction results to see if you've won a slot.
- If successful, your parachain will be assigned a ParaId and a slot on the Polkadot relay chain.
- Use the Polkadot.js Apps interface to verify your parachain's status under the "Parachains" tab.
- Once your parachain is live, you can start interacting with it using your parachain node.

### 6. Maintain Your Parachain Slot

Remember that parachain slots are leased for a fixed period:

- Monitor your lease period and prepare for renewal auctions.
- Continue development and improvement of your parachain during its active period.
- Engage with your community to maintain support for future auctions.

By following these detailed steps, you'll navigate the complex process of registering your parachain on the Polkadot network and securing a coveted parachain slot.

## Deploying the Parachain

Now that we have built and registered our parachain, it's time to deploy it to a live network. This process involves several steps to ensure your parachain is correctly connected to the Polkadot ecosystem.

### 1. Prepare the Deployment Environment

- Ensure your collator node is fully synced with the Polkadot relay chain.
- Verify that you have the latest version of your parachain runtime.
- Double-check that all necessary configurations are in place.

### 2. Launch the Collator Node

- Start your collator node with the appropriate flags:

```bash
./target/release/parachain-collator \
    --collator \
    --force-authoring \
    --chain parachain-spec.json \
    --base-path /tmp/parachain/alice \
    --port 40333 \
    --ws-port 8844 \
    -- \
    --execution wasm \
    --chain polkadot-local-testnet.json \
    --port 30343 \
    --ws-port 9977
```

- Monitor the logs to ensure your collator is producing blocks and connecting to the relay chain.

### 3. Connect to the Relay Chain

- Use the Polkadot.js Apps interface to connect to your relay chain node.
- Navigate to the "Network" > "Parachains" tab.
- Verify that your parachain is listed and its status is "Connected".

### 4. Validate Parachain Functionality

- Use the Polkadot.js Apps interface to interact with your parachain.
- Test basic functionality such as token transfers or custom pallet interactions.
- Monitor block production and finalization on both your parachain and the relay chain.

### 5. Monitor and Maintain

- Set up monitoring tools to track your parachain's performance.
- Regularly check for any necessary updates or security patches.
- Engage with your community and gather feedback on the parachain's functionality.

By following these steps, you'll successfully deploy your parachain to a live Polkadot network. Remember that maintaining a parachain is an ongoing process, requiring constant attention and updates to ensure optimal performance and security.

For more detailed information on parachain deployment, refer to the official Polkadot documentation:
[Parachain Development Kit (PDK)](https://wiki.polkadot.network/docs/build-pdk)

Congratulations on deploying your parachain! You're now an active participant in the Polkadot ecosystem, contributing to the future of interoperable blockchain technology.


## Troubleshooting

This section provides solutions to common issues you may encounter during the parachain deployment process.

### 1. Compilation Errors

If you encounter compilation errors:

- Ensure all dependencies are up to date by running `cargo update`.
- Check that you're using the correct Rust toolchain version (nightly).
- Verify that all required features are enabled in your `Cargo.toml` file.
- Clear your target directory with `cargo clean` and rebuild.

### 2. Network Connectivity Issues

If you experience network connectivity problems:

- Check your internet connection and firewall settings.
- Ensure your nodes are properly configured to connect to the Polkadot network.
- Verify that you're using the correct chain specification and network identifiers.
- Check if your ports are open and correctly forwarded (default P2P port is 30333).

### 3. Parachain Registration Issues

If you're having trouble registering your parachain:

- Double-check that your parachain's runtime and genesis state are correctly generated.
- Ensure you have sufficient DOT tokens for the parachain slot auction.
- Verify that your parachain meets all the technical requirements set by Polkadot.

### 4. Performance Issues

If your parachain is experiencing performance problems:

- Monitor your system resources and ensure your hardware meets the recommended specifications.
- Optimize your runtime code, especially any custom pallets.
- Consider using benchmarking tools to identify performance bottlenecks.

### 5. Upgrade and Migration Issues

When upgrading your parachain:

- Always test upgrades on a local or test network before applying them to production.
- Ensure all state migrations are correctly implemented and tested.
- Keep detailed documentation of all changes and upgrade procedures.

### 6. Crate-Specific Issues

During our development process, we encountered specific issues with the `bandersnatch_vrfs` crate:

- **Private Field Access**: We faced errors related to accessing the private field `h` in the `PiopParams` struct.
- **Type Mismatches**: There were issues with mismatched types when calling the `pedersen_vrf` function.

To address these issues, we made the following changes:

1. In the `sign_ring_vrf` method of the `SecretKey` struct:
   ```rust
   let (signature,secret_blinding) = pedersen_vrf(*blinding_base).sign_pedersen_vrf(t, ios, None, &self.0);
   ```

2. In the `verify_ring_vrf` method of the `RingVrfSignature` struct:
   ```rust
   pedersen_vrf(*blinding_base).verify_pedersen_vrf(t,ios.as_ref(),&self.signature) ?;
   ```

These changes involve dereferencing the `blinding_base` variable when passing it to the `pedersen_vrf` function. This ensures that the function receives the correct type (the dereferenced value of `blinding_base`) instead of a reference.

If you encounter similar issues, consider reviewing your use of the `bandersnatch_vrfs` crate and applying similar fixes.

Remember, the Polkadot and Substrate communities are excellent resources for troubleshooting. Don't hesitate to seek help on the official forums or Discord channels if you encounter persistent issues.

## Resolving Compilation Errors in executor_intf.rs

Before we proceed to deploying the parachain, it's important to address some compilation errors we encountered during the development process. Specifically, we faced issues in the `executor_intf.rs` file related to type mismatches and incorrect method signatures. Here's a summary of the changes we made to resolve these issues:

1. Updated import statements:
   ```rust
   use sp_storage::{ChildInfo, TrackedStorageKey};
   ```

2. Modified the `ValidationExternalities` implementation to match the `sc_executor::Externalities` trait definitions:
   ```rust
   impl sc_executor::Externalities for ValidationExternalities {
       fn storage(&self, key: &[u8]) -> Option<Vec<u8>> {
           self.storage.get(key).cloned()
       }

       fn storage_hash(&self, key: &[u8]) -> Option<Vec<u8>> {
           self.storage(key).map(|v| sp_core::blake2_128(&v).to_vec())
       }

       fn child_storage(&self, child_info: &sp_storage::ChildInfo, key: &[u8]) -> Option<Vec<u8>> {
           None
       }

       // ... (other method implementations)
   }
   ```

3. Updated method signatures to use `sp_storage::ChildInfo` and `sp_storage::TrackedStorageKey`:
   ```rust
   fn child_storage_root(&mut self, child_info: &sp_storage::ChildInfo, state_version: StateVersion) -> Vec<u8> {
       Vec::new()
   }

   fn get_whitelist(&self) -> Vec<sp_storage::TrackedStorageKey> {
       Vec::new()
   }

   fn set_whitelist(&mut self, new: Vec<sp_storage::TrackedStorageKey>) {}
   ```

These changes ensure that our parachain's execution environment correctly implements the required traits and provides the necessary functionality for the Polkadot runtime. When working on your own parachain, you may encounter similar issues. Always make sure to use the correct types and implement all required methods as specified by the traits you're working with.

## Recent Updates to executor_intf.rs

We've made additional changes to the `executor_intf.rs` file to further resolve compilation errors and improve our parachain's compatibility with the Polkadot runtime. Here's a summary of the latest modifications:

1. Updated import statements:
   ```rust
   use sp_core::{Blake2Hasher, storage::ChildInfo};
   use sc_executor::{
       sp_wasm_interface::HostFunctionRegistry,
       Externalities,
   };
   use sp_io::SubstrateHostFunctions;
   ```

2. Modified the `PvfHostFunctionsWrapper` implementation:
   ```rust
   impl sc_executor::HostFunctions for PvfHostFunctionsWrapper {
       fn host_functions() -> Vec<&'static dyn sc_executor::HostFunctionRegistry> {
           sp_io::SubstrateHostFunctions::host_functions()
       }
   }
   ```

3. Added missing methods to the `Externalities` implementation for `ValidationExternalities`:
   ```rust
   fn storage_changes_root(&mut self, _parent: &[u8]) -> Result<Option<Vec<u8>>, ()> {
       Ok(None)
   }

   fn register_overlay_stats(&mut self, _stats: &sp_externalities::OverlayedChangeStats) {}
   ```

These changes were necessary to ensure our parachain correctly implements all required traits and methods expected by the Polkadot runtime. By resolving these compilation errors, we've improved the compatibility of our parachain with the Polkadot ecosystem, which is crucial for successful deployment and operation.

Remember, when developing your own parachain, you may encounter similar issues. Always ensure that your implementation adheres to the latest Polkadot and Substrate specifications, and don't hesitate to consult the official documentation or community resources when facing compilation errors.

With these compilation errors resolved, we can now move on to the exciting process of deploying our parachain to a live network.

## Conclusion

Congratulations! You have successfully deployed a parachain on the Polkadot network. This tutorial covered the essential steps to set up your development environment, build your custom parachain, and register it on the network. Let's recap the key points:

1. Setting up the development environment with Rust and the Polkadot SDK
2. Creating and configuring a parachain project using the Substrate Parachain Template
3. Building the parachain and generating the necessary chain specifications
4. Registering the parachain through the parachain slot auction process
5. Resolving compilation errors and updating the execution environment

Remember, deploying a parachain is just the beginning of your journey in the Polkadot ecosystem. To further enhance your knowledge and capabilities, consider exploring these advanced topics:

- Cross-chain messaging using XCMP (Cross-Chain Message Passing)
- Implementing custom pallets for your parachain's specific use case
- Optimizing your parachain's performance and security
- Participating in the Polkadot governance process

For more in-depth information and the latest updates, refer to the official Polkadot documentation:

- [Polkadot Wiki](https://wiki.polkadot.network/)
- [Substrate Developer Hub](https://substrate.dev/)
- [Polkadot.js Documentation](https://polkadot.js.org/docs/)

Happy building, and welcome to the exciting world of Polkadot parachains! Your journey to revolutionizing blockchain interoperability starts here.
