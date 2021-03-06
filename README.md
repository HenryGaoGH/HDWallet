[![Build Status](https://travis-ci.com/essentiaone/HDWallet.svg?branch=develop)](https://travis-ci.com/essentiaone/HDWallet)

# HDWalletKit
HDWalletKit is a Swift framwork that enables you to create and use bitcoin HD wallet ([Hierarchical Deterministic Wallets](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)) in your own app.

You can check if the address generation is working right [here](https://iancoleman.io/bip39/).

## Features
- Mnemonic recovery phrease in [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)

## How to use

- Generate seed and convert it to mnemonic sentence.

```swift
let entropy = Data(hex: "000102030405060708090a0b0c0d0e0f")
let mnemonic = Mnemonic.create(entropy: entropy)
print(mnemonic)
// abandon amount liar amount expire adjust cage candy arch gather drum buyer

let seed = Mnemonic.createSeed(mnemonic: mnemonic)
print(seed.toHexString())
```

- PrivateKey and key derivation (BIP32, BIP44)

```swift
let masterPrivateKey = PrivateKey(seed: seed, network: .main)

// m/44'
let purpose = masterPrivateKey.derived(at: 44, hardens: true)

// m/44'/0'
let coinType = purpose.derived(at: 0, hardens: true)

// m/44'/0'/0'
let account = coinType.derived(at: 0, hardens: true)

// m/44'/0'/0'/0
let change = account.derived(at: 0)

// m/44'/0'/0'/0
let firstPrivateKey = change.derived(at: 0)
print(firstPrivateKey.publicKey.address)
```


- Create your wallet and generate address

```swift
let entropy = Data(hex: "000102030405060708090a0b0c0d0e0f")
let mnemonic = Mnemonic.create(entropy: entropy)
let seed = Mnemonic.createSeed(mnemonic: mnemonic)
let network: Network = .main(.bitcoin)
let wallet = Wallet(seed: seed, network: network)
let account = wallet.generateAccount()
print(account)
```

- Sign transaction by private key

```swift
let signer = EIP155Signer()
let rawTransaction1 = EthereumRawTransaction(
    value: Wei("10000000000000000")!,
    to: "0x91c79f31De5208fadCbF83f0a7B0A9b6d8aBA90F",
    gasPrice: 99000000000,
    gasLimit: 21000,
    nonce: 2
)
guard let signed = try? signer.hash(rawTransaction: rawTransaction1).toHexString() else { return }
print(signed)
```


## License
WalletKit is released under the [MIT License](https://github.com/essentiaone/HDWallet/blob/develop/LICENSE).
