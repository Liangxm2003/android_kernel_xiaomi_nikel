# CVE porting list — nikel 3.18.22

Base: vanilla `3.18.22` + MTK/Redmi Note 4 patches (`3d4e6944`).
Generated: 2026-11-21. Sources: `nluedtke/linux_kernel_cves` stream `3.18`
(complete through ~2024), cross-checked against `nikel_defconfig`.

## What this is

- **365** CVEs were fixed in stable `3.18.23` … `3.18.140` after this kernel's base.
- **157** of those touch code that is actually built in this kernel (after config filtering).
- **208** are not applicable because the subsystem is disabled (BT, SCTP/DCCP/RDS,
  KEYS, KVM, Xen, mac80211, media/DRM, most SCSI low-level drivers, F2FS/Btrfs/XFS/NFS, …).
- **148** post-EOL fixes were selected from the 1,161 “outstanding” CVEs. This is a
  curated subset, not the whole outstanding set; the rest is either config-disabled,
  too old/irrelevant, or needs a per-CVE applicability check.

The full 365-row inventory with commit hashes is in `cve_port_list.csv`.

## Tiers

| Tier | Meaning |
|---|---|
| **P0** | Reachable from the network or a plugged-in USB device without local privileges. |
| **P1** | Local privilege escalation / UAF / data exposure, or a device node reachable by an app or system daemon. |
| **P2** | DoS, infoleak, hardening, or a bug whose trigger needs root/CAP_NET_ADMIN anyway. |

> Android context: `CONFIG_USER_NS` is **not set**, so an untrusted app cannot get
> `CAP_NET_RAW`/`CAP_NET_ADMIN` through a user+net namespace. AF_PACKET, x_tables and
> xfrm bugs are therefore P1 (reachable from system daemons that already hold those
> capabilities), not remote P0.

## Stable fixes that apply (3.18.23 – 3.18.140)

### P0 — remote / malicious USB device (21)
| CVE | Fixed in | Subject | Upstream commit |
|---|---|---|---|
| CVE-2016-3841 | 3.18.25 | ipv6: add complete rcu protection around np->opt | `46ddb98e2018` |
| CVE-2015-8816 | 3.18.27 | USB: fix invalid memory access in hub_activate() | `425b3d713f07` |
| CVE-2016-2384 | 3.18.28 | ALSA: usb-audio: avoid freeing umidi object twice | `b4dc014d417d` |
| CVE-2016-2184 | 3.18.31 | ALSA: usb-audio: Fix NULL dereference in create_fixed_stream_quirk() | `53b7c0ed67fe` |
| CVE-2016-5696 | 3.18.41 | tcp: make challenge acks less predictable | `0efba8d124de` |
| CVE-2016-6828 | 3.18.46 | tcp: fix use after free in tcp_xmit_retransmit_queue() | `fa9b0c17c956` |
| CVE-2017-5970 | 3.18.49 | ipv4: keep skb->dst around in presence of IP options | `eeae5e030fcd` |
| CVE-2017-9074 | 3.18.56 | ipv6: Prevent overrun when parsing v6 header options | `5ca68dbb5a6b` |
| CVE-2017-9242 | 3.18.56 | ipv6: fix out of bound writes in __ip6_append_data() | `1d31de23f617` |
| CVE-2017-7542 | 3.18.64 | ipv6: avoid overflow of offset in ip6_find_1stfragopt | `41d33a5b803b` |
| CVE-2017-1000 | 3.18.65 | udp: consistently apply ufo or fragmentation | `4ac8dc208caf` |
| CVE-2017-1000112 | 3.18.65 | udp: consistently apply ufo or fragmentation | `4ac8dc208caf` |
| CVE-2017-14106 | 3.18.72 | tcp: initialize rcv_mss to TCP_MIN_MSS instead of 0 | `1722ca90e1b8` |
| CVE-2017-16529 | 3.18.75 | ALSA: usb-audio: Check out-of-bounds access by corrupted buffer descriptor | `ee44ff5445ae` |
| CVE-2017-16531 | 3.18.75 | USB: fix out-of-bounds in usb_set_configuration | `0502bf54bd01` |
| CVE-2017-16527 | 3.18.76 | ALSA: usb-audio: Kill stray URB at exiting | `9d263dba2528` |
| CVE-2017-16533 | 3.18.76 | HID: usbhid: fix out-of-bounds bug | `f4cf5d75416a` |
| CVE-2017-16535 | 3.18.78 | USB: core: fix out-of-bounds access bug in usb_get_bos_descriptor() | `7c1c88e160ed` |
| CVE-2017-17558 | 3.18.89 | USB: core: prevent malicious bNumInterfaces overflow | `dd3ad5f60d52` |
| CVE-2018-20169 | 3.18.129 | USB: check usb_get_extra_descriptor for proper size | `d672c306e00a` |
| CVE-2019-15927 | 3.18.132 | ALSA: usb-audio: Avoid access before bLength check in build_audio_procunit() | `172236e69b71` |

