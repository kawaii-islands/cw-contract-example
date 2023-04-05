# CosmWasm Examples

[![CircleCI](https://circleci.com/gh/InterWasm/cw-contacts/tree/main.svg?style=shield)](https://app.circleci.com/pipelines/github/InterWasm/cw-contracts)

This repo is a collection of simple contracts built with the
[cosmwasm](https://github.com/CosmWasm/cosmwasm) framework.
Smart contracts here are for only demonstration purposes, **not production ready**.
Production grade smart contracts are collected under [cw-plus](https://github.com/CosmWasm/cw-plus).

This repo's organization is relatively simple. The top-level directory is just a placeholder
and has no real code. And we use workspaces to add multiple contracts below.
This allows us to compile all contracts with one command.

## Usage:

The following contracts are available for use. For each of the contracts in `contracts`, you can view the source code under `src`
and a precompiled wasm ready for deployment called `contract.wasm`.

Take a look here:

* [escrow](https://github.com/InterWasm/cw-contracts/tree/main/contracts/escrow) - A basic escrow with timeout and partial release
* [nameservice](https://github.com/InterWasm/cw-contracts/tree/main/contracts/nameservice) - Simple name service application to buy names and map values to those names
* [voting](https://github.com/InterWasm/cw-contracts/tree/main/contracts/voting) - An example voting contract to create, manage, vote and deposit on polls
* [simple-option](https://github.com/InterWasm/cw-contracts/tree/main/contracts/simple-option) - A contract that replicates options in finance
* [cw20-pot](https://github.com/InterWasm/cw-contracts/tree/main/contracts/cw20-pot) - Basic smart contract using cw20 contact

You can get more info from `README.md` file in each of the contacts.

## Development

### Starting a contract

If you want to add a contract, first fork this repo and create a branch for your PR.
I suggest setting it up via [cw-template](https://github.com/CosmWasm/cw-template):

`cargo generate --git https://github.com/CosmWasm/cw-template.git --name FOO`

Then update the `README.md` to reflect your actual contract (just read the `README.md` in the autogenerated
template - it explains a lot).

### Preparing for merge

Before you merge the code, make sure it builds and passes all tests:

```
./devtools/build_test_all.sh
```

Once you pass these checks, please open a [PR on this repo](https://github.com/InterWasm/cw-contracts/pulls).

### Release builds

On every tag release builds are automatically created and
[deployed to GitHub Releases](https://github.com/InterWasm/cw-contracts/releases).

You can build release artifacts manually like this, which creates a reproducible
optimized build for each contract and saves them to the `./artifacts` directory:

```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.3 ./contracts/*/
```


### Build with oraichain
- Doc gốc ```https://docs.cosmwasm.com/docs/getting-started/compile-contract/```
- B1: Cài đặt các thư viện theo thằng này https://docs.cosmwasm.com/docs/getting-started/installation nhưng thay cái repo
  git clone https://github.com/CosmWasm/wasmd.git =>  https://github.com/oraichain/orai để cài thay thế cho wasmd vì mình sẽ dùng oraid. Cách cài thư viện này giống bọn này https://docs.cosmwasm.com/docs/getting-started/installation
- B2: Compile https://docs.cosmwasm.com/docs/getting-started/compile-contract
```angular2html
1. cd contracts/nameservice
2. docker run --rm -v "$(pwd)":/code \
--mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
--mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
cosmwasm/rust-optimizer:0.12.11

- Sau khi chạy xong thấy có thư mục build artifacts
```
- B3: Add ví orai vào oraid cho việc tương tác nếu cần kí
```angular2html
- oraid keys add  name_wallet --recover
vd: oraid keys add  OraiWallet2023 --recover
- xong em nhập mnemonic vào và passphrase
- có thể xem với lệnh `orai keys show -a OraiWallet2023`
```

- B4: Deploy https://docs.cosmwasm.com/docs/getting-started/interact-with-contract
```angular2html
RES=$(/Users/admin/go/bin/oraid tx wasm store artifacts/cw_nameservice.wasm --from OraiWallet2023 --node https://testnet-rpc.orai.io:443 --chain-id Oraichain-testnet --gas-prices 0.25orai --gas auto --gas-adjustment 1.3 -y --output json -b block)
echo $RES
```
- echo  $RES ra sẽ thấy transaction mới tạo, vào scan để xem codeId và address smc cho nhanh
- vd đã tạo : https://scan.orai.io/txs/899BC1AB6CAC2F79986A974A0E6D01C887FDF6E43308649CB115627A8AEDBCC3
- Các bước sau tùy vào sử dụng vì khi có address rồi thì dùng nodejs call
