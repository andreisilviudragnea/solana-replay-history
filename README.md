# How to replay Solana mainnet txs from Google Cloud Storage snapshots and ledger archives?

As an example, let's use this Solana tx with truncated
logs: [4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj](https://solscan.io/tx/4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj).
It contains a log line "Log truncated".

For this tutorial, a powerful Ubuntu machine is needed. I used a machine with the following specs:

<details>
<summary>Ubuntu machine specs</summary>

```bash
lscpu
Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         46 bits physical, 57 bits virtual
  Byte Order:            Little Endian
CPU(s):                  64
  On-line CPU(s) list:   0-63
Vendor ID:               GenuineIntel
  Model name:            Intel(R) Xeon(R) Gold 6314U CPU @ 2.30GHz
    CPU family:          6
    Model:               106
    Thread(s) per core:  2
    Core(s) per socket:  32
    Socket(s):           1
    Stepping:            6
    CPU max MHz:         3400.0000
    CPU min MHz:         800.0000
    BogoMIPS:            4600.00
    Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs
                         bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt ts
                         c_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb cat_l3 invpcid_single ssbd mba ibrs ibpb stibp ibrs_enhanced tpr_shadow vnmi flexpriority ept vpid
                         ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid cqm rdt_a avx512f avx512dq rdseed adx smap avx512ifma clflushopt clwb intel_pt avx512cd sha_ni avx512bw avx512vl xsaveopt xs
                         avec xgetbv1 xsaves cqm_llc cqm_occup_llc cqm_mbm_total cqm_mbm_local split_lock_detect wbnoinvd dtherm ida arat pln pts avx512vbmi umip pku ospke avx512_vbmi2 gfni vaes vpclmulqdq avx
                         512_vnni avx512_bitalg tme avx512_vpopcntdq la57 rdpid fsrm md_clear pconfig flush_l1d arch_capabilities
Virtualization features:
  Virtualization:        VT-x
Caches (sum of all):
  L1d:                   1.5 MiB (32 instances)
  L1i:                   1 MiB (32 instances)
  L2:                    40 MiB (32 instances)
  L3:                    48 MiB (1 instance)
NUMA:
  NUMA node(s):          1
  NUMA node0 CPU(s):     0-63
Vulnerabilities:
  Gather data sampling:  Mitigation; Microcode
  Itlb multihit:         Not affected
  L1tf:                  Not affected
  Mds:                   Not affected
  Meltdown:              Not affected
  Mmio stale data:       Mitigation; Clear CPU buffers; SMT vulnerable
  Retbleed:              Not affected
  Spec rstack overflow:  Not affected
  Spec store bypass:     Mitigation; Speculative Store Bypass disabled via prctl and seccomp
  Spectre v1:            Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:            Mitigation; Enhanced IBRS, IBPB conditional, RSB filling, PBRSB-eIBRS SW sequence
  Srbds:                 Not affected
  Tsx async abort:       Not affected

lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0         7:0    0  63.9M  1 loop /snap/core20/2182
loop1         7:1    0    87M  1 loop /snap/lxd/27948
loop2         7:2    0  39.1M  1 loop /snap/snapd/21184
loop3         7:3    0  63.9M  1 loop /snap/core20/2264
loop4         7:4    0  38.7M  1 loop /snap/snapd/21465
nvme0n1     259:0    0 238.5G  0 disk
├─nvme0n1p1 259:1    0   512M  0 part /boot/efi
├─nvme0n1p2 259:2    0   1.9G  0 part [SWAP]
└─nvme0n1p3 259:3    0 236.1G  0 part /
nvme1n1     259:4    0 238.5G  0 disk
nvme2n1     259:5    0   3.5T  0 disk /mnt/ledger
nvme3n1     259:6    0   3.5T  0 disk /mnt/accounts

grep MemTotal /proc/meminfo
MemTotal:       527754348 kB # 527.754348 GB

lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.4 LTS
Release:	22.04
Codename:	jammy
```

</details>

## 1. Find in Google Cloud Storage the highest slot less than the tx slot

The Google Cloud Storage endpoints are:

- https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-us-ny5
- https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2 (used in this tutorial)
- https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-asia-sg1

These endpoints are taken
from the [README](https://github.com/solana-labs/solana-bigtable/blob/master/README.md?plain=1#L124)
of [solana-bigtable](https://github.com/solana-labs/solana-bigtable) repository.

For our example, the slot of
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
data (`257034560 < 257207162 < 257472032`).

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

The snapshot archive contains the state of all Solana accounts at a specific slot, but also
the [bank state](https://solana.com/docs/terminology#bank-state).

I recommend downloading from the endpoint closest to your machine. In my case, I am downloading from Europe endpoint, so
my download speed is the best.

If you check the [257034560](https://console.cloud.google.com/storage/browser/mainnet-beta-ledger-europe-fr2/257034560)
bucket, you will see both a snapshot for
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

The ledger archive contains the ledger data (transactions, blocks, slots, forks) for a specific range of slots. In
Solana,
the accounts state (stored in snapshot archive) is decoupled from the ledger (stored in ledger archive). A running
validator needs all account states at a specific snapshot slot, but only a part of the ledger, for some recent slots.

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

The
downloaded [rocksdb.tar.zst](https://console.cloud.google.com/storage/browser/_details/mainnet-beta-ledger-europe-fr2/257034560/rocksdb.tar.zst)
archive contains slots from roughly
[Mar 28, 2024 at 21:39:50 UTC](https://explorer.solana.com/block/257034560)
until [Mar 31, 2024 at 03:59:19 UTC](https://explorer.solana.com/block/257472031), so for about 2 days, 6 hours, 19
minutes, and 29 seconds.

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

And the size of the extracted directory is 1.8 TB:

```bash
du -sh /mnt/ledger/rocksdb
1.8T	/mnt/ledger/rocksdb
```

So the `/mnt/ledger` drive should have at least 3 TB capacity.

## 6. Compile `agave-ledger-tool` with `--log-messages-bytes-limit` support

`agave-ledger-tool` does not support the `--log-messages-bytes-limit` parameter. I created a
branch [ledger-tool-log-messages-bytes-limit-v1.17](https://github.com/andreisilviudragnea/solana/tree/ledger-tool-log-messages-bytes-limit-v1.17)
from [v1.17](https://github.com/anza-xyz/agave/tree/v1.17) with support for this parameter. Future Agave client versions
will support this parameter, as https://github.com/anza-xyz/agave/pull/854 has been merged.

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

The command may fail with:

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
at [simple-solana-geyser-plugin](https://github.com/andreisilviudragnea/simple-solana-geyser-plugin).

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
contains all the account states at the specific slot. The ledger will be replayed until after the tx
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
- `--halt-at-slot 257207163` - Halt the ledger replay process at the specified slot.
  The ledger replay process will start from the snapshot slot [257197855](https://solscan.io/block/257197855) until
  after the tx slot.
- `--geyser-plugin-config /root/simple-solana-geyser-plugin/config.json` - Specify the configuration file for the Geyser
  plugin.
- `--log-messages-bytes-limit 1000000` - Maximum number of bytes written to the program log before truncation. This
  needs to be a bigger value than the default
  of [10000](https://github.com/anza-xyz/agave/blob/master/program-runtime/src/log_collector.rs#L4).

The `notify_transaction` log statement contains the expected tx
signature [4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj](https://solscan.io/tx/4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj).
Also, the logs are not truncated anymore:

```bash
[2024-04-17T23:49:27.161026560Z INFO  simple_solana_geyser_plugin] notify_transaction(slot=257207162, transaction=ReplicaTransactionInfoV2 {
        signature: 4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj,
        is_vote: false,
        transaction: SanitizedTransaction {
            message: V0(
                LoadedMessage {
                    message: Message {
                        header: MessageHeader {
                            num_required_signatures: 1,
                            num_readonly_signed_accounts: 0,
                            num_readonly_unsigned_accounts: 2,
                        },
                        account_keys: [
                            4NxuZhiHzeL6pENbPpP4GuHadHHu1jSDGkz5MDBW1tMR,
                            ComputeBudget111111111111111111111111111111,
                            NeonVMyRX5GbCrsAHnUwx1nYYoJAtskU1bWUo6JGNyG,
                        ],
                        recent_blockhash: FYkzc2pPRM5iW93tLg1GJRMk75JESg5EAVY4Tsd2sfvy,
                        instructions: [
                            CompiledInstruction {
                                program_id_index: 1,
                                accounts: [],
                                data: [
                                    1,
                                    0,
                                    0,
                                    4,
                                    0,
                                ],
                            },
                            CompiledInstruction {
                                program_id_index: 1,
                                accounts: [],
                                data: [
                                    2,
                                    192,
                                    92,
                                    21,
                                    0,
                                ],
                            },
                            CompiledInstruction {
                                program_id_index: 1,
                                accounts: [],
                                data: [
                                    3,
                                    48,
                                    117,
                                    0,
                                    0,
                                    0,
                                    0,
                                    0,
                                    0,
                                ],
                            },
                            CompiledInstruction {
                                program_id_index: 2,
                                accounts: [
                                    8,
                                    0,
                                    10,
                                    13,
                                    27,
                                    2,
                                    7,
                                    31,
                                    20,
                                    14,
                                    6,
                                    25,
                                    18,
                                    17,
                                    24,
                                    29,
                                    30,
                                    28,
                                    21,
                                    23,
                                    5,
                                    33,
                                    11,
                                    19,
                                    12,
                                    16,
                                    26,
                                    9,
                                    32,
                                    15,
                                    22,
                                    4,
                                    3,
                                ],
                                data: [
                                    33,
                                    16,
                                    0,
                                    0,
                                    0,
                                    244,
                                    1,
                                    0,
                                    0,
                                    8,
                                    0,
                                    0,
                                    0,
                                ],
                            },
                        ],
                        address_table_lookups: [
                            MessageAddressTableLookup {
                                account_key: 5JmBLtFy2a8vbz14pttFEwNBXDC6PfdU3fC8dVoFFVQ5,
                                writable_indexes: [
                                    1,
                                    3,
                                    5,
                                    8,
                                    9,
                                    10,
                                    11,
                                    13,
                                    14,
                                    15,
                                    16,
                                    17,
                                    18,
                                    21,
                                    24,
                                    25,
                                    26,
                                    29,
                                ],
                                readonly_indexes: [
                                    0,
                                    2,
                                    4,
                                    6,
                                    7,
                                    12,
                                    19,
                                    20,
                                    22,
                                    23,
                                    27,
                                    28,
                                    30,
                                ],
                            },
                        ],
                    },
                    loaded_addresses: LoadedAddresses {
                        writable: [
                            NFDicejYdJAkXzmCYewDupc1nW81uaQsDSGCyynGfPx,
                            Hupohbo7EFAgH7TQAykaiRHw87vuKGaMkHQHPcNBLpVd,
                            C9ouTkDQWzgLfwJhvB9c3LiKTYTZFUiqvS6P9FdRh8LU,
                            48W4UZeZuR1fc1strDG2qjctHkK6B6ueqYBXcsT9ge9W,
                            2JjcBgbwgoRwAQPAsLvQMAiryPzMx22GrYDhnSamwdhA,
                            HpXM2eqxUyU5nTmV4JNvy8YxgmvdXiRU77jemrvVrg5g,
                            Ew3ds9DqTnuAf6BwnRTouk4RPcEDuCtHejPbtU8NevEs,
                            HjR6rgNTuw8oP4GKjYNPdFSygGztg3UKAbMkYgBHawCa,
                            CWLGxiFYHKi6YDHgED2nEGP3DnSyKVGBgXt52SKMturu,
                            ESCR2FTLWzkKHwoG2mkQcJxwgjUC4oqWbtuBKPpeiDFd,
                            555MzvmcV1xbxZ8r7rVmpg8UUZQWg6yajm69cgzibNUt,
                            3SsLJzo49RVE7FFEiSWMSi637RQQzWVX7efj8itin9Zj,
                            HF9QTeAEcKGzp75J7YicQ7VXuAEYfCyEntH88qTk4Dgy,
                            ETgzehqcb7vA55q2RSMiCmgr8coogwoRNw9KZYdF2MyK,
                            5Lm7nBLnoKouQzg2ULVQkzJ57PfafRctzsUsJigHsPtU,
                            5KuJMkRzVGgzy9EYQHMjsGBhZe2ZXouCeJ5gjn7d4WKj,
                            DAq5V72NBwiz7G3xivnbqq6kBCRRVXBZiUTgdnfwenMP,
                            3KsEA5Z3NDHz85g7byBEP4T9489NKG6VFEMKExSBmT3o,
                        ],
                        readonly: [
                            BhPPTPCPSSDjBLqiZpwjVSmDNrTHBUBgGx746vuWhEHg,
                            HLMwQyzfoxnR2QW23Xkgx7vcK5aDXRnBVkj1nLVQ8SoV,
                            BypdCd5tuJnyoihHg3SVW7qqRsqyVVccg2EURUX3twrg,
                            6EscUPSWFVHpbtoELx6CmR4TdNUq77yeHpbVx77ZUB9u,
                            4bwQcuoDPg2rsfjjSwBT2oARfFBm34M7aoWwnRmRje66,
                            EWgjAiifMhPvRaVLsSYQ7JSjcHbYwcGmyJbQU6k3ukfp,
                            11111111111111111111111111111111,
                            AXisyaUthrsf9nxai11AuVrJzyC7rrxuthVGFDo4MvUp,
                            7AL3iWyLCmKmxuaCXZDiDsYUMaB1iwrSDLmSryihQrAd,
                            9bBrVxJkX61vhuYXzJ8K91JtFrctDbesReju6qf5pkM1,
                            2hzhEy9GJUYM3v7uocc2TtuuHrvYwTHy4wXiyLnQTGf9,
                            FxnaU7UHiaqBCCEejvqmsSnRvqJW4e7HiUsy9uJQUHVi,
                            CHQjfH7AaxHBfgi8g8HRcZGPJgNMjQ3ofNroEmZUsoXs,
                        ],
                    },
                    is_writable_account_cache: [
                        true,
                        false,
                        false,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        true,
                        false,
                        false,
                        false,
                        false,
                        false,
                        false,
                        false,
                        false,
                        false,
                        false,
                        false,
                        false,
                        false,
                    ],
                },
            ),
            message_hash: 7twwExqqn5GccbEfjTrByUWks2i8Jtd5QzeZzgJYmQNr,
            is_simple_vote_tx: false,
            signatures: [
                4QdDG3fjk4vLLHEpxrFYUMux49Eg4vVaynaiKA9fJR64ZSoEcBA4xPpSYAfnSxoB1p2GQAruh8fPoXsUgX5YdZsj,
            ],
        },
        transaction_status_meta: TransactionStatusMeta {
            status: Ok(
                (),
            ),
            fee: 47000,
            pre_balances: [
                4498256550,
                1,
                1141440,
                1517280,
                170227680,
                1517280,
                2206320,
                1517280,
                1825413120,
                27123120,
                16190880,
                2206320,
                30338640,
                1385040,
                1517280,
                1746960,
                1517280,
                1385040,
                173881680,
                30338640,
                1517280,
                1517280,
                2665680,
                1517280,
                1517280,
                1976640,
                1517280,
                1,
                1517280,
                0,
                1517280,
                1517280,
                0,
                1517280,
            ],
            post_balances: [
                4498204550,
                1,
                1141440,
                1517280,
                170227680,
                1517280,
                2206320,
                1517280,
                1825413120,
                27123120,
                16195880,
                2206320,
                30338640,
                1385040,
                1517280,
                1746960,
                1517280,
                1385040,
                173881680,
                30338640,
                1517280,
                1517280,
                2665680,
                1517280,
                1517280,
                1976640,
                1517280,
                1,
                1517280,
                0,
                1517280,
                1517280,
                0,
                1517280,
            ],
            inner_instructions: Some(
                [
                    InnerInstructions {
                        index: 3,
                        instructions: [
                            InnerInstruction {
                                instruction: CompiledInstruction {
                                    program_id_index: 27,
                                    accounts: [
                                        0,
                                        10,
                                    ],
                                    data: [
                                        2,
                                        0,
                                        0,
                                        0,
                                        136,
                                        19,
                                        0,
                                        0,
                                        0,
                                        0,
                                        0,
                                        0,
                                    ],
                                },
                                stack_height: Some(
                                    2,
                                ),
                            },
                        ],
                    },
                ],
            ),
            log_messages: Some(
                [
                    "Program ComputeBudget111111111111111111111111111111 invoke [1]",
                    "Program ComputeBudget111111111111111111111111111111 success",
                    "Program ComputeBudget111111111111111111111111111111 invoke [1]",
                    "Program ComputeBudget111111111111111111111111111111 success",
                    "Program ComputeBudget111111111111111111111111111111 invoke [1]",
                    "Program ComputeBudget111111111111111111111111111111 success",
                    "Program NeonVMyRX5GbCrsAHnUwx1nYYoJAtskU1bWUo6JGNyG invoke [1]",
                    "Program log: Instruction: Begin or Continue Transaction from Account",
                    "Program data: SEFTSA== kLpcy2aXFKuEgMiMFiDoyFRH5c8rR3Y6IW90UFonrRg=",
                    "Program data: TE9HMw== Q94td7+AJ+JdvRebSR6NZPODmKo= Aw== 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",
                    "Program data: RU5URVI= U1RBVElDQ0FMTA== ICw15Rf6gDtTdWXEDwppZdcgRgk=",
                    "Program data: RVhJVA== UkVUVVJO",
                    "Program 11111111111111111111111111111111 invoke [2]",
                    "Program 11111111111111111111111111111111 success",
                    "Program data: R0FT ECcAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA= 2HwPAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=",
                    "Program NeonVMyRX5GbCrsAHnUwx1nYYoJAtskU1bWUo6JGNyG consumed 238250 of 1399550 compute units",
                    "Program NeonVMyRX5GbCrsAHnUwx1nYYoJAtskU1bWUo6JGNyG success",
                ],
            ),
            pre_token_balances: Some(
                [],
            ),
            post_token_balances: Some(
                [],
            ),
            rewards: Some(
                [],
            ),
            loaded_addresses: LoadedAddresses {
                writable: [
                    NFDicejYdJAkXzmCYewDupc1nW81uaQsDSGCyynGfPx,
                    Hupohbo7EFAgH7TQAykaiRHw87vuKGaMkHQHPcNBLpVd,
                    C9ouTkDQWzgLfwJhvB9c3LiKTYTZFUiqvS6P9FdRh8LU,
                    48W4UZeZuR1fc1strDG2qjctHkK6B6ueqYBXcsT9ge9W,
                    2JjcBgbwgoRwAQPAsLvQMAiryPzMx22GrYDhnSamwdhA,
                    HpXM2eqxUyU5nTmV4JNvy8YxgmvdXiRU77jemrvVrg5g,
                    Ew3ds9DqTnuAf6BwnRTouk4RPcEDuCtHejPbtU8NevEs,
                    HjR6rgNTuw8oP4GKjYNPdFSygGztg3UKAbMkYgBHawCa,
                    CWLGxiFYHKi6YDHgED2nEGP3DnSyKVGBgXt52SKMturu,
                    ESCR2FTLWzkKHwoG2mkQcJxwgjUC4oqWbtuBKPpeiDFd,
                    555MzvmcV1xbxZ8r7rVmpg8UUZQWg6yajm69cgzibNUt,
                    3SsLJzo49RVE7FFEiSWMSi637RQQzWVX7efj8itin9Zj,
                    HF9QTeAEcKGzp75J7YicQ7VXuAEYfCyEntH88qTk4Dgy,
                    ETgzehqcb7vA55q2RSMiCmgr8coogwoRNw9KZYdF2MyK,
                    5Lm7nBLnoKouQzg2ULVQkzJ57PfafRctzsUsJigHsPtU,
                    5KuJMkRzVGgzy9EYQHMjsGBhZe2ZXouCeJ5gjn7d4WKj,
                    DAq5V72NBwiz7G3xivnbqq6kBCRRVXBZiUTgdnfwenMP,
                    3KsEA5Z3NDHz85g7byBEP4T9489NKG6VFEMKExSBmT3o,
                ],
                readonly: [
                    BhPPTPCPSSDjBLqiZpwjVSmDNrTHBUBgGx746vuWhEHg,
                    HLMwQyzfoxnR2QW23Xkgx7vcK5aDXRnBVkj1nLVQ8SoV,
                    BypdCd5tuJnyoihHg3SVW7qqRsqyVVccg2EURUX3twrg,
                    6EscUPSWFVHpbtoELx6CmR4TdNUq77yeHpbVx77ZUB9u,
                    4bwQcuoDPg2rsfjjSwBT2oARfFBm34M7aoWwnRmRje66,
                    EWgjAiifMhPvRaVLsSYQ7JSjcHbYwcGmyJbQU6k3ukfp,
                    11111111111111111111111111111111,
                    AXisyaUthrsf9nxai11AuVrJzyC7rrxuthVGFDo4MvUp,
                    7AL3iWyLCmKmxuaCXZDiDsYUMaB1iwrSDLmSryihQrAd,
                    9bBrVxJkX61vhuYXzJ8K91JtFrctDbesReju6qf5pkM1,
                    2hzhEy9GJUYM3v7uocc2TtuuHrvYwTHy4wXiyLnQTGf9,
                    FxnaU7UHiaqBCCEejvqmsSnRvqJW4e7HiUsy9uJQUHVi,
                    CHQjfH7AaxHBfgi8g8HRcZGPJgNMjQ3ofNroEmZUsoXs,
                ],
            },
            return_data: None,
            compute_units_consumed: Some(
                238756,
            ),
        },
        index: 806,
    })
```

Replaying the ledger is a slow process, it took about 1 hour and 18 minutes for about `9308 (257207163 - 257197855)`
slots:

```bash
[2024-04-17T23:49:27.914537807Z INFO  solana_ledger::blockstore_processor] ledger processed in 1 hour, 18 minutes, 32 seconds, 574 ms, 656 µs and 192 ns. root slot is 257207163, 1 bank: 257207163
[2024-04-17T23:49:27.977465869Z INFO  solana_runtime::accounts_background_service] AccountsBackgroundService has stopped
[2024-04-17T23:49:28.292699419Z INFO  solana_core::accounts_hash_verifier] AccountsHashVerifier has stopped
[2024-04-17T23:49:29.663924996Z INFO  agave_ledger_tool] ledger tool took 5265.6s
```

The total time for this process is about 210 minutes (3 hours and 30 minutes):

- 94 minutes for downloading the ledger archive
- 37 minutes for extracting the ledger archive
- 79 minutes for replaying around 9308 slots

The snapshot
archive [mainnet-beta-ledger-europe-fr2/257034560/hourly/snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst](https://console.cloud.google.com/storage/browser/_details/mainnet-beta-ledger-europe-fr2/257034560/hourly/snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst;tab=live_object)
has a size of 63 GB. When extracted, the size of the content is 228 GB, so the state of all Solana accounts at a
specific slot is 228 GB at the moment.