### P1 — local privilege escalation and daemon-reachable (73)
#### Networking
| CVE | Fixed in | Subject | Upstream commit |
|---|---|---|---|
| CVE-2013-7446 | 3.18.26 | unix: avoid use-after-free in ep_remove_wait_queue | `72032798034d` |
| CVE-2015-8543 | 3.18.26 | net: add validation for the socket syscall protocol argument | `e60ccfd9e596` |
| CVE-2013-4312 | 3.18.27 | unix: properly account for FDs passed over unix sockets | `a5b9e44af8d3` |
| CVE-2016-7117 | 3.18.37 | net: Fix use after free in the recvmmsg exit path | `cdd1fd36f4b6` |
| CVE-2016-10229 | 3.18.45 | udp: properly support MSG_PEEK with truncated buffers | `69335972b1c1` |
| CVE-2016-8655 | 3.18.46 | packet: fix race condition in packet_set_ring | `5c120b79dd66` |
| CVE-2017-6214 | 3.18.49 | tcp: avoid infinite loop in tcp_splice_read() | `337bb5f3fd50` |
| CVE-2017-7184 | 3.18.49 | xfrm_user: validate XFRM_MSG_NEWAE XFRMA_REPLAY_ESN_VAL replay_window | `281edc5a3778` |
| CVE-2017-7308 | 3.18.49 | net/packet: fix overflow in check for priv area size | `eeead20a3d20` |
| CVE-2015-3288 | 3.18.52 | mm: avoid setting up anonymous pages into file mapping | `47ce8d2e1fca` |
| CVE-2015-9004 | 3.18.52 | perf: Tighten (and fix) the grouping condition | `5a7b3b1ec16b` |
| CVE-2017-1000111 | 3.18.65 | packet: fix tp_reserve race in packet_set_ring | `f2ce502f8665` |
| CVE-2017-11600 | 3.18.70 | xfrm: policy: check policy direction value | `0d400015a32c` |
| CVE-2017-15649 | 3.18.77 | packet: in packet_do_bind, test fanout with bind_lock held | `b0763909b453` |
| CVE-2018-9568 | 3.18.77 | net: Set sk_prot_creator when cloning sockets to the right proto | `bc8a5a45208d` |
| CVE-2019-15916 | 3.18.137 | net-sysfs: Fix mem leak in netdev_register_kobject | `a7053bc3aeef` |
| CVE-2019-9213 | 3.18.137 | mm: enforce min addr even if capable() in expand_downwards() | `f290a73f3e91` |
| CVE-2019-10639 | 3.18.139 | netns: provide pure entropy for net_hash_mix() | `c2bca92ba948` |

#### Memory, scheduler, IPC, perf, core
| CVE | Fixed in | Subject | Upstream commit |
|---|---|---|---|
| CVE-2015-7613 | 3.18.23 | Initialize msg/shm IPC objects before doing ipc_addid() | `b5495ddce465` |
| CVE-2016-2847 | 3.18.37 | pipe: limit the per-user amount of pages allocated in pipes | `be65d29ff7b6` |
| CVE-2016-4794 | 3.18.37 | percpu: fix synchronization between chunk->map_extend_work and chunk destruction | `9e9f68827d7e` |
| CVE-2016-7911 | 3.18.38 | block: fix use-after-free in sys_ioprio_get() | `b86ef7ef2355` |
| CVE-2016-7910 | 3.18.40 | block: fix use-after-free in seq file | `8bc7adafc3a5` |
| CVE-2016-5195 | 3.18.44 | mm: remove gup_flags FOLL_WRITE games from __get_user_pages() | `e45a502bdeae` |
| CVE-2017-2618 | 3.18.49 | selinux: fix off-by-one in setprocattr | `0f436bf3f81b` |
| CVE-2017-7616 | 3.18.50 | mm/mempolicy.c: fix error handling in set_mempolicy and mbind. | `c01cf9586df9` |
| CVE-2016-7097 | 3.18.52 | posix_acl: Clear SGID bit when setting file permissions | `d8333c045f04` |
| CVE-2017-10661 | 3.18.52 | timerfd: Protect the might cancel mechanism proper | `4636aad51829` |
| CVE-2015-8963 | 3.18.54 | perf: Fix race in swevent hash | `6ee649ba055c` |
| CVE-2016-6786 | 3.18.54 | perf: Fix event->ctx locking | `33b738f7c5a7` |
| CVE-2016-6787 | 3.18.54 | perf: Fix event->ctx locking | `33b738f7c5a7` |
| CVE-2017-1000364 | 3.18.58 | mm: larger stack guard gap, between vmas | `d4712eb79b17` |
| CVE-2017-1000379 | 3.18.58 | mm: larger stack guard gap, between vmas | `d4712eb79b17` |
| CVE-2017-1000365 | 3.18.59 | fs/exec.c: account for argv/envp pointers | `2dff2164d171` |
| CVE-2018-14634 | 3.18.62 | exec: Limit arg stack to at most 75% of _STK_LIM | `915d91836939` |
| CVE-2019-9457 | 3.18.62 | exec: Limit arg stack to at most 75% of _STK_LIM | `915d91836939` |
| CVE-2018-10675 | 3.18.67 | mm/mempolicy: fix use after free when calling get_mempolicy | `d5a76b2ba687` |
| CVE-2017-9725 | 3.18.69 | mm: cma: fix incorrect type conversion for size during dma allocation | `f0c8d9367c92` |
| CVE-2017-12190 | 3.18.76 | fix unbalanced page refcounting in bio_map_user_iov | `d7795bf6a9d8` |
| CVE-2015-9016 | 3.18.80 | blk-mq: fix race between timeout and freeing request | `b6885d31d1c6` |
| CVE-2017-18203 | 3.18.85 | dm: fix race between dm_get_from_kobject() and __dm_destroy() | `84cc7b5d26c1` |
| CVE-2017-1000405 | 3.18.86 | mm, thp: Do not make page table dirty unconditionally in touch_p[mu]d() | `8b37803c5fc0` |
| CVE-2017-18208 | 3.18.86 | mm/madvise.c: fix madvise() infinite loop under special circumstances | `d5ec57c35ac4` |
| CVE-2018-18386 | 3.18.91 | n_tty: fix EXTPROC vs ICANON interaction with TIOCINQ (aka FIONREAD) | `95a9e2bf54b8` |
| CVE-2017-13216 | 3.18.92 | staging: android: ashmem: fix a race condition in ASHMEM_SET_SIZE ioctl | `6dc42f889217` |
| CVE-2017-6001 | 3.18.92 | perf/core: Fix concurrent sys_perf_event_open() vs. 'move_group' race | `2f9cf5cd5580` |
| CVE-2018-6927 | 3.18.93 | futex: Prevent overflow by strengthen input validation | `ad211e59c683` |
| CVE-2018-5344 | 3.18.94 | loop: fix concurrent lo_open/lo_release | `524a6efbd123` |
| CVE-2017-18344 | 3.18.95 | posix-timer: Properly check sigevent->sigev_notify | `28ef9653c185` |
| CVE-2018-1000199 | 3.18.103 | perf/hwbp: Simplify the perf-hwbp code, fix documentation | `8f7db030a639` |
| CVE-2018-9422 | 3.18.105 | futex: Remove requirement for lock_page() in get_futex_key() | `714f4e552318` |
| CVE-2018-17182 | 3.18.123 | mm: get rid of vmacache_flush_all() entirely | `2b8f74c8f0a4` |
| CVE-2018-10883 | 3.18.124 | jbd2: don't mark block as modified if the handle is out of credits | `524100251d15` |
| CVE-2018-13053 | 3.18.124 | alarmtimer: Prevent overflow for relative nanosleep | `f497869a6fea` |
| CVE-2018-17972 | 3.18.124 | proc: restrict kernel stack dumps to root | `ea20fab573d9` |
| CVE-2018-16862 | 3.18.129 | mm: cleancache: fix corruption on missed inode invalidation | `599379965a37` |
| CVE-2018-12896 | 3.18.131 | posix-timers: Sanitize overrun handling | `2f5f58a8b5d8` |
| CVE-2019-20054 | 3.18.138 | fs/proc/proc_sysctl.c: fix NULL pointer dereference in put_links | `beb70e5c511c` |
| CVE-2017-18551 | 3.18.139 | i2c: core-smbus: prevent stack corruption on read I2C_BLOCK_DATA | `e045c806436d` |
| CVE-2019-11190 | 3.18.139 | binfmt_elf: switch to new creds when switching to new mm | `119b8e38491b` |
| CVE-2019-9454 | 3.18.139 | i2c: core-smbus: prevent stack corruption on read I2C_BLOCK_DATA | `e045c806436d` |

