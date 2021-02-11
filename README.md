# Hedera Hashgraph Extension Wallet

This wallet was created for the Hedera Hashgraph hackathon. It supports all hashgraph actions such as transfer, token create, etc.

## Installation

- Grab the latest release from this repository
- Open up chrome and navigate to `chrome://extensions/`
- Drag the zip file onto that page
- Open the wallet from the top right extension icon, and import your accounts.

## Application integration

- `$ npm i -S @hashgraph/sdk@^2`

```
const { Client, TransferTransaction } = require("@hashgraph/sdk");

let wallet;

document.addEventListener('hederaWalletLoaded', async () => {
    // Grab the injected wallet from the window object
    wallet = window.wallet;

    // Testnet, Mainnet, or Previewnet
    const account = await wallet.login('Testnet');

    // Instantiate a client
    const client = Client.forTestnet();

    // Set the operator for signing to the signer from the wallet
    client.setOperatorWith(
        account.id,
        account.publicKey,
        wallet.getTransactionSigner()
    );

    // You can now use any Hashgraph SDK transaction builders,
    // and execute using this client.
    const executed = await new TransferTransaction()
        .addHbarTransfer(wallet.account.id, Hbar.fromTinybars(-1))
        .addHbarTransfer('some.other.id', Hbar.fromTinybars(1))
        .signWithOperator(client)
        .execute(client);

    console.log(await executed.getReceipt(client));
})
```

### Injected APIs

#### Account Object
```
{
    id:string           // Example: 0.0.123456
    publicKey:string,   // Example: 302a30050....
    network:string,     // Example: Testnet
}
```

#### APIs

- `wallet.login(network:string)` - Requests a login from the user
- `wallet.logout()` - Logs out of the user's wallet
- `wallet.account` - Will be populated with an account instance after calling `login()`
- `wallet.addAccount(network:string, id:string, privateKey:string)` - Requests that the user add
    a specific account to their wallet. This is a utility for account creation processes.
- `wallet.getTransactionSigner()` - Returns a transaction signer that can be used with
    the SDK's `client.setOperatorWith`

_Note: Logins persist past page reload. When the page first loads you can check `wallet.account` to
see if there is already a login permission in place._
