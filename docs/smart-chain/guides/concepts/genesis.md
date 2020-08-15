# Genesis File


This document explains how the genesis file of the Binance Smart Chain is structured.


## What is a Genesis File

A genesis file is a JSON file which defines the initial state of your blockchain. It can be seen as height `0` of your blockchain. The first block, at height `1`, will reference the genesis file as its parent.

The state defined in the genesis file contains all the necessary information, like initial token allocation, genesis time, default parameters, and more. Let us break down these information.

Genesis Link fot Chapel Testnet: <https://github.com/binance-chain/bsc/releases/download/v1.0.0-beta.1/genesis.json>

## Explaination

* **chainId**

**66** for main-net and **96** for test-net.  To compatible with third part service that already supports Ethereum, we’d better not use network id that Ethereum ecology that already used.  The network id of test-net should be distinct from main-net.


* **period**

Minimum difference between two consecutive block’s timestamps. Suggested 3s for the testnet .

* **epoch**

Number of blocks after which to checkpoint and reset the pending votes. Suggested 100 for testnet

* **nonce**

The nonce is the cryptographically secure mining proof-of-work that proves beyond reasonable doubt that a particular amount of computation has been expended in the determination of this token value.

In Binance Smart Chain, this value is always set to 0x0.


* **timestamp**

Must be at least the parent timestamp + BLOCK_PERIOD.

* **extraData**

	* EXTRA_VANITY: Fixed number of extra-data prefix bytes reserved for signer vanity. Suggested 32 bytes
	* Signer Info: validator address
	* EXTRA_SEAL bytes (fixed) is the signer’s signature sealing the header.

* **gasLimit**

A scalar value equal to the current chain-wide limit of Gas expenditure per block. High in our case to avoid being limited by this threshold during tests. Note: this does not indicate that we should not pay attention to the Gas consumption of our Contracts.

GasCeil is 40000000 for testnet

* **difficulty**

A scalar value corresponding to the difficulty level applied during the nonce discovering of this block.
Suggested 0x1 for testnet

* **mixHash**

Reserved for fork protection logic, similar to the extra-data during the DAO.
Must be filled with zeroes during normal operation.

* **coinbase**

System controled address for collecting block rewards

* **alloc**

Allows to define a list of pre-filled wallets.

| Contract Name         | Address                                   | ABI file                                      |
| ----------------------|-------------------------------------------|--------------------------------------------------- |
| BSCValidatorSet       |[0x0000000000000000000000000000000000001000](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001000/contracts) |[bscvalidatorset](../../system-smart-contract/bscvalidatorset.abi)|
| SlashIndicator        |[0x0000000000000000000000000000000000001001](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001001/contracts) |[slashindicator](../../system-smart-contract/slashindicator.abi)|
| SystemReward          |[0x0000000000000000000000000000000000001002](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001002/contracts) |[systemreward](../../system-smart-contract/systemreward.abi)|
| TendermintLightClient |[0x0000000000000000000000000000000000001003](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001003/contracts) |[tendermintlightclient](../../system-smart-contract/tendermintlightclient.abi) |
| TokenHub              |[0x0000000000000000000000000000000000001004](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001004/contracts) |[tokenhub](../../system-smart-contract/tokenhub.abi)|
| RelayerIncentivize    |[0x0000000000000000000000000000000000001005](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001005/contracts) |[relayerincentivize](../../system-smart-contract/relayerincentivize.abi)|
| RelayerHub            |[0x0000000000000000000000000000000000001006](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001006/contracts) |[relayerhub](../../system-smart-contract/relayerhub.abi) |
| GovHub                |[0x0000000000000000000000000000000000001007](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001007/contracts) |[govhub](../../system-smart-contract/govhub.abi) |
| TokenManager          |[0x0000000000000000000000000000000000001008](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000001008/contracts) |[tokenmanager](../../system-smart-contract/tokenmanager.abi) |
| CrossChain            |[0x0000000000000000000000000000000000002000](https://explorer.binance.org/smart-testnet/address/0x0000000000000000000000000000000000002000/contracts) |[crosschain](../../system-smart-contract/crosschain.abi) |

* **number**

Block height in the chain, where the height of the genesis is block 0.

* **parentHash**

The Keccak 256-bit hash of the entire parent block’s header (including its nonce and mixhash). Pointer to the parent block, thus effectively building the chain of blocks. In the case of the Genesis block, and only in this case, it's 0.


## Binance Smart Chain Initialization

There are two requirements we need to meet:
1. There are already some BNBs in the BC network when it starts up.
2. All the initial validators of Binance Smart Chain should be recorded in the BC.

The first one is a must, because if we want to transfer some BNB to BSC, we will consume some gas in BSC. So we must ensure there are already some BNBs in BSC. That means the first interchain transfer should be done in the genesis block of side chain.

For the second one, we should have that to ensure all the validators info and changes are tracked in the main chain.

So the solution is we enable the staking functionality of BSC first on BC. So people can apply to be a validator or delegate to these candidates. We can have a time limit. After that, we collect all the elected validators and write them to the genesis of BSC.