#### Android, TTY, block, drivers
| CVE | Fixed in | Subject | Upstream commit |
|---|---|---|---|
| CVE-2015-2925 | 3.18.23 | dcache: Handle escaped paths in prepend_path | `27f5c615afb5` |
| CVE-2016-0723 | 3.18.27 | tty: Fix unsafe ldisc reference via ioctl(TIOCGETD) | `16c10d77d37d` |
| CVE-2017-7495 | 3.18.40 | ext4: fix data exposure after a crash | `aba6b2d882d2` |
| CVE-2016-9120 | 3.18.51 | staging/android/ion : fix a race condition in the ion driver | `f63514257efd` |
| CVE-2015-8964 | 3.18.55 | tty: Prevent ldisc drivers from re-using stale tty fields | `f98d89173f9e` |
| CVE-2017-12146 | 3.18.61 | driver core: platform: fix race condition with driver_override | `61b0972bd40a` |
| CVE-2017-14140 | 3.18.67 | Sanitize 'move_pages()' permission checks | `8cf95002f0c9` |
| CVE-2018-7566 | 3.18.96 | ALSA: seq: Fix racy pool initializations | `667768364865` |
| CVE-2018-13405 | 3.18.116 | Fix up non-directory creation in SGID directories | `c5f2c5be9d17` |
| CVE-2018-18281 | 3.18.125 | mremap: properly flush TLB before releasing the page | `0f1490a75739` |
| CVE-2019-12819 | 3.18.137 | mdio_bus: Fix use-after-free on device_register fails | `077a353641d2` |
| CVE-2019-15214 | 3.18.139 | ALSA: core: Fix card races between register and disconnect | `8ad4179e47f7` |

