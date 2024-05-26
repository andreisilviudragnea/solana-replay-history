# Ledger tool

In order to get the state of a Solana account at a specific slot from a snapshot archive,
you can use the following command from [Agave master](git@github.com:anza-xyz/agave.git) branch:

```bash
~/solana/target/release/solana-ledger-tool --force-update-to-open accounts --account NeonVMyRX5GbCrsAHnUwx1nYYoJAtskU1bWUo6JGNyG
```

Example output:

```
~/solana/target/release/solana-ledger-tool --force-update-to-open accounts --account NeonVMyRX5GbCrsAHnUwx1nYYoJAtskU1bWUo6JGNyG
[2024-05-26T13:26:13.855655211Z INFO  solana_ledger_tool] solana-ledger-tool 2.0.0 (src:00000000; feat:3257402020, client:SolanaLabs)
[2024-05-26T13:26:13.856058332Z INFO  solana_ledger::blockstore] Maximum open file descriptors: 1000000
[2024-05-26T13:26:13.856066513Z INFO  solana_ledger::blockstore] Opening blockstore at "/mnt/ledger/rocksdb"
[2024-05-26T13:26:13.865158668Z INFO  solana_ledger::blockstore_db] Opening Rocks with secondary (read only) access at: "/mnt/ledger/rocksdb/solana-secondary". This secondary access could temporarily degrade other accesses, such as by solana-validator
Failed to open blockstore at "/mnt/ledger", it is missing at least one critical file: Error { message: "IO error: No such file or directory: While opening a file for sequentially reading: /mnt/ledger/rocksdb/CURRENT: No such file or directory" }
[2024-05-26T13:26:13.867278813Z INFO  solana_ledger_tool::ledger_utils] Attempting to temporarily open blockstore with Primary access in order to update
[2024-05-26T13:26:13.867291541Z INFO  solana_ledger::blockstore] Maximum open file descriptors: 1000000
[2024-05-26T13:26:13.867294309Z INFO  solana_ledger::blockstore] Opening blockstore at "/mnt/ledger/rocksdb"
[2024-05-26T13:26:13.867395828Z WARN  solana_ledger::blockstore_db] Unable to detect Rocks columns: Error { message: "IO error: No such file or directory: While opening a file for sequentially reading: /mnt/ledger/rocksdb/CURRENT: No such file or directory" }
[2024-05-26T13:26:13.961471505Z INFO  solana_ledger::blockstore_db] Rocks's automatic compactions are disabled due to PrimaryForMaintenance access
[2024-05-26T13:26:13.961509922Z INFO  solana_ledger::blockstore] Opening blockstore done; blockstore open took 94ms
[2024-05-26T13:26:13.963272005Z INFO  solana_ledger_tool::ledger_utils] Blockstore forced open succeeded, retrying with original access: Secondary
[2024-05-26T13:26:13.963292971Z INFO  solana_ledger::blockstore] Maximum open file descriptors: 1000000
[2024-05-26T13:26:13.963296059Z INFO  solana_ledger::blockstore] Opening blockstore at "/mnt/ledger/rocksdb"
[2024-05-26T13:26:13.963407452Z INFO  solana_ledger::blockstore_db] Opening Rocks with secondary (read only) access at: "/mnt/ledger/rocksdb/solana-secondary". This secondary access could temporarily degrade other accesses, such as by solana-validator
[2024-05-26T13:26:13.978651079Z INFO  solana_ledger::blockstore_db] Rocks's automatic compactions are disabled due to Secondary access
[2024-05-26T13:26:13.978678110Z INFO  solana_ledger::blockstore] Opening blockstore done; blockstore open took 15ms
[2024-05-26T13:26:13.979103413Z INFO  solana_ledger_tool::ledger_utils] Default accounts path is switched aligning with Blockstore's secondary access: "/mnt/ledger/ledger_tool/accounts"
[2024-05-26T13:26:13.979128712Z WARN  solana_accounts_db::utils] Failed to delete contents of '/mnt/ledger/ledger_tool/accounts': could not read dir: No such file or directory (os error 2)
[2024-05-26T13:26:13.979218086Z INFO  solana_ledger_tool::ledger_utils] Cleaning contents of account path: /mnt/ledger/ledger_tool/accounts/run
[2024-05-26T13:26:13.979317959Z INFO  solana_ledger_tool::ledger_utils] Cleaning account paths took 101us
[2024-05-26T13:26:13.979326065Z INFO  solana_ledger_tool::ledger_utils] Cleaning contents of account snapshot paths: ["/mnt/ledger/ledger_tool/accounts/snapshot"]
[2024-05-26T13:26:13.979334172Z INFO  solana_runtime::snapshot_utils] Unable to read bank snapshots directory '/mnt/ledger/ledger_tool/snapshot': No such file or directory (os error 2)
[2024-05-26T13:26:13.979401348Z INFO  solana_ledger::bank_forks_utils] Initializing bank snapshots dir: /mnt/ledger/ledger_tool/snapshot
[2024-05-26T13:26:13.979443490Z INFO  solana_runtime::snapshot_bank_utils] Loading bank from full snapshot archive: /mnt/ledger/snapshot-257197855-jEyCvNxd8BJWA2XJvXb6vvDxbtZnFvz6WQBaVxnxkog.tar.zst, and incremental snapshot archive: None
[2024-05-26T13:26:30.302574753Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 62341/411528 slots with 0 collisions
[2024-05-26T13:26:32.304341875Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 69878/411528 slots with 0 collisions
[2024-05-26T13:26:34.306126599Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 76844/411528 slots with 0 collisions
[2024-05-26T13:26:36.307879329Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 84561/411528 slots with 0 collisions
[2024-05-26T13:26:38.309622285Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 92087/411528 slots with 0 collisions
[2024-05-26T13:26:40.311425503Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 98825/411528 slots with 0 collisions
[2024-05-26T13:26:42.313093188Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 104442/411528 slots with 0 collisions
[2024-05-26T13:26:44.314658139Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 112130/411528 slots with 0 collisions
[2024-05-26T13:26:46.316294518Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 119720/411528 slots with 0 collisions
[2024-05-26T13:26:48.317921173Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 127244/411528 slots with 0 collisions
[2024-05-26T13:26:50.319619498Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 134183/411528 slots with 0 collisions
[2024-05-26T13:26:52.321419940Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 139918/411528 slots with 0 collisions
[2024-05-26T13:26:54.323215596Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 145833/411528 slots with 0 collisions
[2024-05-26T13:26:56.324975866Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 152527/411528 slots with 0 collisions
[2024-05-26T13:26:58.326769604Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 157841/411528 slots with 0 collisions
[2024-05-26T13:27:00.328457126Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 162314/411528 slots with 0 collisions
[2024-05-26T13:27:02.330492319Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 166411/411528 slots with 0 collisions
[2024-05-26T13:27:04.332578454Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 170511/411528 slots with 0 collisions
[2024-05-26T13:27:06.334791015Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 174418/411528 slots with 0 collisions
[2024-05-26T13:27:08.336869623Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 178051/411528 slots with 0 collisions
[2024-05-26T13:27:10.338935872Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 182171/411528 slots with 0 collisions
[2024-05-26T13:27:12.340758912Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 187675/411528 slots with 0 collisions
[2024-05-26T13:27:14.342373284Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 194638/411528 slots with 0 collisions
[2024-05-26T13:27:16.343998676Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 201764/411528 slots with 0 collisions
[2024-05-26T13:27:18.345651366Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 208448/411528 slots with 0 collisions
[2024-05-26T13:27:20.347337602Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 215113/411528 slots with 0 collisions
[2024-05-26T13:27:22.349101085Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 221255/411528 slots with 0 collisions
[2024-05-26T13:27:24.350948076Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 227348/411528 slots with 0 collisions
[2024-05-26T13:27:26.352775808Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 233408/411528 slots with 0 collisions
[2024-05-26T13:27:28.354550826Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 239741/411528 slots with 0 collisions
[2024-05-26T13:27:30.356450524Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 246042/411528 slots with 0 collisions
[2024-05-26T13:27:32.358231023Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 250243/411528 slots with 0 collisions
[2024-05-26T13:27:34.360033005Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 254495/411528 slots with 0 collisions
[2024-05-26T13:27:36.361855740Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 261349/411528 slots with 0 collisions
[2024-05-26T13:27:38.363505598Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 269156/411528 slots with 0 collisions
[2024-05-26T13:27:40.365149149Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 276864/411528 slots with 0 collisions
[2024-05-26T13:27:42.366712919Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 284524/411528 slots with 0 collisions
[2024-05-26T13:27:44.368320228Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 292386/411528 slots with 0 collisions
[2024-05-26T13:27:46.369886326Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 300259/411528 slots with 0 collisions
[2024-05-26T13:27:48.371251200Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 308157/411528 slots with 0 collisions
[2024-05-26T13:27:50.372546821Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 315902/411528 slots with 0 collisions
[2024-05-26T13:27:52.373909491Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 323665/411528 slots with 0 collisions
[2024-05-26T13:27:54.375439134Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 331463/411528 slots with 0 collisions
[2024-05-26T13:27:56.377029247Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 336676/411528 slots with 0 collisions
[2024-05-26T13:27:58.378715563Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 340675/411528 slots with 0 collisions
[2024-05-26T13:28:00.380437634Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 343805/411528 slots with 0 collisions
[2024-05-26T13:28:02.382181210Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 347002/411528 slots with 0 collisions
[2024-05-26T13:28:04.383840180Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 350041/411528 slots with 0 collisions
[2024-05-26T13:28:06.385659165Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 353204/411528 slots with 0 collisions
[2024-05-26T13:28:08.387385085Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 356348/411528 slots with 0 collisions
[2024-05-26T13:28:10.389036594Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 362228/411528 slots with 0 collisions
[2024-05-26T13:28:12.390631896Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 368085/411528 slots with 0 collisions
[2024-05-26T13:28:14.392261927Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 373852/411528 slots with 0 collisions
[2024-05-26T13:28:16.393867196Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 379735/411528 slots with 0 collisions
[2024-05-26T13:28:18.395495264Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 386146/411528 slots with 0 collisions
[2024-05-26T13:28:20.397135249Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 393986/411528 slots with 0 collisions
[2024-05-26T13:28:22.398774220Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 401732/411528 slots with 0 collisions
[2024-05-26T13:28:24.400420658Z INFO  solana_runtime::snapshot_utils::snapshot_storage_rebuilder] rebuilt storages for 409471/411528 slots with 0 collisions
[2024-05-26T13:28:24.903295665Z INFO  solana_accounts_db::shared_buffer_reader] reading entire decompressed file took: 130923088 us, bytes: 244809268736, read_us: 130621864, waiting_for_buffer_us: 0, largest fetch: 11272192, error: Ok(0)
[2024-05-26T13:28:24.925569093Z INFO  solana_accounts_db::hardened_unpack] unpacked 102883 entries total
[2024-05-26T13:28:24.925780679Z INFO  solana_accounts_db::hardened_unpack] unpacked 102884 entries total
[2024-05-26T13:28:24.926079680Z INFO  solana_accounts_db::hardened_unpack] unpacked 102884 entries total
[2024-05-26T13:28:24.945900940Z INFO  solana_accounts_db::hardened_unpack] unpacked 102883 entries total
[2024-05-26T13:28:25.448284207Z INFO  solana_runtime::snapshot_utils] snapshot untar took 131.4s
[2024-05-26T13:28:25.461073063Z INFO  solana_runtime::snapshot_utils] snapshot version: 1.2.0
[2024-05-26T13:28:25.461125676Z INFO  solana_runtime::snapshot_bank_utils] Rebuilding bank from full snapshot /mnt/ledger/ledger_tool/snapshot/tmp-snapshot-archive-WDWpZj/snapshots/257197855/257197855 and incremental snapshot None
[2024-05-26T13:28:36.690250855Z INFO  solana_accounts_db::accounts_db] Background account hasher has started
[2024-05-26T13:28:38.782130233Z INFO  solana_accounts_db::accounts_db] generating index: 1813/411528 slots... (906/s)
[2024-05-26T13:28:40.782142505Z INFO  solana_accounts_db::accounts_db] generating index: 114366/411528 slots... (28591/s)
[2024-05-26T13:28:42.783292096Z INFO  solana_accounts_db::accounts_db] generating index: 210850/411528 slots... (35141/s)
[2024-05-26T13:28:45.479009551Z INFO  solana_accounts_db::accounts_db] rent_collector: RentCollector { epoch: 595, epoch_schedule: EpochSchedule { slots_per_epoch: 432000, leader_schedule_slot_offset: 432000, warmup: false, first_normal_epoch: 0, first_normal_slot: 0 }, slots_per_year: 78892314.984, rent: Rent { lamports_per_byte_year: 3480, exemption_threshold: 2.0, burn_percent: 100 } }
[2024-05-26T13:28:46.686734738Z INFO  solana_metrics::metrics] metrics disabled: environment variable not found
[2024-05-26T13:28:46.688058134Z INFO  solana_metrics::metrics] datapoint: accounts_index_startup estimate_mem_bytes=0i flush_should_evict_us=0i count_in_mem=0i count=264721369i bg_waiting_percent=3.5549569129943848 bg_throttling_wait_percent=0 slot_list_len=0i ref_count=0i slot_list_cached=0i min_in_bin_mem=0i max_in_bin_mem=0i count_from_bins_mem=0i median_from_bins_mem=0i min_in_bin_disk=372i max_in_bin_disk=212772i count_from_bins_disk=264721369i median_from_bins_disk=55778i gets_from_mem=0i get_mem_us=0i gets_missing=0i get_missing_us=0i entries_from_mem=0i entry_mem_us=0i load_disk_found_count=0i load_disk_found_us=0i load_disk_missing_count=0i load_disk_missing_us=0i entries_missing=0i entry_missing_us=0i failed_to_evict=0i updates_in_mem=0i get_range_us=0i inserts=264721369i deletes=0i active_threads=32i items=0i keys=0i ms_per_age=5000i buckets_scanned=9041i flush_scan_us=17i flush_update_us=0i flush_grow_us=0i flush_evict_us=0i disk_index_resizes=9073i disk_index_failed_resizes=261i disk_index_max_size=439637i disk_index_new_file_us=1185015i disk_index_resize_us=42374666i disk_index_flush_file_us=6i disk_index_index_file_size=18845211552i disk_index_data_file_size=0i disk_index_data_file_count=0i disk_index_find_index_entry_mut_us=26372116i disk_index_flush_mmap_us=7653321i disk_data_resizes=0i disk_data_max_size=0i disk_data_new_file_us=0i disk_data_resize_us=0i disk_data_flush_file_us=0i disk_data_flush_mmap_us=0i flush_entries_updated_on_disk=0i flush_entries_evicted_from_mem=0i
[2024-05-26T13:28:56.717864888Z INFO  solana_metrics::metrics] datapoint: accounts_index_startup entries_created=428770665i entries_reused=0i
[2024-05-26T13:28:56.717888457Z INFO  solana_metrics::metrics] datapoint: accounts_index_startup estimate_mem_bytes=0i flush_should_evict_us=0i count_in_mem=0i count=428770665i bg_waiting_percent=0 bg_throttling_wait_percent=0 slot_list_len=0i ref_count=0i slot_list_cached=0i min_in_bin_mem=0i max_in_bin_mem=0i count_from_bins_mem=0i median_from_bins_mem=0i min_in_bin_disk=1201i max_in_bin_disk=212772i count_from_bins_disk=428770665i median_from_bins_disk=56541i gets_from_mem=0i get_mem_us=0i gets_missing=0i get_missing_us=0i entries_from_mem=0i entry_mem_us=0i load_disk_found_count=0i load_disk_found_us=0i load_disk_missing_count=0i load_disk_missing_us=0i entries_missing=0i entry_missing_us=0i failed_to_evict=0i updates_in_mem=0i get_range_us=0i inserts=164049296i deletes=0i active_threads=32i items=0i keys=0i ms_per_age=20502i buckets_scanned=4007i flush_scan_us=8i flush_update_us=0i flush_grow_us=0i flush_evict_us=0i disk_index_resizes=4164i disk_index_failed_resizes=2512i disk_index_max_size=160170i disk_index_new_file_us=220809i disk_index_resize_us=250914207i disk_index_flush_file_us=1i disk_index_index_file_size=31885677072i disk_index_data_file_size=0i disk_index_data_file_count=0i disk_index_find_index_entry_mut_us=21938306i disk_index_flush_mmap_us=350799i disk_data_resizes=0i disk_data_max_size=0i disk_data_new_file_us=0i disk_data_resize_us=0i disk_data_flush_file_us=0i disk_data_flush_mmap_us=0i flush_entries_updated_on_disk=0i flush_entries_evicted_from_mem=0i
[2024-05-26T13:29:06.720870954Z INFO  solana_metrics::metrics] datapoint: accounts_index_startup entries_created=31428479i entries_reused=0i
[2024-05-26T13:29:06.720891807Z INFO  solana_metrics::metrics] datapoint: accounts_index_startup estimate_mem_bytes=0i flush_should_evict_us=0i count_in_mem=0i count=460199144i bg_waiting_percent=0 bg_throttling_wait_percent=0 slot_list_len=0i ref_count=0i slot_list_cached=0i min_in_bin_mem=0i max_in_bin_mem=0i count_from_bins_mem=0i median_from_bins_mem=0i min_in_bin_disk=1849i max_in_bin_disk=212772i count_from_bins_disk=460199144i median_from_bins_disk=56615i gets_from_mem=0i get_mem_us=0i gets_missing=0i get_missing_us=0i entries_from_mem=0i entry_mem_us=0i load_disk_found_count=0i load_disk_found_us=0i load_disk_missing_count=0i load_disk_missing_us=0i entries_missing=0i entry_missing_us=0i failed_to_evict=0i updates_in_mem=0i get_range_us=0i inserts=31428479i deletes=0i active_threads=32i items=0i keys=0i ms_per_age=44975i buckets_scanned=1822i flush_scan_us=1i flush_update_us=0i flush_grow_us=0i flush_evict_us=0i disk_index_resizes=1859i disk_index_failed_resizes=2620i disk_index_max_size=142506i disk_index_new_file_us=126295i disk_index_resize_us=307733848i disk_index_flush_file_us=1i disk_index_index_file_size=35714355408i disk_index_data_file_size=0i disk_index_data_file_count=0i disk_index_find_index_entry_mut_us=14556087i disk_index_flush_mmap_us=152090i disk_data_resizes=0i disk_data_max_size=0i disk_data_new_file_us=0i disk_data_resize_us=0i disk_data_flush_file_us=0i disk_data_flush_mmap_us=0i flush_entries_updated_on_disk=0i flush_entries_evicted_from_mem=0i
[2024-05-26T13:29:12.822848347Z INFO  solana_accounts_db::accounts_db] accounts data len: 158169733357
[2024-05-26T13:29:12.925169502Z INFO  solana_metrics::metrics] datapoint: generate_index overall_us=36178257i total_us=8701383i scan_stores_us=3918934i insertion_time_us=366660517i min_bin_size=0i max_bin_size=0i storage_size_storages_us=85807i index_flush_us=24845916i total_rent_paying=0i amount_to_top_off_rent=0i total_items_including_duplicates=490416085i total_items=0i accounts_data_len_dedup_time_us=664848i total_duplicate_slot_keys=36274794i populate_duplicate_keys_us=1826345i total_slots=411528i slots_to_clean=411467i copy_data_us=206490572i
[2024-05-26T13:29:12.928510916Z INFO  solana_metrics::metrics] datapoint: reconstruct_accountsdb_from_fields() accountsdb-notify-at-start-us=36181756i
[2024-05-26T13:29:16.730342470Z INFO  solana_metrics::metrics] datapoint: accounts_index_startup entries_created=4016230i entries_reused=0i
[2024-05-26T13:29:16.730916423Z INFO  solana_metrics::metrics] datapoint: accounts_index_startup estimate_mem_bytes=493620432i flush_should_evict_us=43444i count_in_mem=10285311i count=464215374i bg_waiting_percent=31.774633407592773 bg_throttling_wait_percent=26.142072677612305 slot_list_len=0i ref_count=278819i slot_list_cached=0i min_in_bin_mem=992i max_in_bin_mem=56070i count_from_bins_mem=10284873i median_from_bins_mem=1233i min_in_bin_disk=55738i max_in_bin_disk=212772i count_from_bins_disk=464215374i median_from_bins_disk=56625i gets_from_mem=10565639i get_mem_us=404036i gets_missing=212655i get_missing_us=12144i entries_from_mem=16126628i entry_mem_us=955980i load_disk_found_count=10286512i load_disk_found_us=1427749i load_disk_missing_count=226i load_disk_missing_us=50i entries_missing=10074083i entry_missing_us=1478064i failed_to_evict=0i updates_in_mem=26200711i get_range_us=0i inserts=4016230i deletes=0i active_threads=1i items=0i keys=0i ms_per_age=625i buckets_scanned=745027i flush_scan_us=88880i flush_update_us=200974i flush_grow_us=0i flush_evict_us=5044i disk_index_resizes=561i disk_index_failed_resizes=946i disk_index_max_size=134741i disk_index_new_file_us=38038i disk_index_resize_us=114937394i disk_index_flush_file_us=0i disk_index_index_file_size=36613369536i disk_index_data_file_size=0i disk_index_data_file_count=0i disk_index_find_index_entry_mut_us=4429571i disk_index_flush_mmap_us=33492i disk_data_resizes=0i disk_data_max_size=0i disk_data_new_file_us=0i disk_data_resize_us=0i disk_data_flush_file_us=0i disk_data_flush_mmap_us=0i flush_entries_updated_on_disk=0i flush_entries_evicted_from_mem=1200i
[2024-05-26T13:29:21.534449797Z INFO  solana_runtime::bank] Rebuilding skipped rewrites of 1060 accounts took 7ms
[2024-05-26T13:29:21.534627144Z INFO  solana_metrics::metrics] datapoint: bank-new-from-fields accounts_data_len-from-snapshot=158223682207i accounts_data_len-from-generate_index=158169733357i stakes_accounts_load_duration_us=8588811i
[2024-05-26T13:29:21.843486672Z INFO  solana_runtime::serde_snapshot] rent_collector: RentCollector { epoch: 595, epoch_schedule: EpochSchedule { slots_per_epoch: 432000, leader_schedule_slot_offset: 432000, warmup: false, first_normal_epoch: 0, first_normal_slot: 0 }, slots_per_year: 78892314.984, rent: Rent { lamports_per_byte_year: 3480, exemption_threshold: 2.0, burn_percent: 50 } }
[2024-05-26T13:29:21.843723136Z INFO  solana_runtime::snapshot_bank_utils] Rebuilding status cache from /mnt/ledger/ledger_tool/snapshot/tmp-snapshot-archive-WDWpZj/snapshots/status_cache
[2024-05-26T13:29:22.511682828Z INFO  solana_runtime::snapshot_bank_utils] Rebuilt bank for slot: 257197855
[2024-05-26T13:29:22.521890766Z INFO  solana_runtime::snapshot_bank_utils] rebuild bank from snapshots took 57.1s
[2024-05-26T13:29:22.522141573Z INFO  solana_runtime::bank] Cleaning... Skipped.
[2024-05-26T13:29:22.522146606Z INFO  solana_runtime::bank] Shrinking... Skipped.
[2024-05-26T13:29:22.522151933Z INFO  solana_runtime::bank] Verifying accounts...
[2024-05-26T13:29:22.522152363Z INFO  solana_metrics::metrics] datapoint: bank-get_epoch_accounts_hash_to_serialize slot=257197855i waiting-time-us=0i
[2024-05-26T13:29:22.522214719Z INFO  solana_runtime::bank] Verifying accounts... In background.
[2024-05-26T13:29:22.522218560Z INFO  solana_runtime::bank] Verifying bank...
[2024-05-26T13:29:22.522570713Z INFO  solana_runtime::bank] Initial background accounts hash verification has started
[2024-05-26T13:29:22.523622347Z INFO  solana_accounts_db::accounts_db] skipped rewrite hashes 257197855 0
[2024-05-26T13:29:22.524649296Z INFO  solana_runtime::bank] bank frozen: 257197855 hash: 43Y1Q528CrRzH9jo5acBd5Xp8EGTzBH1tXBVwqxcG6Qs accounts_delta: BGdQN9RfKzrrLJ2qZZAEtANFNFdhZrcPUL85fowyR5Bj signature_count: 521 last_blockhash: GPNd58rNZ1FBe3umcujPuEUrthS6ccvr1vkLhMEiQW7C capitalization: 572900386253520003, stats: BankHashStats { num_updated_accounts: 2538, num_removed_accounts: 11, num_lamports_stored: 68172021857421, total_data_len: 14665644, num_executable_accounts: 0 }
[2024-05-26T13:29:22.524678597Z INFO  solana_runtime::bank] Verifying bank... Done.
[2024-05-26T13:29:22.524690135Z INFO  solana_metrics::metrics] datapoint: verify_snapshot_bank clean_us=4i shrink_us=4i verify_accounts_us=67i verify_bank_us=2458i
[2024-05-26T13:29:22.524697886Z INFO  solana_metrics::metrics] datapoint: bank_from_snapshot_archives untar_full_snapshot_archive_us=131447533i untar_incremental_snapshot_archive_us=0i rebuild_bank_us=57060817i verify_bank_us=2549i
[2024-05-26T13:29:22.718163461Z INFO  solana_metrics::metrics] datapoint: accounts_db_active hash=1i
[2024-05-26T13:29:22.718852829Z INFO  solana_metrics::metrics] datapoint: accounts_db_active hash_scan=1i
[2024-05-26T13:29:22.943833410Z INFO  solana_ledger_tool::ledger_utils] Using: block-verification-method: blockstore-processor
[2024-05-26T13:29:22.943857562Z INFO  solana_ledger_tool::ledger_utils] no scheduler pool is installed for block verification...
[2024-05-26T13:29:22.943979422Z INFO  solana_ledger::blockstore_processor] Processing ledger from slot 257197855...
[2024-05-26T13:29:22.943986172Z INFO  solana_ledger::blockstore_processor] Start slot 257197855 isn't a root, and won't be updated due to secondary blockstore access
[2024-05-26T13:29:22.944025823Z WARN  solana_ledger::blockstore_processor] Starting slot 257197855 is not in Blockstore, unable to process
[2024-05-26T13:29:22.944048372Z INFO  solana_ledger::blockstore_processor] ledger processing timing: ExecuteTimings { metrics: [0, 0, 0, 0, 0, 0, 0, 0, 0], details: ExecuteDetailsTimings { serialize_us: 0, create_vm_us: 0, execute_us: 0, deserialize_us: 0, get_or_create_executor_us: 0, changed_account_count: 0, total_account_count: 0, create_executor_register_syscalls_us: 0, create_executor_load_elf_us: 0, create_executor_verify_code_us: 0, create_executor_jit_compile_us: 0, per_program_timings: {} }, execute_accessories: ExecuteAccessoryTimings { feature_set_clone_us: 0, compute_budget_process_transaction_us: 0, get_executors_us: 0, process_message_us: 0, update_executors_us: 0, process_instructions: ExecuteProcessInstructionTimings { total_us: 0, verify_caller_us: 0, process_executable_chain_us: 0, verify_callee_us: 0 } } }
[2024-05-26T13:29:22.944071770Z INFO  solana_metrics::metrics] datapoint: process_blockstore_from_root total_time_us=43i frozen_banks=1i slot=257197855i num_slots_processed=0i num_new_roots_found=0i forks=1i
[2024-05-26T13:29:22.944091897Z INFO  solana_ledger::blockstore_processor] ledger processed in 43 µs and 987 ns. root slot is 257197855, 1 bank: 257197855
[2024-05-26T13:29:22.944182722Z INFO  solana_core::accounts_hash_verifier] AccountsHashVerifier has started
[2024-05-26T13:29:22.944204190Z INFO  solana_core::accounts_hash_verifier] AccountsHashVerifier has stopped
[2024-05-26T13:29:22.944279436Z INFO  solana_runtime::accounts_background_service] AccountsBackgroundService has started
[2024-05-26T13:29:22.944296843Z INFO  solana_runtime::accounts_background_service] AccountsBackgroundService has stopped
[2024-05-26T13:29:22.993828170Z INFO  solana_ledger_tool] Scanning individual accounts: [NeonVMyRX5GbCrsAHnUwx1nYYoJAtskU1bWUo6JGNyG]
NeonVMyRX5GbCrsAHnUwx1nYYoJAtskU1bWUo6JGNyG:
  balance: 0.00114144 SOL
  owner: 'BPFLoaderUpgradeab1e11111111111111111111111'
  executable: true
  slot: 257049359
  rent_epoch: 18446744073709551615
  data_len: 36
  data: 'AgAAAOPgkMZ8zv0w9/sMh7kcTLD98j3qSVUdCfWwH/oDWPYJ'
  encoding: "base64"

TotalAccountsStats {
    num_accounts: 1,
    data_len: 36,
    num_executable_accounts: 1,
    executable_data_len: 36,
    num_rent_exempt_accounts: 1,
    num_rent_paying_accounts: 0,
    num_rent_paying_accounts_without_data: 0,
    lamports_in_rent_paying_accounts: 0,
}
[2024-05-26T13:29:22.993929020Z INFO  solana_ledger_tool] accounts scan took 69us
[2024-05-26T13:29:22.993970016Z INFO  solana_ledger_tool] ledger tool took 189.1s
```
