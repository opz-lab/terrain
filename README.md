# Terrain

<a href="https://www.npmjs.com/package/@terra-money/terrain">
  <img src="img/terrain-banner.png" alt="Terrain banner" />
</a>

---

<p align="center">
  <b>Terrain</b> - A development environment for seamless smart contract development on the interchain.
</p>

<div align="center">
  <a href="https://oclif.io"><img src="https://img.shields.io/badge/cli-oclif-brightgreen.svg" height="23px" /></a>
  <a href="https://npmjs.org/package/@terra-money/terrain"><img src="https://img.shields.io/npm/v/@terra-money/terrain" height="23px" /></a>
  <a href="https://npmjs.org/package/@terra-money/terrain"><img src="https://img.shields.io/npm/dm/@terra-money/terrain" height="23px" /></a>
</div>

---

Terrain allows you to:

- Scaffold a template smart contract and frontend for interchain app development.
- Dramatically simplify the development and deployment process.

Terrain is **not**:

- A fully-featured command-line interface (CLI). If you need a fully-featured client for Terra, use <a href="https://docs.terra.money/develop/terrad/using-terrad" target="_blank">terrad</a>.
- A Light Client Daemon (LCD). You will still need an RPC endpoint to deploy your contract. A local chain like <a href="https://github.com/terra-money/LocalTerra#readme" target="_blank">LocalTerra</a> is the recommended option for this.

---

# Table of contents