### P2 — DoS / infoleak / hardening (63)
| CVE | Fixed in | Subject | Upstream commit |
|---|---|---|---|
| CVE-2015-0275 | 3.18.25 | ext4: allocate entire range in zero range | `b9fca5cb9ab9` |
| CVE-2015-8215 | 3.18.25 | ipv6: addrconf: validate new MTU before applying it | `a9ff3cb5ec5a` |
| CVE-2016-2545 | 3.18.27 | ALSA: timer: Fix double unlink of active_list | `73c0532426ba` |
| CVE-2016-2547 | 3.18.27 | ALSA: timer: Harden slave timer list handling | `f40ee9cf5f69` |
| CVE-2016-2548 | 3.18.27 | ALSA: timer: Harden slave timer list handling | `f40ee9cf5f69` |
| CVE-2017-13167 | 3.18.28 | ALSA: timer: Fix race at concurrent reads | `0f97e402030c` |
| CVE-2015-8785 | 3.18.31 | fuse: break infinite loop in fuse_fill_write_pages() | `d48d21de5e80` |
| CVE-2016-0821 | 3.18.31 | include/linux/poison.h: fix LIST_POISON{1,2} offset | `88965e61d381` |
| CVE-2016-4581 | 3.18.34 | propogate_mnt: Handle the first propogated copy being a slave | `b688848a01ce` |
| CVE-2016-4913 | 3.18.34 | get_rock_ridge_filename(): handle malformed NM entries | `b8500fc5acce` |
| CVE-2016-9754 | 3.18.35 | ring-buffer: Prevent overflow of size in ring_buffer_resize() | `180fbec3621c` |
| CVE-2016-1583 | 3.18.36 | proc: prevent stacking filesystems on top | `70e21269e3ec` |
| CVE-2016-3134 | 3.18.37 | netfilter: x_tables: fix unconditional helper | `c2a1b8ee3f6a` |
| CVE-2016-3156 | 3.18.37 | ipv4: Don't do expensive useless work during inetdev destroy. | `abe3994caa1c` |
| CVE-2016-4486 | 3.18.37 | net: fix infoleak in rtnetlink | `69243164a954` |
| CVE-2016-4805 | 3.18.37 | ppp: take reference on channels netns | `a472ae852324` |
| CVE-2016-7916 | 3.18.37 | proc: prevent accessing /proc/<PID>/environ until it's ready | `aa9be0d64b75` |
| CVE-2016-9806 | 3.18.37 | netlink: Fix dump skb leak/double free | `57b26930c714` |
| CVE-2016-1237 | 3.18.40 | posix_acl: Add set_posix_acl | `6abbd53f83fe` |
| CVE-2016-6136 | 3.18.40 | audit: fix a double fetch in audit_log_single_execve_arg() | `3f4976f0e610` |
| CVE-2016-8666 | 3.18.47 | tunnels: Don't apply GRO to multiple layers of encapsulation. | `dd4fff23f0f4` |
| CVE-2016-8405 | 3.18.49 | fbdev: color map copying bounds checking | `59ab6ac4aa85` |
| CVE-2017-7618 | 3.18.50 | crypto: ahash - Fix EINPROGRESS notification callback | `c2798145e731` |
| CVE-2017-2671 | 3.18.51 | ping: implement proper locking | `4e340a02d59c` |
| CVE-2014-9940 | 3.18.52 | regulator: core: Fix regualtor_ena_gpio_free not to access pin after freeing | `42f268023df2` |
| CVE-2016-9793 | 3.18.52 | net: avoid signed overflows for SO_{SND|RCV}BUFFORCE | `29fc5d17f274` |
| CVE-2016-9794 | 3.18.52 | ALSA: pcm : Call kill_fasync() in stream lock | `e6ef3b68c000` |
| CVE-2015-8955 | 3.18.54 | arm64: perf: reject groups spanning multiple HW PMUs | `e00f6efe21bd` |
| CVE-2015-8967 | 3.18.54 | arm64: make sys_call_table const | `c8f417a2d6af` |
| CVE-2016-2544 | 3.18.54 | ALSA: seq: Fix race at timer setup and close | `6d4025862a08` |
| CVE-2016-2546 | 3.18.54 | ALSA: timer: Fix race among timer ioctls | `19f609798f7e` |
| CVE-2016-4569 | 3.18.54 | ALSA: timer: Fix leak in SNDRV_TIMER_IOCTL_PARAMS | `bbcf19d30674` |
| CVE-2016-4578 | 3.18.54 | ALSA: timer: Fix leak in events via snd_timer_user_ccallback | `bbb79774fcea` |
| CVE-2016-7915 | 3.18.54 | HID: core: prevent out-of-bound readings | `18377401142c` |
| CVE-2017-1000380 | 3.18.57 | ALSA: timer: Fix race between read and ioctl | `d96c363ff004` |
| CVE-2017-18017 | 3.18.60 | netfilter: xt_TCPMSS: add more sanity tests on tcph->doff | `f4549a698a53` |
| CVE-2017-7533 | 3.18.64 | dentry name snapshots | `a1dbc0979b4f` |
| CVE-2017-12153 | 3.18.73 | nl80211: check for the required netlink attributes presence | `65e3664fc608` |
| CVE-2016-8650 | 3.18.74 | mpi: Fix NULL ptr dereference in mpi_powm() | `703937f005ea` |
| CVE-2016-10208 | 3.18.75 | ext4: validate s_first_meta_bg at mount time | `2ce649fdacd5` |
| CVE-2016-2543 | 3.18.76 | ALSA: seq: Fix missing NULL check at remove_events ioctl | `954e2ed41fec` |
| CVE-2017-15265 | 3.18.76 | ALSA: seq: Fix use-after-free at creating a port | `035e6d0b5b19` |
| CVE-2018-7191 | 3.18.82 | tun: call dev_get_valid_name() before register_netdevice() | `638c8339e05e` |
| CVE-2017-16939 | 3.18.86 | ipsec: Fix aborted xfrm policy dump crash | `8586e1841344` |
| CVE-2017-0861 | 3.18.88 | ALSA: pcm: prevent UAF in snd_pcm_info | `14416b2c878b` |
| CVE-2017-17449 | 3.18.91 | netlink: Add netns check on taps | `5594e3eba3ee` |
| CVE-2017-18595 | 3.18.91 | tracing: Fix possible double free on failure of allocating trace buffer | `f9e16c238bd6` |
| CVE-2018-1000004 | 3.18.94 | ALSA: seq: Make ioctls race-free | `6aab3aa52366` |
| CVE-2018-1092 | 3.18.106 | ext4: fail ext4_iget for root directory if unallocated | `bf1b17715e91` |
| CVE-2018-1093 | 3.18.108 | ext4: add validity checks for bitmap block numbers | `0643dbdb5c69` |
| CVE-2017-18255 | 3.18.109 | perf/core: Fix the perf_cpu_time_max_percent check | `2e9cbae49c5d` |
| CVE-2019-18675 | 3.18.113 | mmap: introduce sane default mmap limits | `bf3ff108832e` |
| CVE-2018-10879 | 3.18.115 | ext4: make sure bitmaps and the inode table don't overlap with bg descriptors | `780f50a6dcd2` |
| CVE-2018-10881 | 3.18.115 | ext4: clear i_data in ext4_inode_info when removing inline data | `15b85a060ace` |
| CVE-2018-10902 | 3.18.117 | ALSA: rawmidi: Change resized buffers atomically | `bfa30d8adcee` |
| CVE-2018-10876 | 3.18.124 | ext4: only look at the bg_flags field if it is valid | `8d419749b274` |
| CVE-2018-10877 | 3.18.124 | ext4: verify the depth of extent tree in ext4_find_extent() | `683626073ac0` |
| CVE-2018-10878 | 3.18.124 | ext4: always check block group bounds in ext4_init_block_bitmap() | `6f6da33decb7` |
| CVE-2018-10880 | 3.18.124 | ext4: never move the system.data xattr out of the inode body | `fd2634366fca` |
| CVE-2018-10882 | 3.18.124 | ext4: add more inode number paranoia checks | `292dc9673821` |
| CVE-2018-5953 | 3.18.131 | printk: hash addresses printed with %p | `fa3ec41ea511` |
| CVE-2019-6133 | 3.18.132 | fork: record start_time late | `0b2758fb10d9` |
| CVE-2020-0066 | 3.18.137 | netlink: Trim skb to alloc size to avoid MSG_TRUNC | `5821948648ca` |

## Post-EOL fixes selected for backport (not in any 3.18.y)

