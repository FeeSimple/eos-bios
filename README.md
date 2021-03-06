EOS.IO Software-based blockchain boot orchestration tool
--------------------------------------------------------
[chinese version](./README-cn.md)

`eos-bios` is a command-line tool for people who want to kickstart a
blockchain using EOS.IO Software.

It implements the following:
* Booting a mainnet
* Booting testnets
* Booting local development environments
* Booting consortium or private networks
* Orchestrates launches with many participants through the Network Discovery Protocol.

This program relies on you publishing a `discovery` file.

NOTE: Jump directly to the [sample configurations](./sample_config) if
you know what you're doing.


Launch a local node with a single command
-----------------------------------------

[Download `eos-bios`](https://github.com/eoscanada/eos-bios/releases),
clone this repo, go to `sample_config/docker`:

    git clone https://github.com/eoscanada/eos-bios
    cd eos-bios/sample_config
    wget https://github.com/eoscanada/eos-bios/releases/download/......tar.gz  # Pick the right one for you
    tar -zxvf eos-bios*tar.gz
    mv eos-bios_*/eos-bios .

Then run:

    ./eos-bios boot

Enjoy.

This gives you a fully fledged development environment, a chain loaded
with all system contracts, very similar to what you will get on the
main network once launched.

The sample configuration sets up a single node, as it doesn't point to
other block producer candidates (through the `peers` property).


Boot a network
--------------

By tweaking your configuration, you can grow the network.  To force
the `boot` operation, run:

    ./eos-bios boot

Have your friends run:

    ./eos-bios join --verify

Once your `boot` call finishes properly, it will display the
_Kickstart Data_ that you can provide your friends with.  Once they
paste that in their terminal, they will join your network.

For this to work, you need to properly configure your `config.yaml`
and have properly disseminated your `discovery` files, and given the
URL to your friends.



Join an existing network
------------------------

To join a network, tweak your discovery file to point to the network you're trying to join and publish it. Make sure other participants in the network link to your discovery file as their `peers`.

* Read the [annotated sample configuration file](sample_config/config.yaml).
* Read [sample discovery file here](https://github.com/eoscanada/network-discovery)

Run:

    eos-bios join [--verify]

The `--verify` option toggles the boot sequence verification.


Orchestrate a community launch
------------------------------

When the time comes to orchestrate a launch, *everyone* will run:

    eos-bios orchestrate

According to an algorithm, and using the network discovery data, each
team will be assigned a role deterministically.

You then fall in one of these three categories:

1. The BIOS Boot node, which will, alone, execute the equivalent of `eos-bios boot`.
2. An Appointed Block Producer, which executes the equivalent of `eos-bios join --verify`
3. An other participant, which executes the equivalent of `eos-bios join`

The same hooks are used in `boot`, `join` and `orchestrate`, so get
them right and practice.



Install / Download
------------------

You can download the latest release here:
https://github.com/eoscanada/eos-bios/releases .. it is a single
binary that you can download on all major platforms. Simply make
executable and run. It has zero dependencies.

Alternatively, you can build from source with:

    go get -v github.com/eoscanada/eos-bios/eos-bios

This will install the binary in `~/go/bin` provided you have the Go
tool installed (quick install at https://golang.org/dl)


Join the discussion
-------------------

On Telegram through this invite link:
https://t.me/joinchat/GSUv1UaI5QIuifHZs8k_eA (EOSIO BIOS Boot channel)


Previous proposition
--------------------

See the previous proposition in this repo in README.v0.md



TODO
----

* Shuffling of the top 5 for Boot selection
* Wait on Bitcoin Block
  * Add bitcoin_block_height in LaunchData
* In Orchestrate, compute the LaunchData by the most votes, weighted by the highest Weight
* Do we auto-publish the `my_discovery_file.yaml` ? Make it a hook?
* canonical_url ?
* convention regarding URLs, which pieces we want to see in there (the name of the organization, `testnet-[network_name])

* Allow ABPs to publish their `secret-p2p-address` (called Meshing
  data ?) while the BIOS Boot node is doing its job.
  * The pasted data would simply be added to the list of addresses
    passed to the `join_network` hook when the BIOS Boot is ready
    and we receive the Kickstart data.
  * Both be a base64 encoded JSON:
    * `type=genesis` with the genesis JSON, which includes the public key, perhaps the pubkey on the side..
    * `type=meshing` with the p2p IP/host to connect to
    * `type=handoff` with the private key used to do everything.

* No publishing of `secret-p2p-address`, only a remote control of
  `/v1/net/connect` to some ABPs who have published and established
  the network.
  * hook_boot_network.sh
  * hook_boot_publish_genesis.sh
  * hook_boot_connect_mesh.sh
  * hook_boot_publish_privkey.sh

  * hook_join_network.sh  # add connect_count
