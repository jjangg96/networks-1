# Setting up cyber Genesis Validator

This guide will provide detailed instructions on how to submit gentx for bostrom chain!

The primary point of communication for the whole gentx and lauch ceremonies will be our [Cyber Hall of Fame](https://t.me/fameofcyber) telegram chat.

Some important notices about joining the genesis validator set: 

1. **GenTx must be submited by 0:00 UTC 1st November**
2. We recommend only highly experienced validators who have run on past Cosmos SDK chains and have participated in a genesis ceremony before become genesis validators on Cyber.
3. Being a Genesis validator for a Supercomputer is an honor, so we expect all participants to treat this opportuninty accordingly. We want our validators to be pro-active, maintain good uptime, communicate and act professionally during chain upgrades, and respectfully participate in governance.
4. To become a Genesis validator, you must have boot tokens on your account in Genesis. That might've happened if you participated in one of our previous testnets or Game of Links, or some other kind of incentivized activity. 


## Hardware setup

Recommended hardware setup stays the same as it was for the last Bostrom-testnets:

```js
CPU: 6 cores
RAM: 32 GB
SSD: 1 TB
Connection: 50+Mbps, Stable and low-latency connection
GPU: Nvidia GeForce (or Tesla/Titan/Quadro) with CUDA-cores; 4+ Gb of video memory*
Software: Ubuntu 18.04 LTS / 20.04 LTS
```

The blockchain is always growing. Therefore, in the future, hardware requirements might increase.
Also, the specs above are not mandatory and may differ, but it always implies that the node might be powerful and reliable.

## Instructions

All instructions provided are for the Ubuntu 20.04 system and may vary for other versions\OS.

### Install GO 1.17+

Cyber is written in `go`, and we'll need the version 1.17+ to compile it. 

Check which version you currently have with 

```bash
go version
``` 

If you need to update or install 'go' we'd recommend to use the handy go version manager called [g](https://github.com/stefanmaric/g). To install 'go' with it use: 

```bash
curl -sSL https://git.io/g-install | sh -s
source ~/.bashrc/
```

Agree to install latest version, or manually install specific version: 

```bash
g install 1.17.2
g set 1.17.2
go vesion
```
You might see something like that: 

```bash
go version go1.17.2 linux/amd64
```

### Install cyber 

Download source files from github: 

```bash
git clone https://github.com/cybercongress/go-cyber.git
cd go-cyber
git checkout bostrom-dev
```

Then install `cyber` daemon.  Running the following command will install the executable cyber to your GOPATH: 

```bash
make install
```

Verify your installation by running:

```bash
cyber version --long
```

Result must look like that: 

```bash
name: cyber
server_name: cyber
version: 0.2.0-rc2
commit: .......
build_tags: netgo ledger,
go: go version go1.17.2 linux/amd64
build_deps:
- filippo.io/edwards25519@v1.0.0-beta.2
- github.com/99designs/keyring@v1.1.6
...
- gopkg.in/yaml.v3@v3.0.0-20210107192922-496545a6307b
- nhooyr.io/websocket@v1.8.6
cosmos_sdk_version: v0.44.2
```

If your `cyber` version does not match - please verify that you dont have any other daemons laying somewhere else in `/usr/local/bin`.

### Initialize your node

Before you proceed verify that you dont have any old\test nodes initialized in that machine, check and remove folders inside `~/.cyber/`. If you have the testnet node running, stop it and remove `~/.cyber/` folder (do not forget to backup your seeds before you remove anything):

```bash
ls -la ~/
rm -rf .cyber/
```

Then run the following to init fresh one(replce <your_node_moniker> with something, for example `web2_destroyer`): 

```bash
cyber init <your_node_moniker>
```

This will create a new `.cyber` folder in your HOME directory.

### Download Pregenesis File

You can now download the "pregenesis" file for the chain.  This is a genesis file with the chain-id and all balances.

```sh
cd $HOME/.cyber/config/
wget -O $HOME/.cyber/config/genesis.json  https://gateway.ipfs.cybernode.ai/ipfs/QmQAd2SBwypsz5ZqkYgeSTFLq3peGV9xGE3i1gW5oPjcYT
```

### Import Validator Key

To create a gentx, you will need the private key to an address that has some tokens in Genesis.

There are a couple options for how to import a key into `cyber`.

You can import such a key into via a mnemonic or add one from the Ledger.

```bash
cyber keys add <your_key_name> --recover
```

You could use your **ledger** device with the Cosmos app installed on it to sign transactions. Add address from Ledger:

```bash
cyber keys add <your_key_name> --ledger
```

**<your_key_name>** is any name you pick to represent this key pair.
You have to refer to that name later when you use cli to sign transactions.

### Create GenTx

Now that you have your key imported you are able to use it to create your gentx.

To create the Genesis transaction, you will have to choose the following parameters for your validator:

- moniker
- commission-rate
- commission-max-rate
- commission-max-change-rate
- min-self-delegation (must be >10000boot)
- website (optional)
- details (optional)
- identity (keybase key hash, this is used to get validator logos in block explorers. optional)
- pubkey (will be pulled automatically)

Note that your gentx will be rejected if you use an amount greater than what you have on you balance in Genesis.

An example Genesis command would look like this:

```bash
cyber gentx <your_key_name> \
  --amount=10000000000boot \
  --min-self-delegation "1000000000" \
  --pubkey=$(cyber tendermint show-validator) \
  --moniker=<your_validator_nickname> \
  --commission-rate="0.05" \
  --commission-max-rate="0.10" \
  --commission-max-change-rate="0.01" \
  --chain-id=bostrom 
```

Output will look similar to this:

```sh
Genesis transaction written to "/root/.cyber/config/gentx/gentx-445e04520cef1116faab9900e2edadcb8164477c.json"
```
File content must look similar to this [sample-gentx](https://github.com/cybercongress/networks/blob/main/bostrom/gentxs/gentx-SAMPLE.json)

### Submit Your GenTx

To submit your GenTx for inclusion in the chain, please upload it to the [github.com/cybercongress/networks](https://github.com/cybercongress/networks/tree/main/bostrom) repo by `0:00 UTC 1st November`.

To upload the your Genesis file, please follow these steps:

1. Rename the gentx file located in your `$HOME/.cyber/config/gentx/` to gentx-{your-moniker}.json (please do not have any spaces or special characters in the file name)
2. Fork this repo by going to https://github.com/cybercongress/networks, clicking on fork, and choose your account (if multiple).
3. Clone your copy of the fork to your local machine
```sh
git clone https://github.com/<your_github_username>/networks
```
4. Copy the gentx to the networks repo (ensure that it is in the correct folder)

```sh
cp ~/.cyber/config/gentx/gentx-<your-moniker>.json networks/bostrom/gentxs/
```

5. Commit and push to your repo.
 
```sh
cd networks
git add bostrom/gentxs/*
git commit -m "<your validator moniker> gentx"
git push origin master
```

6. Create a pull request from your fork to master on this repo.
7. Let us know in [Cyber Hall of Fame](https://t.me/fameofcyber) when you've completed this process!
8. Stay tuned, further steps will be provided soon! 

### Back-up validator keys (!)

Your identity as validator consists of two things: 

- your account (to sign transactions)
- your validator private key (to sign stuff on the chain consensus layer)

Please back up `$HOME/.cyber/config/priv_validator_key.json` along with your seed phrase. In case of occasional folder loss you would be able to restore you validator.

Those things should not be changed, otherwise, your validator won't start on Genesis. 