These are fixes that landed after 3.18 EOL (`3.18.140`, 2019) or were never
backported to 3.18. The commit hashes are not in the 3.18 dataset; locate them by
subject in the mainline tree (`git log --grep`) and backport. Android-specific
IDs are marked where two IDs share one fix.

| CVE | Area | Fix subject | Pri | Note |
|---|---|---|---|---|
| CVE-2019-2215 | binder | remove waitqueue when thread exits (UAF) | P1 | critical; old 3.18 binder is affected |
| CVE-2020-0030 | binder | synchronize_rcu() when using POLLFREE | P1 | epoll POLLFREE UAF |
| CVE-2019-2025 | binder | race allows malicious free of live buffer | P1 |  |
| CVE-2019-2213 | binder | possible UAF when freeing buffer | P1 |  |
| CVE-2019-2181 | binder | overflow when allocating security context | P1 |  |
| CVE-2019-1999 | binder | race between munmap() and direct reclaim | P1 |  |
| CVE-2018-9465 | binder | proc->files use-after-free | P1 |  |
| CVE-2021-39686 | binder | use euid from cred instead of task | P1 |  |
| CVE-2023-52609 | binder | race between mmput() and do_exit() | P1 |  |
| CVE-2024-26606 | binder | signal epoll threads of self-work | P1 |  |
| CVE-2018-20510 | binder | replace "%p" with "%pK" | P2 | infoleak hardening |
| CVE-2018-20509 | binder | refactor binder ref inc/dec for thread safety | P2 | large prerequisite refactor |
| CVE-2020-0009 | ashmem | disallow ashmem memory from being remapped | P1 |  |
| CVE-2021-0929 | ion | delete dma_buf->kmap/unmap implementation | P2 |  |
| CVE-2021-39714 | ion | drop ion_map_kernel interface | P2 |  |
| CVE-2019-11477 | net/tcp | limit payload size of sacked skbs | P0 | remote panic (SACK) |
| CVE-2019-11478 | net/tcp | tcp_fragment() sane memory limits | P0 | remote DoS |
| CVE-2019-11479 | net/tcp | add tcp_min_snd_mss sysctl | P0 | remote DoS |
| CVE-2018-5391 | net/ipv4 | discard IPv4 datagrams with overlapping segments | P0 | remote DoS |
| CVE-2016-8645 | net/tcp | take care of truncations done by sk_filter() | P0 |  |
| CVE-2017-5972 | net/tcp | do not lock listener to process SYN packets | P0 | remote DoS |
| CVE-2023-1206 | net/tcp | reduce chance of collisions in inet6_hashfn() | P0 | remote DoS |
| CVE-2023-52527 | net/ip | fix transhdrlen in __ip{,6}_append_data() | P0 |  |
| CVE-2023-6932 | net/igmp | fix refcnt UAF on IGMP query | P0 |  |
| CVE-2024-0584 | net/igmp | fix refcnt UAF on IGMP query | P0 | duplicate of 6932 for older branches |
| CVE-2016-7917 | net/netfilter | correctly validate length of batch messages | P1 | nfnetlink, CONFIG_NETFILTER_NETLINK=y |
| CVE-2021-22555 | net/netfilter | x_tables compat match/target pad OOB write | P1 | critical; CONFIG_COMPAT=y |
| CVE-2021-0937 | net/netfilter | x_tables compat pad OOB write (Android id) | P1 | same fix as 22555 |
| CVE-2021-29650 | net/netfilter | x_tables correct memory barriers | P2 |  |
| CVE-2021-39636 | net/netfilter | x_tables pointer leaks to userspace | P2 |  |
| CVE-2020-25211 | net/netfilter | ctnetlink range check for l3/l4 protonum | P1 |  |
| CVE-2021-0448 | net/netfilter | ctnetlink range check (Android id) | P1 | same fix as 25211 |
| CVE-2023-7192 | net/netfilter | ctnetlink refcount leak | P2 |  |
| CVE-2023-39192 | net/netfilter | xt_u32 validate user space input | P1 | CONFIG_NETFILTER_XT_MATCH_U32=y |
| CVE-2023-39194 | net/xfrm | xfrm_address_filter OOB read | P2 |  |
| CVE-2023-3772 | net/xfrm | add NULL check in xfrm_update_ae_params | P2 |  |
| CVE-2023-3106 | net/xfrm | fix crash in XFRM_MSG_GETSA netlink handler | P2 |  |
| CVE-2019-15666 | net/xfrm | out-of-bound array accesses in __xfrm_policy_unlink | P2 |  |
| CVE-2022-36879 | net/xfrm | possible double xfrm_pols_put in xfrm_bundle_lookup | P2 |  |
| CVE-2020-27066 | net/xfrm | double free in xfrm_policy_timer | P2 |  |
| CVE-2020-36691 | net/netlink | limit recursion depth in policy validation | P2 |  |
| CVE-2022-20158 | net/packet | slab-out-of-bounds in packet_recvmsg() | P1 |  |
| CVE-2022-20368 | net/packet | slab-out-of-bounds in packet_recvmsg() | P1 | Android id, same fix |
| CVE-2020-14386 | net/packet | tpacket_rcv ring overflow | P1 | verify 3.18 code has the vulnerable path |
| CVE-2017-6346 | net/packet | fix races in fanout_add() | P1 |  |
| CVE-2019-20812 | net/packet | set default value for tmo | P2 |  |
| CVE-2021-0920 | net/unix | fix garbage collect vs MSG_PEEK | P1 | critical; Android exploited in the wild |
| CVE-2021-4083 | net/unix | fget: check fd still exists after getting a ref | P1 | critical UAF |
| CVE-2021-4203 | net/unix | fix races in sk_peer_pid/sk_peer_cred accesses | P1 |  |
| CVE-2021-20265 | net/unix | fix struct pid memory leak | P2 |  |
| CVE-2020-36516 | net/ipv4 | avoid shared IP generator for connected sockets | P2 |  |
| CVE-2022-32296 | net/tcp | increase source port perturb table to 2^16 | P2 |  |
| CVE-2022-3566 | net/tcp | fix data races around icsk->icsk_af_ops | P1 |  |
| CVE-2022-3567 | net/ipv6 | fix data races around sk->sk_prot | P1 |  |
| CVE-2021-45485 | net/ipv6 | use prandom_u32() for ID generation | P2 |  |
| CVE-2023-0394 | net/ipv6 | deduct extension header length in rawv6_push_pending_frames | P1 |  |
| CVE-2023-52340 | net/ipv6 | remove max_size check inline with ipv4 | P1 |  |
| CVE-2023-42752 | net/igmp | limit igmpv3_newpack() packet size to IP_MAX_MTU | P1 |  |
| CVE-2017-18509 | net/ipv6 | check sk type/protocol early in ip_mroute_set/getsockopt | P1 | CONFIG_IPV6_MROUTE=y |
| CVE-2022-3524 | net/ipv6 | fix memory leak in ipv6_renew_options() | P1 |  |
| CVE-2024-26733 | net/arp | prevent overflow in arp_req_get() | P1 |  |
| CVE-2024-26633 | net/ipv6 | fix NEXTHDR_FRAGMENT handling in ip6_tnl_parse_tlv_enc_lim | P1 | CONFIG_IPV6_TUNNEL=y |
| CVE-2021-46936 | net | fix use-after-free in tw_timer_handler | P2 |  |
| CVE-2021-47103 | net | fully convert sk->sk_rx_dst to RCU rules | P1 |  |
| CVE-2024-26679 | net | read sk->sk_family once in inet_recv_error | P2 |  |
| CVE-2024-26675 | net/ppp | ppp_async: limit MRU to 64K | P1 | CONFIG_PPP_ASYNC=y |
| CVE-2021-33909 | fs/seq_file | disallow extremely large seq buffer allocations | P1 | critical; stack buffer overflow |
| CVE-2019-11487 | mm/pipe | prevent page refcount overflow in pipe_buf_get | P1 |  |
| CVE-2019-11599 | fs/coredump | mmget_not_zero/get_task_mm vs core dumping race | P1 |  |
| CVE-2020-10732 | fs/binfmt_elf | allocate initialized memory in fill_thread_core_info | P1 |  |
| CVE-2020-14381 | kernel/futex | fix inode life-time issue | P1 |  |
| CVE-2020-35508 | kernel/fork | fix copy_process(CLONE_PARENT) race | P1 |  |
| CVE-2021-4002 | mm/hugetlb | flush TLBs correctly after huge_pmd_unshare | P1 |  |
| CVE-2022-41222 | mm/mremap | hold rmap lock in write mode | P1 |  |
| CVE-2024-26759 | mm/swap | fix race when skipping swapcache | P1 |  |
| CVE-2024-26686 | fs/proc | do_task_stat: use sig->stats_lock | P1 |  |
| CVE-2019-5489 | mm/mincore | count mapped pages rather than cached pages | P2 | side channel |
| CVE-2020-12826 | kernel/signal | extend exec_id to 64 bits | P2 |  |
| CVE-2023-0458 | kernel/prlimit | speculation check in do_prlimit | P2 |  |
| CVE-2023-0459 | kernel/uaccess | speculation barrier in copy_from_user | P2 |  |
| CVE-2023-52489 | mm/sparsemem | race accessing memory_section->usage | P2 |  |
| CVE-2023-6915 | lib/ida | fix crash in ida_free when bitmap empty | P2 |  |
| CVE-2024-26720 | mm/writeback | divide-by-zero in wb_dirty_limits | P2 |  |
| CVE-2023-52522 | net/neigh | store tearing in neigh_periodic_work | P2 |  |
| CVE-2015-8839 | fs/ext4 | races between page faults and hole punching | P1 |  |
| CVE-2019-11833 | fs/ext4 | zero unused memory in extent tree block | P1 |  |
| CVE-2019-19319 | fs/ext4 | protect journal inode blocks with block_validity | P1 |  |
| CVE-2019-19447 | fs/ext4 | deleting a file with i_nlink == 0 | P1 |  |
| CVE-2020-14314 | fs/ext4 | negative array index in do_split() | P1 |  |
| CVE-2021-33631 | fs/ext4 | kernel BUG in ext4_write_inline_data_end() | P1 |  |
| CVE-2021-40490 | fs/ext4 | race writing inline_data while xattrs change | P1 |  |
| CVE-2022-0850 | fs/ext4 | infoleak via ext4_extent_header | P1 |  |
| CVE-2022-1184 | fs/ext4 | verify dir block before splitting it | P1 |  |
| CVE-2023-2513 | fs/ext4 | UAF in ext4_xattr_set_entry | P1 |  |
| CVE-2023-34256 | fs/ext4 | slab-out-of-bounds in ext4_group_desc_csum | P1 |  |
| CVE-2024-0775 | fs/ext4 | error recovery in __ext4_remount() | P1 |  |
| CVE-2024-26704 | fs/ext4 | double-free of blocks due to wrong extents moved_len | P1 |  |
| CVE-2024-26772 | fs/ext4 | avoid allocating from corrupted group (find_by_goal) | P1 |  |
| CVE-2024-26773 | fs/ext4 | avoid allocating from corrupted group (try_best_found) | P1 |  |
| CVE-2024-26774 | fs/ext4 | divide-by-zero in mb_update_avg_fragment_size | P1 |  |
| CVE-2023-52622 | fs/ext4 | online resizing failures due to oversized flex bg | P2 |  |
| CVE-2021-47116 | fs/ext4 | memory leak in ext4_mb_init_backend | P2 |  |
| CVE-2021-47117 | fs/ext4 | bug in ext4_es_cache_extent | P2 |  |
| CVE-2021-47119 | fs/ext4 | memory leak in ext4_fill_super | P2 |  |
| CVE-2020-36322 | fs/fuse | fix bad inode | P1 | CONFIG_FUSE_FS=y |
| CVE-2022-1011 | fs/fuse | fix pipe buffer lifetime for direct_io | P1 |  |
| CVE-2024-26764 | fs/aio | restrict kiocb_set_cancel_fn to libaio | P2 | CONFIG_AIO=y |
| CVE-2022-4662 | usb/core | prevent nested device-reset calls | P1 |  |
| CVE-2021-39685 | usb/gadget | detect too-big endpoint 0 requests | P1 | CONFIG_USB_GADGET=y |
| CVE-2020-13143 | usb/gadget | illegal array access binding with UDC | P1 |  |
| CVE-2022-25258 | usb/gadget | validate interface OS descriptor requests | P1 |  |
| CVE-2022-24958 | usb/gadget | don't release an existing dev->buf | P1 |  |
| CVE-2020-12464 | usb/core | free-while-in-use in USB S-G library | P1 |  |
| CVE-2019-19537 | usb/core | races in char device registration | P1 |  |
| CVE-2021-47170 | usb/core | don't WARN on excessively large usbfs allocations | P2 |  |
| CVE-2016-4482 | usb/core | usbfs infoleak in devio | P2 |  |
| CVE-2019-15117 | usb/alsa | OOB in parse_audio_mixer_unit | P0 | malicious USB audio device |
| CVE-2019-15118 | usb/alsa | stack overflow in check_input_term | P0 | malicious USB audio device |
| CVE-2019-15222 | usb/alsa | gpf in snd_usb_pipe_sanity_check | P0 | malicious USB audio device |
| CVE-2018-19824 | usb/alsa | UAF decrement if card has no live interfaces | P0 | malicious USB audio device |
| CVE-2023-1073 | hid | check empty report_list in hid_validate_values | P1 |  |
| CVE-2022-20565 | hid | correctly handle ReportSize being zero | P1 |  |
| CVE-2020-0465 | hid | sanitize event code and type when mapping input | P1 |  |
| CVE-2020-0431 | hid | clear unmapped usages | P1 |  |
| CVE-2021-0512 | hid | make arrays usage and value the same | P1 |  |
| CVE-2021-46906 | hid | usbhid infoleak in hid_submit_ctrl | P2 |  |
| CVE-2022-20132 | hid | add hid_is_usb() helper | P1 |  |
| CVE-2017-7273 | hid | hid-cypress validate report length | P1 | CONFIG_HID_CYPRESS=y |
| CVE-2019-19532 | hid | fix assumption that devices have inputs | P1 |  |
| CVE-2020-29660 | tty | fix ->session locking | P1 |  |
| CVE-2020-29661 | tty | fix ->pgrp locking in tiocspgrp | P1 |  |
| CVE-2022-1462 | tty/pty | tty_insert_flip_string_and_push_buffer in pty_write | P1 |  |
| CVE-2019-3901 | kernel/perf | perf_event_open() vs execve() race | P1 |  |
| CVE-2020-14351 | kernel/perf | race in perf_mmap_close() | P1 |  |
| CVE-2023-1077 | kernel/sched | pick_next_rt_entity check list_entry | P1 |  |
| CVE-2023-0590 | net/sched | race condition in qdisc_graft() | P1 | CONFIG_NET_SCHED=y |
| CVE-2023-3776 | net/sched | cls_fw improper refcount update UAF | P1 | CONFIG_NET_CLS_FW=y |
| CVE-2023-4128 | net/sched | cls_u32 no longer copy tcf_result | P1 | CONFIG_NET_CLS_U32=y |
| CVE-2023-4207 | net/sched | cls_fw no longer copy tcf_result | P1 |  |
| CVE-2023-4208 | net/sched | cls_u32 no longer copy tcf_result | P1 |  |
| CVE-2024-26739 | net/sched | act_mirred don't override retval | P2 | CONFIG_NET_ACT_MIRRED=y |
| CVE-2021-47082 | net/tun | avoid double free in tun_free_netdev | P1 | CONFIG_TUN=y |
| CVE-2022-4744 | net/tun | avoid double free in tun_free_netdev | P1 | same fix |
| CVE-2021-47083 | pinctrl/mediatek | fix global-out-of-bounds issue | P1 | CONFIG_PINCTRL_MT6797=y |
| CVE-2024-26659 | usb/xhci | handle isoc Babble and Buffer Overrun events | P1 | CONFIG_USB_XHCI_MTK=y |
| CVE-2024-26789 | crypto/arm64 | neonbs out-of-bounds on short input | P2 |  |
| CVE-2023-45863 | lib/kobject | slab-out-of-bounds in fill_kobj_path | P2 |  |
| CVE-2023-28772 | lib/seq_buf | overflow in seq_buf_putmem_hex | P2 |  |

