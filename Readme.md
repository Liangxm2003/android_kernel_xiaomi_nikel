# Custom kernel for Xiaomi Redmi Note 4 MTK (Nikel) by Ruben1863
# Kernel version 3.18.22

> **Status: maybe usable.**
> This is a work-in-progress hardware bring-up kernel. Some peripherals
> work, many are still untested or incomplete. Treat it as experimental.

> **AI contribution notice**
> Parts of this kernel — including the BMI160 accelerometer/gyroscope,
> AH1903 hall sensor, YAS537 magnetometer/compass support, the ACCDET
> headset configuration, the BQ24296 charger driver, the CW2015 fuel
> gauge, the FT5346 touch
> EINT/HPS tuning and several device-tree fixes — were developed with
> the help of an AI coding assistant. Everything was built and tested on
> real hardware before being committed, except the fingerprint (the test
> unit's sensor module is faulty; see below).

> **Tested on one device only**
> This kernel has only been tested on a single Redmi Note 4 (nikel,
> MT6797) unit. Different panel, touch, sensor or audio variants may
> behave differently. Keep a bootable backup before flashing, and do not
> assume it is safe for daily use on another device.

> **Fingerprint could NOT be verified on the author's unit**
> The Goodix fingerprint software stack is implemented, but the author's
> second-hand sensor module does not answer on SPI: its IRQ fires on
> touch, yet every chip-id read returns 0x0 and enrollment fails with
> "Add fingerprint failed". The official kernel behaves the same, so the
> fault is in the module. See "Fingerprint status" below.

=========================================================================
* Working:
	* LCM (For now just nt35532_fhd_boe_vdo_lcm)
	* Touch (FT5346)
	* Boot
	* MTP
	* ADB
	* Flashlight (dual-tone)
	* BMI160 accelerometer
	* AH1903 hall sensor (smart cover)
	* YAS537 magnetometer / compass
	* BMI160 gyroscope
	* Wired headset detection, audio routing, mic and buttons
	* Lock-screen numeric PIN (credential set and unlock verified)
	* Battery gauge and charging (BQ24296 charger on I2C-0, CW2015
	  gauge on I2C-1): charge status, current, voltage and a
	  stock-like percentage are reported and refreshed, and the battery
	  charges instead of the charger sitting disabled

=================================================
* Not working / untested:
	* Fingerprint sensor — this unit does not answer on SPI (chip id 0x0)
	  although its IRQ fires on touch; the same happens with the official
	  kernel, so the module is likely faulty (second-hand device)
	* Everything not listed above
	* Other panel, touch, sensor and audio variants
	* Daily-driver stability

* Lock screen note:
	* The PIN itself works (verified lock/unlock). MIUI Settings can
	  appear to hang while saving because it calls the fingerprint
	  `preEnroll()` afterwards; the credential is written before that
	  call.
	* Fingerprint: see the dedicated "Fingerprint status" section below.
	  The apparent Settings hang is only the fingerprint `preEnroll()`
	  call; the PIN is written before it.

=========================================================================
* Fingerprint status — implemented, but untestable on the author's unit:

	* The sensor is **Goodix on SPI0**, not FPC. The FPC TA probes SPI1,
	  finds nothing attached and returns -3/FPC_ERROR_COMM.

	* Software implemented:
		* Goodix SPI0 child + `mediatek,goodix-fp` platform node, with
		  **no SPI0 pinmux** (the controller uses pad-macro pads;
		  GPIO58 is the touch panel regulator enable).
		* `/dev/goodix_fp`, pad-macro prime, and the IRQ pin
		  (GPIO86/EINT9) re-muxed after every chip reset.
		* MIUI TEE daemon fix: `/system/bin/mcDriverDaemon` uses
		  `/data/misc/mcregistr`, the directory is created at
		  `post-fs-data`, and `mobicore` + `goodixfpd` are restarted
		  after boot. Without this the HAL fails with 0x427.
	  With that, `gf_hal_init` returns 0 and the enrollment screen
	  starts (`GF_CMD_ENROLL`).

	* Why it is untested: the author's module is faulty.
		* The IRQ line works: `goodix_fp_irq` increments when a finger
		  touches the sensor.
		* But every SPI read returns chip id **0x0**, the HAL image
		  command returns **1023**, and the 60 s enrollment times out
		  with "Add fingerprint failed".
		* The official/stock kernel fails at exactly the same point, so
		  the kernel is not the cause. On this second-hand unit the
		  fingerprint flex/SPI data path or the sensor die is faulty.

	* If you have a healthy module, verify it like this:
		1. `grep goodix /proc/interrupts` — the counter must increment
		   when you touch the sensor.
		2. `dmesg | grep gf_check_9p_chip` — a real chip version must
		   be printed, not 0x0.
		3. Settings → Lock screen & password → Fingerprints → Add.
	  If the SPI reads remain 0x0, reseat or replace the fingerprint
	  flex/module. The software side is ready for a working module.

=========================================================================
* Charging / battery status:

	* This board uses a BQ24296-family charger at I2C address 0x6b on
	  I2C-0 (the `sw_charger@6b` node), not a BQ25890. The stock MIUI
	  kernel uses the `bq24296` / `charging_hw_bq24296` drivers, so
	  this kernel builds `bq24296.c` for the chip.

	* The kernel-side driver uses plain `i2c_transfer()` reads and
	  writes. MTK's `(1<<8|1)` I2C-extension read is deliberately not
	  used because `CONFIG_MTK_I2C_EXTENSION` is disabled in this tree
	  (treating it as a 257-byte DMA from a one-byte buffer is unsafe).

	* Hardware layer: the `charging_hw_bq24296.c` shipped in this tree
	  targets an MT6332 companion PMIC that nikel does not have, so the
	  MT6351 version (`lct_charging_hw_bq24296.c`, same current/voltage
	  tables) is compiled instead.

	* Fuel gauge: nikel has a CellWise CW2015 at I2C-1 address 0x62
	  (DT node `cw2015@62`, compatible `nikel,cw2015`). The official
	  MIUI kernel for this board uses a CW2015 gauge together with
	  MediaTek's *classic* battery meter (`battery_meter.c` /
	  `battery_common.c`), not Fuel Gauge 2.0, so this tree now does
	  the same: `CONFIG_MTK_HAFG_20` is disabled and the classic
	  driver pair is built. The kernel uses the gauge percentage for
	  the reported SOC when the gauge is awake and has a loaded
	  battery profile, and falls back to the classic MediaTek value
	  otherwise. The CW2015 driver is read-only: it never writes the
	  battery profile or wakes/resets the gauge. The design capacity
	  is 4100 mAh. On the author's unit the old FG20 path reported
	  79 % while the gauge read ~92 %; after switching to the classic
	  meter, both the classic MediaTek value and the CW2015 agree
	  (94 % at ~4.3 V).

	* Verified on the author's unit (second-hand, battery condition
	  unknown):
		- `bq24296` probes on I2C-0 at 0x6b; REG08 reports
		  `CHRG_STAT=fast charging` and `PG_STAT=1`, REG09=0 (no
		  charger fault).
		- Average battery current is positive while charging
		  (~0.15–0.6 A on a 500 mA PC USB port) and the battery
		  voltage rises when the charge starts.
		- The CW2015 reports a valid SOC (CONFIG=0x02, profile
		  loaded, MODE=0x00 awake) and the UI follows it; the
		  classic battery thread refreshes the `power_supply`
		  attributes every cycle.
		- Charging from about 76 % upward was observed during
		  bring-up; the CW2015 read ~92–94 % at ~4.2–4.3 V in the
		  last tests, but a complete 0–100 % cycle has not been
		  documented yet. Use a real charger for a full-charge
		  test; a PC USB port with the screen on may show a net
		  discharge.

	* `dumpsys battery` still prints duplicated `_smb` fields
	  (`level: 94` followed by `level: 50`, etc.). Those are the
	  dual-battery stubs in `battery_common.c` (`capacity_smb` /
	  `present_smb` are fixed at 50 / 0); Android uses the non-`_smb`
	  `capacity` / `present` values.

