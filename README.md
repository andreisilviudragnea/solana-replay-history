# How to replay Solana mainnet txs from Google Cloud Storage snapshots and ledger archives?

As an example, let's use this Solana tx with truncated
logs: [4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj](https://solscan.io/tx/4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj).
It contains a log line "Log truncated".

## 1. Find in Google Cloud Storage the highest slot less than the tx slot

The Google Cloud Storage endpoints are:

- https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-us-ny5
- https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2 (used in this tutorial)
- https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-asia-sg1

These endpoints are taken
from the [README](https://github.com/solana-labs/solana-bigtable/blob/master/README.md?plain=1#L124)
of [solana-bigtable](https://github.com/solana-labs/solana-bigtable) repository.

For our example, the slot
of
tx [4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj](https://solscan.io/tx/4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj)
is [257207162](https://solscan.io/block/257207162).

In Google Cloud Storage, the highest slot less than [257207162](https://solscan.io/block/257207162)
is [257034560](https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2/257034560).

Check the [bounds.txt](https://storage.googleapis.com/mainnet-beta-ledger-europe-fr2/257034560/bounds.txt) file inside
the
[257034560](https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2/257034560) bucket. It should
contain the tx slot [257207162](https://solscan.io/block/257207162) in the range of ledger data:

```
Ledger has data for 427359 slots 257034560 to 257472032
  with 414285 rooted slots from 257034560 to 257471967
  and 45 slots past the last root
```

This is ok in our case, because the tx slot [257207162](https://solscan.io/block/257207162) is in the range of ledger
data
(`257034560 < 257207162 < 257472032`).

The bounds in this file refer to ledger data inside
the [rocksdb.tar.zst](https://console.cloud.google.com/storage/browser/_details/mainnet-beta-ledger-europe-fr2/257034560/rocksdb.tar.zst)
archive.

## 2. Download the [genesis.tar.bz2](https://api.mainnet-beta.solana.com/genesis.tar.bz2) archive for Solana Mainnet cluster

An important part of the ledger replay process is the genesis archive. This archive contains the genesis configuration
for Solana Mainnet cluster. It needs to be downloaded into the `/mnt/ledger` directory:

```bash
wget "https://api.mainnet-beta.solana.com/genesis.tar.bz2"
```

## 2. Download the snapshot from Google Cloud Storage for the highest slot less than the tx slot

The snapshot archive contains the state of all Solana accounts at a specific slot, but also the bank state.

I recommend downloading from the endpoint closest to your machine. In my case, I am downloading from Europe endpoint, so
my download speed is the best.

If you check the [257034560](https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2/257034560)
bucket, you
will see
both a snapshot for
slot [257034560](https://console.cloud.google.com/storage/browser/_details/mainnet-beta-ledger-europe-fr2/257034560/snapshot-257034560-3BEhaqKsp7r3cTwM8HH2wQZDGGRhiwvkQcGMGpsrTJFj.tar.zst)
and a bucket
called [hourly](https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2/257034560/hourly)
containing other snapshots.

If you go into
the [hourly](https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2/257034560/hourly)
directory, you will find the snapshot with highest slot less than [257207162](https://solscan.io/block/257207162), which
is
[257197855](https://console.cloud.google.com/storage/browser/_details/mainnet-beta-ledger-europe-fr2/257034560/hourly/snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst).

It is important to use the snapshot of the highest slot less than the tx slot, because ledger replay starts from the
snapshot slot and it takes a long time, so the closer you are to the expected tx slot, the less replay work is needed.

Let's download this snapshot in the `/mnt/ledger` directory (the download link is the public URL
for [257197855](https://storage.googleapis.com/mainnet-beta-ledger-europe-fr2/257034560/hourly/snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst)
from the UI):

```bash
root@solana-test-01:/mnt/ledger# wget "https://storage.googleapis.com/mainnet-beta-ledger-europe-fr2/257034560/hourly/snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst"
--2024-04-17 09:52:14--  https://storage.googleapis.com/mainnet-beta-ledger-europe-fr2/257034560/hourly/snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst
Resolving storage.googleapis.com (storage.googleapis.com)... 216.58.214.27, 142.250.179.187, 142.251.39.123, ...
Connecting to storage.googleapis.com (storage.googleapis.com)|216.58.214.27|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 66997446556 (62G) [application/octet-stream]
Saving to: ‘snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst’

snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz 100%[================================================================================================================>]  62.40G   157MB/s    in 6m 46s

2024-04-17 09:59:00 (157 MB/s) - ‘snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst’ saved [66997446556/66997446556]
```

## 3. Download the ledger archive from Google Cloud Storage for the highest slot less than the tx slot

The ledger archive contains the ledger data (transactions, blocks, slots, forks) for a specific range of slots.

From the same [257034560](https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2/257034560)
bucket, download
the [rocksdb.tar.zst](https://console.cloud.google.com/storage/browser/_details/mainnet-beta-ledger-europe-fr2/257034560/rocksdb.tar.zst)
archive (the download link is the public URL
for [rocksdb.tar.zst](https://storage.googleapis.com/mainnet-beta-ledger-europe-fr2/257034560/rocksdb.tar.zst) from UI).

This can take a while (94m 10s), because
the [rocksdb.tar.zst](https://console.cloud.google.com/storage/browser/_details/mainnet-beta-ledger-europe-fr2/257034560/rocksdb.tar.zst)
archive is huge (838.6 GB), so use a screen session if your connection is unstable:

```bash
root@solana-test-01:/mnt/ledger# wget "https://storage.googleapis.com/mainnet-beta-ledger-europe-fr2/257034560/rocksdb.tar.zst"
--2024-04-17 09:54:38--  https://storage.googleapis.com/mainnet-beta-ledger-europe-fr2/257034560/rocksdb.tar.zst
Resolving storage.googleapis.com (storage.googleapis.com)... 142.251.39.123, 142.250.179.155, 142.251.36.59, ...
Connecting to storage.googleapis.com (storage.googleapis.com)|142.251.39.123|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 900407911369 (839G) [application/octet-stream]
Saving to: ‘rocksdb.tar.zst’

rocksdb.tar.zst                                    100%[================================================================================================================>] 838.57G   154MB/s    in 94m 10s

2024-04-17 11:28:48 (152 MB/s) - ‘rocksdb.tar.zst’ saved [900407911369/900407911369]
```

## 4. Extract the [rocksdb.tar.zst](https://console.cloud.google.com/storage/browser/_details/mainnet-beta-ledger-europe-fr2/257034560/rocksdb.tar.zst) archive

This can also take a while, so use a screen session for this command too:

```bash
tar --use-compress-program=unzstd -xvf rocksdb.tar.zst
```

In my case, it took 37 minutes:

```bash
real    37m4.438s
user    15m5.566s
sys     37m2.644s
```

## 6. Compile `agave-ledger-tool` with `--log-messages-bytes-limit` support

`agave-ledger-tool` does not support the `--log-messages-bytes-limit` parameter. I created a
branch [ledger-tool-log-messages-bytes-limit-v1.17](https://github.com/andreisilviudragnea/solana/tree/ledger-tool-log-messages-bytes-limit-v1.17)
from [v1.17](https://github.com/anza-xyz/agave/tree/v1.17) with support for this parameter.

```bash
git clone https://github.com/andreisilviudragnea/solana.git
cd solana/ledger-tool
git checkout ledger-tool-log-messages-bytes-limit-v1.17
cargo build --release
cd ../target/release
./agave-ledger-tool --version
```

Running the commands above should give an output something similar to:

```bash
agave-ledger-tool 1.17.32 (src:00000000; feat:3746964731, client:Agave)
```

## 7. Check the downloaded ledger data by running `agave-ledger-tool bounds`

```bash
cd /mnt
~/solana/target/release/agave-ledger-tool bounds
```

The command can fail with:

```
[2024-04-17T13:59:44.784150124Z INFO  agave_ledger_tool] agave-ledger-tool 1.17.32 (src:00000000; feat:3746964731, client:Agave)
[2024-04-17T13:59:44.784257455Z ERROR solana_ledger::blockstore] Unable to increase the maximum open file descriptor limit to 1000000 from 1024
Failed to open blockstore at "/mnt/ledger": UnableToSetOpenFileDescriptorLimit
```

The above error can be fixed by running:

```bash
ulimit -n 1000000
```

Running `./agave-ledger-tool bounds` again should give an output similar to:

```bash
[2024-04-17T14:06:09.964041082Z INFO  agave_ledger_tool] agave-ledger-tool 1.17.32 (src:00000000; feat:3746964731, client:Agave)
[2024-04-17T14:06:09.964137182Z INFO  solana_ledger::blockstore] Maximum open file descriptors: 1000000
[2024-04-17T14:06:09.964141385Z INFO  solana_ledger::blockstore] Opening database at "/mnt/ledger/rocksdb"
[2024-04-17T14:06:09.972063122Z INFO  solana_ledger::blockstore_db] Opening Rocks with secondary (read only) access at: "/mnt/ledger/rocksdb/solana-secondary"
[2024-04-17T14:06:09.972078799Z INFO  solana_ledger::blockstore_db] This secondary access could temporarily degrade other accesses, such as by agave-validator
[2024-04-17T14:06:22.805794104Z INFO  solana_ledger::blockstore_db] Rocks's automatic compactions are disabled due to Secondary access
[2024-04-17T14:06:22.805911401Z INFO  solana_ledger::blockstore] "/mnt/ledger/rocksdb" open took 12.8s
Ledger has data for 427359 slots 257034560 to 257472032
  with 414285 rooted slots from 257034560 to 257471967
  and 45 slots past the last root

[2024-04-17T14:06:23.500451285Z INFO  agave_ledger_tool] ledger tool took 13.5s
```

The part

```
Ledger has data for 427359 slots 257034560 to 257472032
  with 414285 rooted slots from 257034560 to 257471967
  and 45 slots past the last root
```

should be identical to the content
of [bounds.txt](https://storage.googleapis.com/mainnet-beta-ledger-europe-fr2/257034560/bounds.txt) file.

## 7. Hook a simple Geyser plugin in the ledger replay process

The `agave-ledger-tool` has a `--geyser-plugin-config` parameter that can be used to hook a Geyser plugin in the ledger
replay process. For this example, we will use a very simple plugin that logs only the expected tx with signature
[4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj](https://solscan.io/tx/4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj).
The plugin can be found
at [imple-solana-geyser-plugin](https://github.com/andreisilviudragnea/simple-solana-geyser-plugin).

```bash
git clone https://github.com/andreisilviudragnea/simple-solana-geyser-plugin.git
cd simple-solana-geyser-plugin
cargo build --release
vim config.json # Edit this file to contain the path to the compiled plugin shared library
```

In my case, it looks something like this:

```json
{
  "libpath": "/root/simple-solana-geyser-plugin/target/release/libsimple_solana_geyser_plugin.so"
}
```

## 8. Replay the ledger between the snapshot slot and the tx slot

The ledger replay process will start from the snapshot slot [257197855](https://solscan.io/block/257197855), which
contains all the account states at the specific slot. The ledger will be replayed until the tx
slot [257207162](https://solscan.io/block/257207162).

```bash
cd /mnt
RUST_LOG=info,solana_metrics=off ~/solana/target/release/agave-ledger-tool verify \
  --skip-verification \
  --halt-at-slot 257207163 \
  --geyser-plugin-config /root/simple-solana-geyser-plugin/config.json \
  --log-messages-bytes-limit 1000000
```

The meaning of all the parameters:

- `--skip-verification` - Skip ledger PoH and transaction verification. This speeds up the ledger replay process.
- `--halt-at-slot 257207163` - Halt the ledger replay process at slot [257207162](https://solscan.io/block/257207162).
  The ledger replay process will start from the snapshot slot [257197855](https://solscan.io/block/257197855) until
  after the tx slot.
- `--geyser-plugin-config /root/simple-solana-geyser-plugin/config.json` - Specify the configuration file for the Geyser
  plugin.
- `--log-messages-bytes-limit 1000000` - Maximum number of bytes written to the program log before truncation. This
  needs to be a bigger value than the default
  of [10000](https://github.com/anza-xyz/agave/blob/master/program-runtime/src/log_collector.rs#L4).