## Not applicable — why 208 stable CVEs are excluded

| Count | Reason |
|---|---|
| 19 | CONFIG_KEYS=n |
| 16 | USB class driver not built |
| 12 | protocol/driver not built |
| 10 | arch/feature not built (arm64) |
| 9 | CONFIG_BT=n |
| 8 | CONFIG_MEDIA_SUPPORT=n |
| 7 | CONFIG_IP_SCTP=n |
| 7 | x86 KVM only |
| 6 | CONFIG_IP_DCCP=n |
| 5 | CONFIG_CHR_DEV_SG=n |
| 4 | CONFIG_RDS=n |
| 4 | CONFIG_INFINIBAND=n |
| 3 | SCSI low-level/bsg not built |
| 3 | CONFIG_DRM_VMWGFX=n |
| 3 | CONFIG_F2FS_FS=n |
| 3 | CONFIG_MAC80211/vendor wifi not built |
| 3 | CONFIG_SCSI_LOWLEVEL=n |
| 3 | CONFIG_BLK_DEV_SR/CDROM=n |
| 2 | CONFIG_ISDN=n |
| 2 | powerpc KVM only |
| 2 | filesystem not built |
| 2 | x86 Xen PV only |
| 2 | CONFIG_INPUT_IMS_PCU=n |
| 2 | CONFIG_XFS_FS=n |
| 2 | CONFIG_KEYS=n (assoc_array unused) |
| 2 | CONFIG_INPUT_GTCO=n |
| 2 | CONFIG_KVM=n |
| 2 | CONFIG_VFIO=n |
| 2 | CONFIG_SND_MSND=n |
| 2 | crypto user API off / unused |
| 2 | CONFIG_NFC=n |
| 2 | staging/irda removed, not built |
| 2 | CONFIG_ATALK=n |
| 2 | MEDIA/DRM not built |
| 1 | CONFIG_NFS_FS=n |
| 1 | CONFIG_VHOST_SCSI=n |
| 1 | CONFIG_VIRTIO_NET=n |
| 1 | CONFIG_PPTP=n |
| 1 | Xen only |
| 1 | CONFIG_INPUT_ATI_REMOTE2=n |
| 1 | CONFIG_INPUT_POWERMATE=n |
| 1 | CONFIG_ATL2=n |
| 1 | powerpc only |
| 1 | CONFIG_HIDDEV=n |
| 1 | CONFIG_SCSI_AACRAID=n |
| 1 | ARM OABI only (arm64) |
| 1 | x86-only get_user_ex (arm64) |
| 1 | CONFIG_FIREWIRE_NET=n |
| 1 | CONFIG_SCSI_ARCMSR=n |
| 1 | CONFIG_CHR_DEV_SG/BLK_DEV_BSG=n |
| 1 | CONFIG_IPV6_GRE=n |
| 1 | CONFIG_L2TP=n |
| 1 | CONFIG_NFSD=n |
| 1 | CONFIG_PRINTER=n |
| 1 | CONFIG_IPX=n |
| 1 | CONFIG_AF_RXRPC=n |
| 1 | CONFIG_POSIX_MQUEUE=n |
| 1 | x86 i8042 not built |
| 1 | CONFIG_SCSI_QLA2XXX=n |
| 1 | CONFIG_FB_ATY=n |
| 1 | CONFIG_ISCSI_TCP=n |
| 1 | CONFIG_USB_UAS=n |
| 1 | CONFIG_USB_SERIAL=n |
| 1 | CONFIG_OCFS2_FS=n |
| 1 | CONFIG_NETFILTER_NETLINK_CTHELPER=n |
| 1 | CONFIG_NETFILTER_XT_MATCH_OSF=n |
| 1 | CONFIG_BRIDGE_NF_EBTABLES=n |
| 1 | CONFIG_USB_MON=n |
| 1 | CONFIG_DRM_UDL=n |
| 1 | CONFIG_NCPFS=n |
| 1 | sbusfb not built |
| 1 | CONFIG_FB_UVESA=n |
| 1 | CONFIG_HID_DEBUG=n |
| 1 | CONFIG_JFS_FS=n |
| 1 | CONFIG_BTRFS_FS=n |
| 1 | CONFIG_ISCSI_TARGET=n |
| 1 | arm64 KVM (CONFIG_KVM=n) |
| 1 | CONFIG_BLK_DEV_FD=n |
| 1 | CONFIG_SUNRPC=n |
| 1 | CONFIG_CAN=n |
| 1 | CONFIG_HSR=n |
| 1 | CONFIG_NETLABEL=n |
| 1 | CONFIG_N_R3964=n |
| 1 | CONFIG_SCSI_MEGARAID_SAS=n |
| 1 | CONFIG_FSL_HYPERVISOR=n |