=========================================================================
* Touch / UI responsiveness:

	* Touch is an FT5346 on I2C-4 (address 0x38), with its EINT on
	  GPIO85. The INT line is active-low, so the EINT is configured
	  **level-low** and is masked while the touch thread reads a
	  report. Without level-low a fast drag could miss a report and
	  appear to stick until the finger was moved back; without the
	  mask the level interrupt could fire tens of thousands of times
	  per second while a finger was held down.

	* MTK HPS (hotplug) is given a base of four little cores
	  (`little_num_base_perf_serv = 4`, big-cluster base 0). With the
	  old single-core idle state, the built-in CIT touch test and the UI
	  visibly lagged. HPS still moves the online set, but it no longer
	  collapses to one CPU; this costs some idle power.

	* The touch controller is kept powered and in its working mode
	  across suspend/resume; only its IRQ is disabled. The old
	  suspend/resume path power-cycled and reset the panel without
	  replaying the full probe initialization, which made the CIT drag
	  stick again after every screen cycle. Keeping it powered fixes
	  that, at the cost of a small amount of standby power.

	* Verified with the built-in CIT touch test: the full-screen drag
	  completes smoothly both after boot and after a lock/unlock cycle.
	  Idle touch-IRQ counts are stable, and no I2C-4 errors are logged.

