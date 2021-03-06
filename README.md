# OWL Token
The OWL token and related smart contracts.

The token and contract can be in **Etherscan**:

* **Mainnet**:   
  * TokenOWLProxy (address): [https://etherscan.io/token/0x1a5f9352af8af974bfc03399e3767df6370d82e4](https://etherscan.io/token/0x1a5f9352af8af974bfc03399e3767df6370d82e4)
  * TokenOWL (master contract): [https://etherscan.io/token/0x60fbbd1fb0076971e8060631b5dd895f55ad5ab7](https://etherscan.io/address/0x60fbbd1fb0076971e8060631b5dd895f55ad5ab7)
* **Rinkeby**:
  * TokenOWLProxy (address): [https://rinkeby.etherscan.io/token/0xa7d1c04faf998f9161fc9f800a99a809b84cfc9d](https://rinkeby.etherscan.io/token/0xa7d1c04faf998f9161fc9f800a99a809b84cfc9d)
  * TokenOWL (master contract): [https://rinkeby.etherscan.io/token/0xff9874588b2358704832a380ca9892f0a1c57b40](https://rinkeby.etherscan.io/token/0xff9874588b2358704832a380ca9892f0a1c57b40)
* **Kovan**:
  * TokenOWLProxy (address): [https://kovan.etherscan.io/token/0xb6f77a34ff81dd13fa68b5774d74541a61047fe8](https://kovan.etherscan.io/token/0xb6f77a34ff81dd13fa68b5774d74541a61047fe8)
  * TokenOWL: [https://kovan.etherscan.io/token/0xa98fdb77b578f30d5d88b3ebc2600bfe67fa09d1](https://kovan.etherscan.io/token/0xa98fdb77b578f30d5d88b3ebc2600bfe67fa09d1)
  * OWLAirdrop: [https://kovan.etherscan.io/token/0x45ce24c27f2442feb62f62bfb26ab161f07adc71](https://kovan.etherscan.io/token/0x45ce24c27f2442feb62f62bfb26ab161f07adc71)
* **xDAI**:
  * BridgedTokenOWL (address): [https://blockscout.com/poa/xdai/address/0x0905Ab807F8FD040255F0cF8fa14756c1D824931](https://blockscout.com/poa/xdai/address/0x0905Ab807F8FD040255F0cF8fa14756c1D824931)


## Setup and show the networks
```bash
# Install dependencies
yarn install

# Compile and restore the network addresses
yarn restore

# Show current network addresses
yarn networks
```

## Execute migrations into a local ganache-cli
```bash
# Run ganache CLU
yarn rpc

# Execute the migrations
yarn migrate

# Or you can do all at once using
yarn migrate-all
```

## Change the lock period time - re-deployment
The deployment script has an environemt variable `LOCK_END_TIME` that 
allows you to set a different lock period (`30 days` by default).

```bash
# Deploy for develop with a given end time for lock period
# flag --reset is NECESSARY as you are re-deploying
# 24H format, please be careful!
LOCK_END_TIME='2018-06-12T16:00:00+02:00' yarn migrate --reset
(npm) > LOCK_END_TIME='2018-06-12T16:00:00+02:00' npm run migrate -- --reset
```

## Generate a new version
```bash
# In a release branch (i.e. release/vX.Y.X)
# Migrate the version to the testnets, at least rinkeby, and posibly mainnet
# You can optionally change the gas price using the GAS_PRICE_GWEI env variable
# if you are changing LOCK_END_TIME here, flag --reset is NECESSARY as you are re-deploying
yarn restore
MNEMONIC=$MNEMONIC_OWL yarn migrate --network rinkeby
(npm) > MNEMONIC=$MNEMONIC_OWL npm run migrate -- --network rinkeby

# Extract the network file
yarn networks-extract

# Verify the contract in Etherscan
# Folow the steps in "Verify contract"

# Commit the network file
git add networks.json
git commit -m 'Updated the networks file'
> OR simply git commit -am 'Updated networks file'

# Generate version using Semantic Version: https://semver.org/
# For example, for a minor version
npm version minor
git push && git push --tags

# Deploy npm package
npm publish --access=public

# Merge tag into develop, to deploy it to production, also merge it into master
git checkout develop
git merge vX.Y.X
```

## Verify contract manually
> NOTE: For mainnet is better to use the automatic verification script (see next section)

Flatten the smart contract:
```bash
npx truffle-flattener contracts/5/TokenOWL.sol > build/TokenOWL-EtherScan.sol
npx truffle-flattener contracts/5/TokenOWLProxy.sol > build/TokenOWLProxy-EtherScan.sol
npx truffle-flattener contracts/5/OWLAirdrop.sol > build/OWLAirdrop-EtherScan.sol
```

Go to Etherscan validation page:
* Go to[https://rinkeby.etherscan.io/verifyContract?a=]()
* Fill the information:
  * Use `build/TokenOWL-EtherScan.sol`
  * Set the exact compiler version used for the compilation i.e. `v0.4.24+commit.e67f0147`
  * Optimization: `No`
* Press validate

## Verify contract automatically (mainnet only)
You can use [verify-on-etherscan](https://www.npmjs.com/package/verify-on-etherscan)

> NOTE: To use the verify You'll need to create an API Key in Etherscan for this step.

```bash
API_KEY=your-etherscan-api-key yarn verify
```

## Verify contract on xDAI

Flatten the smart contract:
```bash
npx truffle-flattener contracts/4/BridgedTokenOWL.sol > build/BridgedTokenOWL-BlockScout.sol
```

Go to Blockscout validation page:
* Go to `https://blockscout.com/poa/xdai/address/<address>/contract_verifications/new`
* Fill the information:
  * Use `BridgedTokenOWL-BlockScout.sol`
  * Compiler version: `v0.4.24+commit.e67f0147`
  * EVM Version: `default`
  * Optimization: `Yes`, Runs `200`
  * Try to fetch contructor arguments automatically: `Yes`
* Press validate


# Change the owner
There's a script to change the OWL owner:

For example, to change the owner in `rinkeby` to `0xb65d2c1a4756ee857fca057ef561758b42277f5e`:
```bash
# Dry run 
MNEMONIC=$MNEMONIC_OWL yarn change-owner --owner 0xb65d2c1a4756ee857fca057ef561758b42277f5e --network rinkeby --dry-run

# Run the transaction
MNEMONIC=$MNEMONIC_OWL yarn change-owner --owner 0xb65d2c1a4756ee857fca057ef561758b42277f5e --network rinkeby
```

## License
[LGPL v3](./LICENSE)
