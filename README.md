# Token Contract on Solana

This project is a Solana program for minting and transferring tokens using the Anchor framework.

## Prerequisites

- [Rust](https://www.rust-lang.org/tools/install)
- [Solana CLI](https://docs.solana.com/cli/install-solana-cli-tools)
- [Anchor](https://project-serum.github.io/anchor/getting-started/installation.html)
- Node.js (for running Solana local validator)

## Setup

1. **Install Rust and Solana CLI**

    Follow the instructions on the [Rust website](https://www.rust-lang.org/tools/install) to install Rust.

    Install the Solana CLI by following the instructions [here](https://docs.solana.com/cli/install-solana-cli-tools).

2. **Install Anchor**

    Follow the instructions on the [Anchor website](https://project-serum.github.io/anchor/getting-started/installation.html) to install Anchor.

3. **Clone the Repository**

    ```sh
    git clone <repository_url>
    cd <repository_directory>
    ```

4. **Install Dependencies**

    ```sh
    anchor build
    ```

5. **Start a Local Solana Test Validator**

    In a separate terminal window, start a local Solana test validator:

    ```sh
    solana-test-validator
    ```

## Deployment

1. **Configure the Solana CLI**

    Set the CLI to use the local cluster:

    ```sh
    solana config set --url localhost
    ```

2. **Build the Project**

    ```sh
    anchor build
    ```

3. **Deploy the Program**

    ```sh
    anchor deploy
    ```

    This command will compile the program, deploy it to the local testnet, and generate the necessary IDL (Interface Description Language) files.

## Testing

To test the smart contract, we will use the `anchor test` command.

1. **Write Tests**

    In the `tests` directory, create a test file `token_contract.js` with the following content:

    ```javascript
    const anchor = require("@project-serum/anchor");
    const { SystemProgram } = anchor.web3;

    describe("token_contract", () => {
      // Configure the client to use the local cluster.
      const provider = anchor.Provider.local();
      anchor.setProvider(provider);

      const program = anchor.workspace.TokenContract;
      let mint = null;
      let fromTokenAccount = null;
      let toTokenAccount = null;

      it("Mints and Transfers tokens", async () => {
        // Generate a new mint and token accounts
        mint = anchor.web3.Keypair.generate();
        fromTokenAccount = await mint.createAccount(provider.wallet.publicKey);
        toTokenAccount = await mint.createAccount(provider.wallet.publicKey);

        // Mint tokens
        await program.rpc.mintToken({
          accounts: {
            mint: mint.publicKey,
            tokenAccount: fromTokenAccount,
            authority: provider.wallet.publicKey,
            tokenProgram: anchor.utils.token.TOKEN_PROGRAM_ID,
          },
          signers: [],
        });

        // Transfer tokens
        await program.rpc.transferToken({
          accounts: {
            from: fromTokenAccount,
            to: toTokenAccount,
            fromAuthority: provider.wallet.publicKey,
            tokenProgram: anchor.utils.token.TOKEN_PROGRAM_ID,
          },
          signers: [],
        });

        const fromTokenAccountInfo = await mint.getAccountInfo(fromTokenAccount);
        const toTokenAccountInfo = await mint.getAccountInfo(toTokenAccount);

        console.log("From Token Account:", fromTokenAccountInfo.amount.toNumber());
        console.log("To Token Account:", toTokenAccountInfo.amount.toNumber());
      });
    });
    ```

2. **Run Tests**

    ```sh
    anchor test
    ```

    This will execute the test script, which will mint and transfer tokens, then check the resulting token balances to ensure the operations were successful.

## Program Structure

### `program/src/lib.rs`

This file contains the main logic of the program, including the `mint_token` and `transfer_token` functions.

### `programs/src/lib.rs`

This file defines the program context and the account structures.

## Conclusion

You have now set up, deployed, and tested a Solana program for minting and transferring tokens using the Anchor framework. If you have any questions or run into issues, refer to the official [Anchor documentation](https://project-serum.github.io/anchor/).