=========================================================================
* Security fixes (CVE backports):

	* This tree started from vanilla 3.18.22. It now includes the
	  security fixes from stable 3.18.23 through 3.18.140 that apply to
	  the subsystems this kernel actually builds (157 patches, after
	  filtering out disabled subsystems such as Bluetooth, SCTP/DCCP,
	  KEYS, KVM, mac80211, media/DRM and most SCSI low-level drivers).
	* Covered areas include: core networking (TCP/UDP/IPv4/IPv6, XFRM,
	  netfilter/x_tables, AF_PACKET, AF_UNIX), USB core/gadget/HID/audio,
	  ext4, FUSE, ISO9660, the memory manager (including Dirty COW and
	  the stack-guard gap), futex/perf/timers, TTY/PTTY, ALSA, I2C,
	  ashmem/ION, the block layer and several information-leak fixes.
	* The full inventory, with fix versions, upstream commit hashes and
	  applicability notes, is in Documentation/CVE_PORT_LIST.md and
	  Documentation/CVE_PORT_LIST.csv.
	* Caveats: applicability was decided from this kernel's config, not
	  from a per-patch runtime test; the CVE dataset is complete through
	  about 2024, so 2025-2026 CVEs are not included yet. The result was
	  compiled and boot-tested on the author's single nikel unit only.
	* The CVE backport work was also done with AI assistance, like the
	  driver work above.
	* A second pass adds high-value fixes that were never backported to
	  3.18.y, adapted from the Android common and later stable trees:
	  binder waitqueue/POLLFREE (CVE-2019-2215, CVE-2020-0030), ashmem
	  remap (CVE-2020-0009), seq_file allocation limit (CVE-2021-33909),
	  x_tables compat pad and barriers (CVE-2021-22555, CVE-2021-29650),
	  AF_UNIX MSG_PEEK garbage collection (CVE-2021-0920), fget
	  revalidation (CVE-2021-4083), AF_PACKET recvmsg/tpacket bounds
	  (CVE-2022-20158, CVE-2020-14386), and TCP SACK/GSO limits
	  (CVE-2019-11477, CVE-2019-11478).

# Building the kernel
```
chmod a+x Build && bash Build
```

# Acknowledgements:
* [@jmpfbmx](https://github.com/jmpfbmx)
* [@R0rt1z2](https://github.com/R0rt1z2)
