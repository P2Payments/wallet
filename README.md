# wallet

Mobile self-custodial wallet for P2Payments, based on an [Aqua Wallet](https://github.com/AquaWallet/aqua-wallet) fork.

This repository exists because Aqua already provides a strong base for the settlement layer we need, while still allowing us to extend the product toward P2Payments-specific payment flows, settings management, and marketplace integrations.

## Why Aqua Wallet

We selected Aqua Wallet as the base for this fork for the following reasons:

- Native self-custodial support for Bitcoin and Liquid, with a single seed phrase backup.
- Lightning support with swap-to-Liquid flows through Boltz, reducing the need for users to manage channel liquidity directly.
- Support for multiple Liquid-based stablecoins, currently including USDT and DePix, which are important as final-settlement assets for integrable payment rails.
- Built-in swap capabilities between supported currencies.
- An existing external app marketplace model, which can be fully replaced by our own embedded mini app for `/settings`.
- Support for multiple wallets based on separate seed phrases, allowing the same user to connect, for example, to their own `/mono` instance and to one or more `/marketplace` accounts at the same time.
- Support for the Shamrock protocol, enabling simple connection to BTCPay Server.
- Strong long-term maintenance expectations, given Aqua's funding and reputation within the ecosystem.

## Planned changes

The current fork is intended to evolve into the P2Payments mobile settlement and configuration wallet.

Planned changes include:

- Adding self-custodial Polygon wallet support derived from the existing seed phrase, in order to support USDT on Polygon.
- If needed for future rails settling in USDT on other chains, extending support through USDT0-based interoperability, using Polygon as the main anchor network.
- Replacing Aqua's marketplace tab with a dedicated **Payments** tab powered by the `/dashboard` Nuxt mini app.
- Integrating exchange rates from yadio.io, which are more representative of real pricing in emerging markets than exchange-only sources such as Kraken.
- Continuously improving core functionality while regularly pulling upstream changes from Aqua.
- Removing the distinction between **Saving Account** (BTC) and **Spending Accounts** (other assets), in order to better fit our payment-oriented product model.
- Adding Russian and removing unnecessary languages, aligning the wallet with our default language set: EN, ES, PT, FR, IT, RU.
- Rebranding the application as part of the P2Payments product family.

## USDT on Polygon

One of the main planned additions is self-custodial USDT support on Polygon.

The goal is to give users access to a settlement asset that is widely usable across integrable payment rails, while preserving the same wallet ownership model already used for Bitcoin and Liquid assets.

The implementation is designed around:

- deriving Polygon wallet support from the same existing seed phrase, avoiding separate backup complexity for the user;  
- supporting USDT on Polygon as a first-class settlement asset;  
- using a Flutter EVM stack for Polygon account derivation, signing, and contract interaction, with `web3dart` and `onchainlabs_flutter` currently under evaluation;  
- minimizing user exposure to native-token operational complexity;  
- enabling network fee handling through an ERC-4337 paymaster flow, so fees can be abstracted from the normal user experience and charged in USDT instead of requiring manual MATIC management.  

This is intended to make USDT settlement operationally simpler for users who need stablecoin-based final settlement without introducing unnecessary wallet fragmentation.  

## `/dashboard` mini app integration

The `/dashboard` mini app will be integrated into the wallet through a WebView-based approach.

The security model is intentionally simple:

- Flutter remains responsible for custody of the seed phrase and private keys.
- The embedded Nuxt app does not receive direct access to secrets.
- A minimal capability bridge is exposed from Flutter to the WebView layer.
- That bridge is limited to the data and actions required for the payment UI, such as receiving addresses, basic wallet context, and signing requests.

This allows us to keep sensitive key material inside the native wallet layer while still building a flexible product interface in Nuxt for P2Payments-specific flows and settings.

## Scope

This repository should be read as the mobile wallet layer of the broader P2Payments architecture.

It is not just a generic wallet fork. Its purpose is to provide:

- self-custodial settlement for Bitcoin, Liquid assets, and future supported rails;
- secure connection to BTCPay Server and related payment infrastructure;
- an embedded interface for `/dashboard` and payment-related flows;
- a mobile foundation for users interacting with `/mono` instances and connected marketplace environments.