<!-- toc -->
* [Terrain](#terrain)
* [Table of contents](#table-of-contents)
* [Setup](#setup)
* [Getting Started](#getting-started)
* [Migrating CosmWasm Contracts on Terra](#migrating-cosmwasm-contracts-on-terra)
* [Use Terrain Main Branch Locally](#use-terrain-main-branch-locally)
* [Terrain Commands](#terrain-commands)
<!-- tocstop -->

# Setup

## Download LocalTerra

For testing purposes, we recommend to install and run LocalTerra on your personal computer. Instructions on how to get LocalTerra up and running can be found in the <a href="https://github.com/terra-money/LocalTerra#readme" target="_blank">
LocalTerra documentation</a>.

<sub>**Note:** _If you are using a Mac with an M1 chip, you might need to update your Docker Desktop due to the <a href="https://github.com/docker/for-mac/issues/5561" target="_blank">qemu bug</a>._</sub>

Once all dependencies have been installed, do the following:

1. Clone the LocalTerra repo.

```
git clone https://github.com/terra-money/LocalTerra.git
```

2. Navigate to the newly created `LocalTerra` directory.

```
cd LocalTerra
```

3. Spin up an instance of the environment with `docker-compose`.

```
docker-compose up
```

## Setup Rust

While WASM smart contracts can be written in any programming language, **it is strongly recommended that you use Rust**, as it is the only language for which mature libraries and tooling exist for CosmWasm. To complete this tutorial, install the latest version of Rust by following the instructions <a href="https://www.rust-lang.org/tools/install" target="_blank">here</a>. Once Rust is installed on your computer, do the following:

1. Set the default release channel used to update Rust to stable.

```sh
rustup default stable
```

2. Add wasm as the compilation target.

```sh
rustup target add wasm32-unknown-unknown
```

3. Install the necessary dependencies for generating contracts.

```sh
cargo install cargo-run-script
```

## Install Node JS and NPM

To run Terrain, you will need to install version 16 of Node.js and download Node Package Manager (npm). It is recommend that you install [Node.js v16 (LTS)](https://nodejs.org/en/download/). If you download the LTS version of Node.js v16, npm will be automatically installed along with your download.

# Getting Started

Now that you have completed the initial setup, generate your first smart contract using the procedure described below.

1. Install the terrain package globally.

```sh
npm install -g @terra-money/terrain
```

<sub>**Note:** _If you would like to install terrain locally, you can execute the command `npm install @terra-money/terrain`, without the `-g` flag, while in the directory in which you would like to be able to use the package. You can then execute any terrain commands by prefixing them with `npx`. For example, to scaffold a new project named `my-terra-dapp` with a locally installed terrain package, you would use the command `npx terrain new my-terra-dapp`._</sub>

2. Generate your smart contract and corresponding frontend templates.

```sh
terrain new my-terra-dapp
```

3. After the project is generated and all necessary Node dependencies are installed, navigate to the new `my-terra-dapp` directory to interact with your app.

```sh
cd my-terra-dapp
```

## Project Structure

The `terrain new` command generates a project that contains a template smart contract, which is named after the specified app name, `my-terra-dapp`, and a corresponding frontend. Other supporting files are generated to provide further functionality. You may view the project structure below.

```
.
├── contracts              # the smart contract directory
│   ├── my-terra-dapp      # template smart contract
│   └── ...
├── frontend               # template frontend application
├── lib                    # predefined task and console functions
├── tasks                  # predefined tasks
├── keys.terrain.js        # keys for signing transactions
├── config.terrain.json    # config for connections and contract deployments
└── refs.terrain.json      # deployed code and contract references
```

## Deployment

The `terrain deploy` command does the following:

- Builds, optimizes, and stores the wasm code on the blockchain.
- Instantiates the contract.

To deploy your new my-terra-dapp smart contract, run the following command in the terminal.

```sh
terrain deploy my-terra-dapp --signer test1
```

In this case, we specify one of the <a href="https://github.com/terra-money/LocalTerra#accounts" target="_blank">preconfigured accounts</a> with balances on LocalTerra, `test1`, as our signer. The signer account will be responsible for paying the gas fee associated with deploying the contract to the Terra blockchain and will be assigned as the owner of the project.

You can also specify the network on which you would like to deploy your contract by adding the `--network` flag. If the network is not specified, as is the case in our above example, your contract will be deployed to `localterra` by default. If your deployment command in the prior step resulted in an error, you will need to ensure that LocalTerra is up and running in the background and that you have properly spelled out your contract name and are utilizing the appropriate Terrain command. You may also deploy to `mainnet`, the live Terra blockchain, as well as `testnet`, a network similar to mainnet used for testing.

### Step-by-step Deployment

You can also execute the build, optimize, store, and instantiate processes separately by executing the following commands in sequential order.

1. [`terrain contract:build CONTRACT`](#terrain-contractbuild-contract)
2. [`terrain contract:optimize CONTRACT`](#terrain-contractoptimize-contract)
3. [`terrain contract:store CONTRACT`](#terrain-contractstore-contract)
4. [`terrain contract:instantiate CONTRACT`](#terrain-contractinstantiate-contract)

Afterward, you will have to run the `terrain sync-refs` command in your project directory to sync the `refs.terrain.json` file in the app root directory to the frontend directory. This file contains references to all contracts in the project which have been stored on any Terra network.

<br/>

### Deploying on Testnet or Mainnet

The predefined accounts in the `keys.terrain.js` file shown below can be used as signers on `testnet`. We will demonstrate how to deploy your smart contract utilizing the preconfigured `custom_tester_1` account. You may also add a personal account to the `keys.terrain.js` file by adding the account name as well as its corresponding private key. You can then use that account as the signer specifying the account name after the `--signer` flag in the `terrain deploy` command.

<sub>**Warning:** _Utilizing a personal account for deployment requires the use of a private key or mnemonic. These are private keys that are generated upon the creation of your personal wallet. Saving or utilizing these keys on your personal computer may expose them to malicious actors who could gain access to your personal wallet if they are able to obtain them. You can create a wallet solely for testing purposes to eliminate risk. Alternatively, you can store your private keys as secret environment variables which you can then reference utilizing `process.env.SECRET_VAR` in `keys.terrain.json`. Use your private key or mnemonic at your own discretion._</sub>

```js
// can use `process.env.SECRET_MNEMONIC` or `process.env.SECRET_PRIV_KEY`
// to populate secret in CI environment instead of hardcoding

module.exports = {
  custom_tester_1: {
    mnemonic:
      'shiver position copy catalog upset verify cheap library enjoy extend second peasant basic kit polar business document shrug pass chuckle lottery blind ecology stand',
  },
  custom_tester_2: {
    privateKey: 'fGl1yNoUnnNUqTUXXhxH9vJU0htlz9lWwBt3fQw+ixw=',
  },
};
```

Prior to deploying your contract, ensure that your signer wallet contains the funds needed to pay for associated transaction fees. You can request funds from the <a href="https://faucet.terra.money/" target="_blank">Terra Testnet Faucet</a> by submitting the wallet address of the account where you would like to receive the funds and clicking on the `Send me tokens` button.

You can retrieve the wallet address associated with the `custom_tester_1` account by executing the `terrain console` command in your terminal while in your project directory.

```sh
terrain console

terrain > wallets.custom_tester_1.key.accAddress
'terra1qd9fwwgnwmwlu2csv49fgtum3rgms64s8tcavp'
```

After you have received the Luna tokens from the Terra Testnet Faucet, query the balance of your account by utilizing the following command in the terrain console.

```sh
terrain > (await client.bank.balance(wallets.custom_tester_1.key.accAddress))[0]
```

Finally, exit the terrain console and deploy the `counter` smart contract to testnet with the `custom_tester_1` account as the signer.

```sh
terrain deploy counter --signer custom_tester_1 --network testnet
```

## Initializing the Frontend Template

After deployment, the `refs.terrain.json` file will be updated in the project directory as well as the `frontend/src` directory. These files contain references to all contracts inside of your project which have been stored on any Terra network. This information is used by terrain's utility functions and also the frontend template. An example of `refs.terrain.json` can be found below:

```json
{
  "localterra": {
    "counter": {
      "codeId": "1",
      "contractAddresses": {
        "default": "terra18vd8fpwxzck93qlwghaj6arh4p7c5n896xzem5"
      }
    }
  },
  "testnet": {
    "counter": {
      "codeId": "18160",
      "contractAddresses": {
        "default": "terra15faphq99pap3fr0dwk46826uqr2usve739l7ms"
      }
    }
  }
}
```

<sub> **Important:** _If you have initialized the contract without using the `terrain deploy` command or have manually changed the `refs.terrain.json` file in the project directory, you will need to sync the references to the `frontend/src` directory in order to ensure frontend functionality. To do so, use the `terrain sync-refs` command._</sub>

After you have synced the contract references, navigate to the `frontend` directory and start the application.

1. Navigate to the `frontend` directory.

```sh
cd frontend
```

2. Start the application.

```sh
npm run start
```

<sub> **Note:** _Switching networks in your Terra Station extension will result in a change in reference to the contract address which corresponds with the new network._</sub>

## Run Contract Functions with Terrain

Once you have successfully deployed your project, you can interact with the deployed contract and the underlying blockchain by utilizing functions defined in the `lib/index.js` file. You may also create your own abstractions in this file for querying or executing transactions.

You can call the functions defined in `lib/index.js` inside of the `terrain console`. An example using the template counter smart contract is shown below.

```sh
terrain console
terrain > await lib.getCountQuery()
{ count: 0 }
terrain > await lib.increment()
terrain > await lib.getCountQuery()
{ count: 1 }
```

You may also specify which network you would like to interact with by utilizing the `--network` flag with the `terrain console` command.

```
terrain console --network NETWORK
```

## Creating Tasks

You can use the functions available inside of the `lib/index.js` file to create tasks. Tasks are used in order to automate the execution of sequential functions or commands. An example task is provided for you in the `tasks/example-with-lib.js` file in your project directory.

```js
// tasks/example-with-lib.js

const { task } = require('@terra-money/terrain');
const lib = require('../lib');

task(async (env) => {
  const { getCountQuery, increment } = lib(env);
  console.log('count 1 = ', await getCountQuery());
  await increment();
  console.log('count 2 = ', await getCountQuery());
});
```

To run the example task shown above, which is located in the `tasks/example-with-lib.js` file, run the following command in the terminal.

```sh
terrain task:run example-with-lib
```

In order to create a new task, run the following command replacing `<task-name>` with the desired name for your new task.

```sh
terrain task:new <task-name>
```

If you would like to use JavaScript in your functions or tasks, you can import [Feather.js](https://github.com/terra-money/feather.js). The `tasks/example-custom-logic.js` file contains an example of a task that uses Terra.js functionality. To learn more about Terra.js, view the <a href="https://terra-money.github.io/feather.js/" target="_blank">Terra.js documentation</a>.

```js
// tasks/example-custom-logic.js

const { task, terrajs } = require('@terra-money/terrain');

// terrajs is basically re-exported terra.js (https://terra-money.github.io/feather.js/)

task(async ({ wallets, refs, config, client }) => {
  console.log('creating new key');
  const key = terrajs.MnemonicKey();
  console.log('private key', key.privateKey.toString('base64'));
  console.log('mnemonic', key.mnemonic);
});
```

## Scripting deployments

As of Terrain 0.4.0 it is possible to deploy and instantiate contracts from tasks. This can be useful for multi-contract, or multi-stage deployments.

```js
const { task } = require('@terra-money/terrain');

task(async ({ wallets, client, deploy }) => {
  // First deploy the counter smart contract.
  await deploy.storeCode('counter', wallets.test1);
  const counterAddress = await deploy.instantiate(
    // Contract name
    'counter',
    // Signer
    wallets.test1,
    {
      // Contract admin
      admin: wallets.test1.key.accAddress,
    },
  );

  // Now deploy a CW20 with the counter contract set as the minter in instantiation.
  await deploy.storeCode('cw20-base', wallets.test1);
  const cw20Address = await deploy.instantiate('cw20-base', wallets.test1, {
    admin: wallets.test1.key.accAddress,
    // Custom instantiation message.
    // with no message provided the default from config.terrain will be used.
    init: {
      name: 'counter',
      symbol: 'CTR',
      decimals: 6,
      initial_balances: [],
      mint: {
        minter: counterAddress,
      },
    },
  });

  // Update the CW20 address in counter.
  // Note: It's important to use the address returned by deploy.instantiate
  // Refs are only read into memory at the start of the task.
  await client.execute(counterAddress, wallets.test1, {
    update_token: { token: cw20Address },
  });

  console.log(`CW20 Address: ${cw20Address}`);
});
```

It is possible to tell Terrain to use a custom deploy task instead of the default deploy process. To do this, add the following to the `global` section in `config.terrain.json`:

```json
"contracts": {
  "counter": {
    "deployTask": "deploy_counter"
  }
}
```

Now instead of running `terrain task:run deploy_counter` you can run `terrain deploy counter`.

# Migrating CosmWasm Contracts on Terra

On Terra, it is possible to initialize a contract as migratable. This functionality allows the administrator to upload a new version of the contract and then send a migrate message to move to the new code. Contracts that have been deployed before implementing the following changes will not be able to be migrated and implemented changes will only be realized when redeploying the contract.

The <a href="https://docs.terra.money/develop/terrain/contract-migration" target="_blank">contract migration tutorial</a> builds on top of the Terrain Quick Start Guide and walks you through a contract migration.

## Adding MigrateMsg to the Contract

In order for a contract to be migratable, it must be able to handle a `MigrateMsg` transaction.

To implement support for `MigrateMsg`, add the message to the `msg.rs` file. To do so, navigate to `msg.rs` and place the following code just above the `InstantiateMsg` struct.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct MigrateMsg {}
```

With `MigrateMsg` defined, update the `contract.rs` file. First, update the import from `crate::msg` to include `MigrateMsg`.

```rust
use crate::msg::{CountResponse, ExecuteMsg, InstantiateMsg, QueryMsg, MigrateMsg};
```

Next, add the following method above `instantiate`.

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn migrate(_deps: DepsMut, _env: Env, _msg: MigrateMsg) -> StdResult<Response> {
    Ok(Response::default())
}
```

## Migrating the Contract

Adding the MigrateMsg to the smart contract allows the contract's administrator to migrate the contract in the future. When we deploy our contract, the wallet address of the signer will be automatically designated as the contract administrator. In the following command, the contract is deployed with the preconfigured LocalTerra `test1` wallet as the signer and administrator of our counter contract.

```sh
terrain deploy counter --signer test1
```

If you decide to make changes to the deployed contract, you can migrate to the updated code by executing the following command.

```sh
terrain contract:migrate counter --signer test1
```

If you would like to specify the address of the desired administrator for your smart contract, you may use the `--admin-address` flag in the deploy command followed by the wallet address of the desired administrator.

```sh
terrain deploy counter --signer test1 --admin-address <insert-admin-wallet-address>
```

# Use Terrain Main Branch Locally

In some cases, the latest features or bug fixes may be integrated into the main branch of the <a href="https://github.com/terra-money/terrain" target="_blank">Terrain Github repo</a>, but not yet released to the corresponding <a href="https://www.npmjs.com/package/@terra-money/terrain" target="_blank">npm package</a>. Subsequently, you may want to use the latest version of Terrain available on Github before it has been released to npm. The below described method may also be used if you are interested in developing on and contributing to Terrain.

<sub>**Warning:** _Features and bug fixes that are implemented on the latest version of Terrain may still be subject to testing. As such, you should only use the main branch of the Terrain github repo in exceptional circumstances. In all other cases, use the npm package._</sub>

To use the main branch of the Terrain repo on your local machine, follow the procedure below.

1. Clone the repo.

```
git clone --branch main --depth 1 https://github.com/terra-money/terrain
```

2. Navigate to the project folder.

```
cd terrain
```

3. Inside the project folder, install all necessary node dependencies.

```
npm install
```

4.  Run the `npm link` command to set up the local repository as your global terrain instance.

```
npm link
```

If you would like to witness your changes immediately upon saving them, you may execute the following command while in your local Terrain directory and allow it to run in a tab in your terminal.

```
npm run watch
```

To unlink the terrain command from the cloned repository and revert back to the default functionality, you can execute the below command.

```
npm unlink terrain
```

---

# Terrain Commands

<!-- commands -->
* [`terrain console`](#terrain-console)
* [`terrain contract:build CONTRACT`](#terrain-contractbuild-contract)
* [`terrain contract:instantiate CONTRACT`](#terrain-contractinstantiate-contract)
* [`terrain contract:migrate CONTRACT`](#terrain-contractmigrate-contract)
* [`terrain contract:new NAME`](#terrain-contractnew-name)
* [`terrain contract:optimize CONTRACT`](#terrain-contractoptimize-contract)
* [`terrain contract:schema CONTRACT`](#terrain-contractschema-contract)
* [`terrain contract:store CONTRACT`](#terrain-contractstore-contract)
* [`terrain contract:updateAdmin CONTRACT ADMIN`](#terrain-contractupdateadmin-contract-admin)
* [`terrain deploy CONTRACT`](#terrain-deploy-contract)
* [`terrain help [COMMAND]`](#terrain-help-command)
* [`terrain new NAME`](#terrain-new-name)
* [`terrain query CONTRACT MSG`](#terrain-query-contract-msg)
* [`terrain sync-refs`](#terrain-sync-refs)
* [`terrain task:new [TASK]`](#terrain-tasknew-task)
* [`terrain task:run [TASK]`](#terrain-taskrun-task)
* [`terrain test CONTRACT-NAME`](#terrain-test-contract-name)
* [`terrain test:coverage [CONTRACT-NAME]`](#terrain-testcoverage-contract-name)
* [`terrain tx CONTRACT MSG`](#terrain-tx-contract-msg)
* [`terrain wallet:new`](#terrain-walletnew)

## `terrain console`

Start a repl console that provides context and convenient utilities to interact with the blockchain and your contracts.

```
USAGE
  $ terrain console [--signer <value>] [--network mainnet|testnet|localterra] [--prefix <value>] [--refs-path
    <value>] [--keys-path <value>]

FLAGS
  --keys-path=<value>  [default: ./keys.terrain.js]
  --network=<option>   [default: localterra] network to deploy to from config.terrain.json
                       <options: mainnet|testnet|localterra>
  --prefix=<value>     [default: terra] address prefix of target chain, all chains supported by Station are supported by
                       terrain
  --refs-path=<value>  [default: ./refs.terrain.json]
  --signer=<value>     [default: test1]

DESCRIPTION
  Start a repl console that provides context and convenient utilities to interact with the blockchain and your
  contracts.
```

_See code: [src/commands/console.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/console.ts)_

## `terrain contract:build CONTRACT`

Build wasm bytecode.

```
USAGE
  $ terrain contract:build [CONTRACT] [--config-path <value>]

FLAGS
  --config-path=<value>  [default: ./config.terrain.json]

DESCRIPTION
  Build wasm bytecode.
```

_See code: [src/commands/contract/build.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/contract/build.ts)_

## `terrain contract:instantiate CONTRACT`

Instantiate the contract.

```
USAGE
  $ terrain contract:instantiate [CONTRACT] [--instance-id <value>] [--code-id <value>] [--signer <value>] [--network
    mainnet|testnet|localterra] [--prefix <value>] [--refs-path <value>] [--keys-path <value>]

FLAGS
  --code-id=<value>      specific codeId to instantiate
  --instance-id=<value>  [default: default] enable management of multiple instances of the same contract
  --keys-path=<value>    [default: ./keys.terrain.js]
  --network=<option>     [default: localterra] network to deploy to from config.terrain.json
                         <options: mainnet|testnet|localterra>
  --prefix=<value>       [default: terra] address prefix of target chain, all chains supported by Station are supported
                         by terrain
  --refs-path=<value>    [default: ./refs.terrain.json]
  --signer=<value>       [default: test1]

DESCRIPTION
  Instantiate the contract.
```

_See code: [src/commands/contract/instantiate.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/contract/instantiate.ts)_

## `terrain contract:migrate CONTRACT`

Migrate the contract.

```
USAGE
  $ terrain contract:migrate [CONTRACT] [--no-rebuild] [--instance-id <value>] [--code-id <value>] [--signer <value>]
    [--network mainnet|testnet|localterra] [--prefix <value>] [--refs-path <value>] [--keys-path <value>]

FLAGS
  --code-id=<value>      specific codeId to instantiate
  --instance-id=<value>  [default: default] enable management of multiple instances of the same contract
  --keys-path=<value>    [default: ./keys.terrain.js]
  --network=<option>     [default: localterra] network to deploy to from config.terrain.json
                         <options: mainnet|testnet|localterra>
  --no-rebuild           deploy the wasm bytecode as is.
  --prefix=<value>       [default: terra] address prefix of target chain, all chains supported by Station are supported
                         by terrain
  --refs-path=<value>    [default: ./refs.terrain.json]
  --signer=<value>       [default: test1]

DESCRIPTION
  Migrate the contract.
```

_See code: [src/commands/contract/migrate.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/contract/migrate.ts)_

## `terrain contract:new NAME`

Generate new contract.

```
USAGE
  $ terrain contract:new [NAME] [--path <value>] [--commitHash <value>] [--authors <value>]

FLAGS
  --authors=<value>     [default: Terra Money <core@terra.money>]
  --commitHash=<value>  [default: 9fa5b9b38fca4f99239ac28be48a6e1f0a4d30c8]
  --path=<value>        [default: contracts] path to keep the contracts

DESCRIPTION
  Generate new contract.

EXAMPLES
  $ terrain code:new awesome_contract

  $ terrain code:new awesome_contract --path path/to/dapp

  $ terrain code:new awesome_contract --path path/to/dapp --authors "ExampleAuthor<example@email.domain>"
```

_See code: [src/commands/contract/new.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/contract/new.ts)_

## `terrain contract:optimize CONTRACT`

Optimize wasm bytecode.

```
USAGE
  $ terrain contract:optimize [CONTRACT]

DESCRIPTION
  Optimize wasm bytecode.
```

_See code: [src/commands/contract/optimize.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/contract/optimize.ts)_

## `terrain contract:schema CONTRACT`

Generate contract schema.

```
USAGE
  $ terrain contract:schema [CONTRACT]

DESCRIPTION
  Generate contract schema.
```

_See code: [src/commands/contract/schema.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/contract/schema.ts)_

## `terrain contract:store CONTRACT`

Store code on chain.

```
USAGE
  $ terrain contract:store [CONTRACT] [--no-rebuild] [--code-id <value>] [--signer <value>] [--network
    mainnet|testnet|localterra] [--prefix <value>] [--refs-path <value>] [--keys-path <value>]

FLAGS
  --code-id=<value>    specific codeId to instantiate
  --keys-path=<value>  [default: ./keys.terrain.js]
  --network=<option>   [default: localterra] network to deploy to from config.terrain.json
                       <options: mainnet|testnet|localterra>
  --no-rebuild         deploy the wasm bytecode as is.
  --prefix=<value>     [default: terra] address prefix of target chain, all chains supported by Station are supported by
                       terrain
  --refs-path=<value>  [default: ./refs.terrain.json]
  --signer=<value>     [default: test1]

DESCRIPTION
  Store code on chain.
```

_See code: [src/commands/contract/store.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/contract/store.ts)_

## `terrain contract:updateAdmin CONTRACT ADMIN`

Update the admin of a contract.

```
USAGE
  $ terrain contract:updateAdmin [CONTRACT] [ADMIN] [--signer <value>] [--network mainnet|testnet|localterra] [--prefix
    <value>] [--instance-id <value>] [--refs-path <value>] [--keys-path <value>]

FLAGS
  --instance-id=<value>  [default: default] enable management of multiple instances of the same contract
  --keys-path=<value>    [default: ./keys.terrain.js]
  --network=<option>     [default: localterra] network to deploy to from config.terrain.json
                         <options: mainnet|testnet|localterra>
  --prefix=<value>       [default: terra] address prefix of target chain, all chains supported by Station are supported
                         by terrain
  --refs-path=<value>    [default: ./refs.terrain.json]
  --signer=<value>       [default: test1]

DESCRIPTION
  Update the admin of a contract.
```

_See code: [src/commands/contract/updateAdmin.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/contract/updateAdmin.ts)_

## `terrain deploy CONTRACT`

Build wasm bytecode, store code on chain and instantiate.

```
USAGE
  $ terrain deploy [CONTRACT] [--memo <value>] [--no-rebuild] [--instance-id <value>] [--frontend-refs-path
    <value>] [--admin-address <value>] [--no-sync <value>] [--signer <value>] [--network mainnet|testnet|localterra]
    [--prefix <value>] [--refs-path <value>] [--keys-path <value>]

FLAGS
  --admin-address=<value>       set custom address as contract admin to allow migration.
  --frontend-refs-path=<value>  [default: ./frontend/src/]
  --instance-id=<value>         [default: default] enable management of multiple instances of the same contract
  --keys-path=<value>           [default: ./keys.terrain.js]
  --memo=<value>                [default: terrain]
  --network=<option>            [default: localterra] network to deploy to from config.terrain.json
                                <options: mainnet|testnet|localterra>
  --no-rebuild                  deploy the wasm bytecode as is.
  --no-sync=<value>             don't attempt to sync contract refs to frontend.
  --prefix=<value>              [default: terra] address prefix of target chain, all chains supported by Station are
                                supported by terrain
  --refs-path=<value>           [default: ./refs.terrain.json]
  --signer=<value>              [default: test1]

DESCRIPTION
  Build wasm bytecode, store code on chain and instantiate.
```

_See code: [src/commands/deploy.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/deploy.ts)_

## `terrain help [COMMAND]`

display help for terrain

```
USAGE
  $ terrain help [COMMAND] [--all]

ARGUMENTS
  COMMAND  command to show help for

FLAGS
  --all  see all commands in CLI

DESCRIPTION
  display help for terrain
```

_See code: [@oclif/plugin-help](https://github.com/oclif/plugin-help/blob/v3.2.18/src/commands/help.ts)_

## `terrain new NAME`

Create new dapp from template.

```
USAGE
  $ terrain new [NAME] [--path <value>] [--framework react|vue|svelte|next|vite|lit] [--version <value>]
    [--authors <value>]

FLAGS
  --authors=<value>     [default: Terra Money <core@terra.money>]
  --framework=<option>  [default: react] Choose the frontend framework you want to use. Non-react framework options have
                        better wallet-provider support but less streamlined contract integration.
                        <options: react|vue|svelte|next|vite|lit>
  --path=<value>        [default: .] Path to create the workspace
  --version=<value>     [default: 1.0]

DESCRIPTION
  Create new dapp from template.

EXAMPLES
  $ terrain new awesome-dapp

  $ terrain new awesome-dapp --path path/to/dapp

  $ terrain new awesome-dapp --path path/to/dapp --authors "ExampleAuthor<example@email.domain>"

  $ terrain new awesome-dapp --path path/to/dapp --framework vue --authors "ExampleAuthor<example@email.domain>"
```

_See code: [src/commands/new.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/new.ts)_

## `terrain query CONTRACT MSG`

Query contracts on the interchain

```
USAGE
  $ terrain query [CONTRACT] [MSG] [--network mainnet|testnet|localterra] [--config-path <value>]

ARGUMENTS
  CONTRACT  Contract address
  MSG       Query msg to be performed in JSON format

FLAGS
  --config-path=<value>  [default: ./config.terrain.json]
  --network=<option>     [default: localterra] network to deploy to from config.terrain.json
                         <options: mainnet|testnet|localterra>

DESCRIPTION
  Query contracts on the interchain

EXAMPLES
  $ terrain query terra1..fx9fs '{"get_count": {}}'

  $ terrain query juno1..af00x '{"get_count": {}}' --network testnet --config-path ../config.terrain.json
```

_See code: [src/commands/query.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/query.ts)_

## `terrain sync-refs`

Sync configuration with frontend app.

```
USAGE
  $ terrain sync-refs [--refs-path <value>] [--dest <value>]

FLAGS
  --dest=<value>       [default: ./frontend/src/]
  --refs-path=<value>  [default: ./refs.terrain.json]

DESCRIPTION
  Sync configuration with frontend app.
```

_See code: [src/commands/sync-refs.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/sync-refs.ts)_

## `terrain task:new [TASK]`

Create a new task.

```
USAGE
  $ terrain task:new [TASK]

DESCRIPTION
  Create a new task.
```

_See code: [src/commands/task/new.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/task/new.ts)_

## `terrain task:run [TASK]`

run predefined task

```
USAGE
  $ terrain task:run [TASK] [--signer <value>] [--network mainnet|testnet|localterra] [--prefix <value>]
    [--refs-path <value>] [--keys-path <value>]

FLAGS
  --keys-path=<value>  [default: ./keys.terrain.js]
  --network=<option>   [default: localterra] network to deploy to from config.terrain.json
                       <options: mainnet|testnet|localterra>
  --prefix=<value>     [default: terra] address prefix of target chain, all chains supported by Station are supported by
                       terrain
  --refs-path=<value>  [default: ./refs.terrain.json]
  --signer=<value>     [default: test1]

DESCRIPTION
  run predefined task
```

_See code: [src/commands/task/run.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/task/run.ts)_

## `terrain test CONTRACT-NAME`

Runs unit tests for a contract directory.

```
USAGE
  $ terrain test [CONTRACT-NAME] [--no-fail-fast]

FLAGS
  --no-fail-fast  Run all tests regardless of failure.

DESCRIPTION
  Runs unit tests for a contract directory.

EXAMPLES
  $ terrain test counter

  $ terrain test counter --no-fail-fast
```

_See code: [src/commands/test.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/test.ts)_

## `terrain test:coverage [CONTRACT-NAME]`

Runs unit tests for a contract directory.

```
USAGE
  $ terrain test:coverage [CONTRACT-NAME]

DESCRIPTION
  Runs unit tests for a contract directory.

EXAMPLES
  $ terrain test:coverage

  $ terrain test:coverage counter
```

_See code: [src/commands/test/coverage.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/test/coverage.ts)_

## `terrain tx CONTRACT MSG`

Execute tx on the interchain

```
USAGE
  $ terrain tx [CONTRACT] [MSG] [--signer <value>] [--network mainnet|testnet|localterra] [--prefix
    <value>] [--refs-path <value>] [--keys-path <value>]

ARGUMENTS
  CONTRACT  Contract address
  MSG       Tx msg to be executed in JSON format

FLAGS
  --keys-path=<value>  [default: ./keys.terrain.js]
  --network=<option>   [default: localterra] network to deploy to from config.terrain.json
                       <options: mainnet|testnet|localterra>
  --prefix=<value>     [default: terra] address prefix of target chain, all chains supported by Station are supported by
                       terrain
  --refs-path=<value>  [default: ./refs.terrain.json]
  --signer=<value>     [default: test1]

DESCRIPTION
  Execute tx on the interchain

EXAMPLES
  $ terrain tx terra1..fx9fs '{"increment": {}}'

  $ terrain tx juno1..af00x '{"reset": {"count": 0}}' --network testnet
```

_See code: [src/commands/tx.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/tx.ts)_

## `terrain wallet:new`

Generate a new wallet to use for signing contracts.

```
USAGE
  $ terrain wallet:new [--outfile <value>] [--index <value>]

FLAGS
  --index=<value>    key index to use, default value is 0
  --outfile=<value>  absolute path to store the mnemonic key to. If omitted, output to stdout

DESCRIPTION
  Generate a new wallet to use for signing contracts.
```

_See code: [src/commands/wallet/new.ts](https://github.com/terra-money/terrain/blob/v1.0.1/src/commands/wallet/new.ts)_
<!-- commandsstop -->
