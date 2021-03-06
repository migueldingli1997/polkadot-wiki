---
id: learn-accounts
title: Polkadot Accounts
sidebar_label: Polkadot Accounts
description: An explanation of accounts, indices, identity, and reaping
---

This document covers the basics of Polkadot and Kusama account addresses and how they exist on-chain. For a more in-depth explanation of the cryptography behind them, please see [the cryptography page](learn-cryptography).

## Address Format

The address format used in Substrate-based chains is SS58. SS58 is a modification of Base-58-check from Bitcoin with some minor modifications. Notably, the format contains an _address type_ prefix which identifies an address as belonging to a specific network.

For example:

- Polkadot addresses always start with the number 1.
- Kusama addresses always start with a capital letter like C, D, F, G, H, J...
- Generic Substrate addresses start with 5.

These prefixes are defined [here](https://github.com/paritytech/substrate/wiki/External-Address-Format-(SS58)).

It's important to understand that the different formats for different networks are merely different representations of the same public key in a private-public keypair generated by an address generation tool (see next section). This makes the addresses cross-Substrate-chain-compatible as long as you convert the format.

### For the Curious: How Prefixes Work

The [SS58 document](https://github.com/paritytech/substrate/wiki/External-Address-Format-(SS58)) states that:

- Polkadot has an address type of `00000000b`, so `0` in decimal.
- Kusama (Polkadot Canary) has an address type of `00000010b`, so `2` in decimal.
- Generic Substrate has `00101010b` as address type, which is `42` in decimal.

Because Base58-check alphabet has no number 0, the lowest value is indeed 1. So `00000000b` is 1 in Base58-check. If we try to [decode](https://www.better-converter.com/Encoders-Decoders/Base58Check-to-Hexadecimal-Decoder) a Polkadot address like `1FRMM8PEiWXYax7rpS6X4XZX1aAAxSWx1CrKTyrVYhV24fg`, the result is `000aff6865635ae11013a83835c019d44ec3f865145943f487ae82a8e7bed3a66b29d7`. The first byte is `00`, which is indeed `00000000` in binary and `0` in decimal and thus matches the address type of Polkadot.

Let's take a look at Substrate addresses. If we decode `5CK8D1sKNwF473wbuBP6NuhQfPaWUetNsWUNAAzVwTfxqjfr`, we get `2a0aff6865635ae11013a83835c019d44ec3f865145943f487ae82a8e7bed3a66b77e5`. The first byte is `2a` which when [converted from hex to decimal](https://www.rapidtables.com/convert/number/hex-to-decimal.html) is 42. 42 is `00101010` in binary, just as the SS58 document states.

Finally, let's look at Kusama addresses. Decoding `CpjsLDC1JFyrhm3ftC9Gs4QoyrkHKhZKtK7YqGTRFtTafgp` gives us `020aff6865635ae11013a83835c019d44ec3f865145943f487ae82a8e7bed3a66b0985` with the first byte being `02`, just as specified. If we try a Kusama address that starts with a completely different letter, like `J4iggBtsWsb61RemU2TDWDXTNHqHNfBSAkGvVZBtn1AJV1a`, we still get `02` as the first byte: `02f2d606a67f58fa0b3ad2b556195a0ef905676efd4e3ec62f8fa1b8461355f1142509`. It seems counterintuitive that some addresses always have the same prefix and others like Kusama can vary wildly, but it's just a quirk of Base58-check encoding.

## Obtaining and Managing an Address

The **most user-friendly** way to create a Kusama address is through the [Polkadot JS UI](https://polkadot.js.org/apps/#/accounts). Remember to back up the seed phrase used to generate your account - the accounts are stored only in your browser, so purging the cache will wipe your accounts as well. You would then have to recreate them using the seed phrase given to you by the UI - this will also restore all your previously held balances.

A **more convenient and recommended** method of keeping the accounts stored on your computer is using the [PolkadotJS extension](https://github.com/polkadot-js/extension). This extension remembers your accounts and allows you to clear your browser cache without fear. Still, don't forget to back up your seed phrase - if you lose access to this computer, or the extension somehow crashes beyond repair, the phrase will come in handy. 

Please note that as this keeps your accounts in the browser, it is not a safe place to keep large holdings. By definition, a browser is a "hot wallet" and susceptible to a wide range of attacks, so keep your funds in cold storage when dealing with non-trivial amounts. Creating cold storage is as simple as securely stashing away the seed phrase for your accounts and removing all traces of the accounts from your computer.

Other than the extension and the default UI, Polkadot and Kusama addresses can also be created with the [Subkey tool](https://github.com/paritytech/substrate/tree/master/bin/utils/subkey). Subkey is intended for users comfortable with using the command line and can seem intimidating, but is quite approachable. Follow the instructions in [this page](https://substrate.dev/docs/en/ecosystem/subkey). When used properly, Subkey is the **most secure** available method of creating an account.

There is also the very secure [Parity Signer](https://www.parity.io/signer/) but it currently only supports Kusama addresses, not Polkadot or generic Substrate.

Alternatively, you might find some wallets on the [Polkaproject.com page](http://www.polkaproject.com/) but bear in mind that these are **unaudited** and not officially affiliated with Web3 Foundation or the Polkadot project unless otherwise stated.

Hardware wallet integration from [Ledger](https://ledger.com) is coming soon.

## Existential Deposit and Reaping

When you generate an account (address), you only generate a _key_ which lets you access it. The account does not exist yet on-chain. For that, it needs the existential deposit - 0.01 KSM / DOT. This deposit is paid by the account sending you tokens. The one "creating" you pays for your creation.

Going below 0.01 causes an account to be _reaped_. Your account will be wiped from the blockchain's state to conserve space. You do not lose access to the reaped address - as long as you have your private key or recovery phrase, you can still use the address - but it needs a top-up of another existential deposit to be able to interact with the chain.

Here's another way to think about existential deposits. Ever notice those `Thumbs.db` files on Windows or `.DS_Store` files on Mac? Those are junk, they serve no specific purpose other than making previews a bit faster. If a folder is completely empty save for such a file, you can remove the folder to clear junk off your hard drive. That does not mean you lose access to this folder forever - you can always recreate it. You have the _key_, after all - you're the computer's owner. It just means you want to keep your computer clean until you maybe end up needing this folder again, and then recreate it. Your address is like this folder - it gets removed from the chain when nothing is in it, but gets put back when it has at least the existential deposit.

## Indices

A Kusama address can have an index. An index is like a short and easy to remember version of an address.

Indices are populated in order. Think of them like slots going from 0 to any arbitrary number:

`[0][1][2][3][4][5][6]...`

If slots 0-2 are populated by addresses A, B and C respectively, and I add an existential deposit to address X, that address will automatically be put into slot 3. Henceforth, you can send me money by just sending to `[3]` rather than remembering my whole address.

```js
[0][1][2][3][4][5][6]...
[A][B][C][X][ ][ ][ ]...
```

But what if an account gets reaped as explained above? In that case, the index is emptied. In other words, the slot frees up again. If someone creates a new account, they may end up using the same index another address was using before.

Because of this unreliability of indices, they **may not be present** in Polkadot mainnet.

## Identities

The _Identities_ pallet built into Kusama allows users to attach on-chain metadata to their accounts. This metadata can be verified by independent registrars to provide trustworthiness. To learn more about how to set or release an identity, how to define sub-accounts, or how to become a registrar, please read [this guide](learn-identity).