Also excluded: x86/KVM/Xen/PowerPC-only fixes, ARM OABI-only fixes, and drivers
whose config symbol is absent (USB serial, USB UAS, USB monitor, HID debug,
input powermate/ati_remote2/gtco/ims-pcu, SCSI low-level, media/DVB, DRM, …).

## Recommended porting order

1. **Batch 1 — Android + memory (highest value, smallest risk):**
   Dirty COW (`CVE-2016-5195`), `CVE-2016-7911`, `CVE-2016-7910`,
   `CVE-2017-10661`, `CVE-2018-6927`, `CVE-2018-9422`, `CVE-2017-18344`,
   `CVE-2018-17182`, `CVE-2018-18281`, `CVE-2019-9213`, `CVE-2017-13216`,
   `CVE-2016-9120`, `CVE-2020-0009`, `CVE-2021-33909`, `CVE-2021-4083`,
   `CVE-2021-0920`, `CVE-2021-22555`, and the binder set.
2. **Batch 2 — networking (remote/daemon reachable):**
   `CVE-2019-11477/78/79`, `CVE-2018-5391`, `CVE-2016-5696`, `CVE-2016-6828`,
   `CVE-2017-5970`, `CVE-2017-9074`, `CVE-2017-9242`, `CVE-2017-7542`,
   `CVE-2017-1000`, `CVE-2017-1000112`, `CVE-2017-14106`, `CVE-2018-9568`,
   `CVE-2022-20158/20368`, `CVE-2023-1206`, `CVE-2023-52527`, `CVE-2023-6932`,
   `CVE-2024-0584`.
3. **Batch 3 — USB / HID / ALSA / TTY:**
   USB core descriptor validation, gadget endpoint checks, `CVE-2019-15117/18`,
   `CVE-2019-15222`, HID report validation, `CVE-2015-8964`, `CVE-2016-0723`,
   `CVE-2020-29660/61`, `CVE-2022-1462`.
4. **Batch 4 — filesystems:** ext4 metadata validation, FUSE, isofs.
5. **Batch 5 — P2 hardening:** the remaining stable fixes and the low-risk
   post-EOL items (infoleaks, refcount leaks, speculation barriers).

The cleanest option is to port **all 157 applicable stable fixes first**
(batches 1–5), because they are already written against the 3.18 API and have
stable backports. The post-EOL set is a second pass and needs per-CVE adaptation.

## Caveats

- The dataset is complete through ~2024. 2025–2026 CVEs are not in this list;
  a supplementary check against `linuxkernelcves.com`/NVD is needed for those.
- “Applies = yes” here means **the subsystem is built**, not that the patch
  applies cleanly. Each patch still needs a code-level check and build test.
- No code has been changed yet. Nothing in this list has been ported or flashed.
