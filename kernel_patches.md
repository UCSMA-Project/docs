---
title: Kernel Patches
---
# Kernel Patches

What it is
What an OpenWRT kernel patch is

* Patches get applied to "$BUILD_DIR/target-mips_34kc_uClibc-0.9.33.2/linux-ar71xx_generic/compat-wireless-2014-05-22/drivers"

* Note, some of these changes are cosmetic, see commit (de3fcd38b2caecc0bc878e8bc992fe33aa23bf25)[https://github.com/UCSMA-Project/OpenWRT-14.07-JS9331/commit/de3fcd38b2caecc0bc878e8bc992fe33aa23bf25#diff-13af2413196137d2b1c0c5c56f319bd9358fc0ee620e3bbae2fea88fe8810933], with file "008-fix_netdev_unregister.patch" for example 

* Q: what is under our control?

## List of patches

### package/kernel/mac80211/patches 

* 930-disable_11n.patch
    * Disables Wi-Fi 802.11n by setting the `ht_supported` (High Throughput) value to false in the "ht_info" `ieee80211_sta_ht_cap` structure
    * 802.11n "uses multiple antennas to increase data rates", according to (Wikipedia)[https://en.wikipedia.org/wiki/IEEE_802.11n-2009]
    * The experiement transmits using only antenna at the time, and using multiple antennas at a time isn't supported by us

* 931-bitrate_order.patch
    * See (Choose Transmitting Rate for Injected Packets)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/packet_injection_procedure.md#choose-transmitting-rate-for-injected-packets] 
    * Sets desired rate in first place in `ath9k_legacy_rates` array since OpenWRT 14.04 doesn't let you pick the desired rate from the radiotap header 
    * The bitrape is by 100 Kbps. The preferred rate is therefore set to 0x18 * 100 Kbps = 2.4 Mbps

* 932-add_nf_2g_info_to_debugfs_misc.patch
    * Prints NF 2G debug information when `read_file_misc` is called in "debug.c"
    * Noise Floor information for the 2.4 GHz channel: minimum, maximum and nominal
        * Noise floor: a higher noise floor means less sensitivity to noise / interferences around the router
        * Nominal: default value
        * Max / min: range of allowed noise floor values

* 933-add_hardware_queue_details_to_debugfs_queues.patch
    * Prints extra transmission queue information when `print_queue` is called "debug.c".
        * Hardware queue number (ath9k has 10 queues)
        * TODO: ask about qd and ampdu qd
        * Hardware queue depth
        * Hardware A-MPDU
            * Aggregation of 802.11 frames, also called MAC Protocol Data Unit
        * Pending frames in the TX queue
        * Whether the TX queue is stopped
        * TX queue Contention Window min and max: range of values to use when backing off on collision
        * TX queue Arbitration Inter-Frame Spacing (AIFS): the amount of time to wait between a request frame in μs. See (Arbitration Inter-Frame Spacing (AIFS))[https://www.tutorialspoint.com/arbitration-inter-frame-spacing-aifs]
        * TX queue Burst Time: maximum amount of time during which frames can be transmitted at once. Packets in the same burst share the same 802.11 header 
    * NOTE: a TX queue has a `axq_num` ATH9K hardware queue number, used to access the information from the TX queue
    * NOTE: `sc` is the software context of the driver

* 934-allow_modification_of_txq_params_in_mesh_and_monitor_mode.patch
    * Commit (a8581cca7318a09b788754bb2b7ae651e8ecfcba)[https://github.com/UCSMA-Project/OpenWRT-14.07-JS9331/blob/master/package/kernel/mac80211/patches/934-allow_modification_of_txq_params_in_mesh_and_monitor_mode.patch]
    * See (Improve Support for Pure Monitor Mode)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/packet_injection_procedure.md#improve-support-for-pure-monitor-mode] on the need to modify TX queue parameters
    * Modifies the `nl80211` interface to allow setting parameters when the device is in mesh mode or monitor mode
    * Note: "wiphy" refers to a physical device

* 935-export_ath_hw_in_init.patch
    * Exports the `ath_hw` ath9k hardware abstract data structure, making it available to other kernel modules
    * Allows the kernel unlocking module to read and write hardware registers of the card
    * See (Unlocking Kernel Module)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/implement_unlocking.md]

* 936-disable_tx_hung_reset.patch
    * TODO

* 937-disable_beacon_stuck_reset
    * TODO

* 938-set_maxCCApwr.patch
    * TODO

* 939-add_bf_pushback_counter.patch
    * TODO

* 940-ensure_only_one_MPDU_or_AMPDU_in_hw_queue.patch
    * TODO
    * Commit (fc7aa359602ffeeca64fced72232e773d2334e9e)[https://github.com/UCSMA-Project/OpenWRT-14.07-JS9331/commit/fc7aa359602ffeeca64fced72232e773d2334e9e]
    * Reduces TXFIFO queue to 1 frame, and only allows 1 frame at a time in the TXFIFO queue
    * Used to more quickly interrupt transmission on collision
    * See (Modified Driver Code)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/TX_path_analysis.md#modified-driver-code]

* 941-add_timer_to_record_last_hw_puttxbuf_call.patch
    * TODO

* 942-add_param_for_force_quiet_bit_after_puttxbuf.patch
    * TODO

* 943-add_switch_to_force_quiet_bit.patch
    * TODO

* 944-add_another_timer_after_interrupt_tx.patch
    * TODO

* 945-add_param_to_control_delay_between_set_clear_the_force_quiet_bit.patch
    * TODO

* 946-switch_to_getnstimeofday.patch
    * TODO

* 947-add_batch_logging.patch
    * TODO

* 948-allow_zero_cwmin_cwmax.patch
    * TODO

* 949-optional_eifs_equal_to_aifs.patch
    * TODO

* 950-re_structure_delay_logging_code.patch
    * TODO

* 951-fix_mis_type_in_debug_code.patch
    * TODO

* 952-add_log_for_unlocking.patch
    * TODO

* 953-add_driver_gpio_out.patch
    * TODO

* 954-allow_iw_set_wmm_params_for_monitor_interface.patch
    * Further tweak of "300-add_txq_control.patch"
    * Allows setting TX queue CW, AIFS and Burst Time with `iw` even if an interface is in monitor mode
    * See (Config Contention Window/AIFS/Burst Duration)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/AR9331_set_parameters.md#config-contention-windowaifsburst-duration]

* 955-allow_set_tx_power_for_monitor_interface.patch
    * TODO

* 956-enable_channel_14.patch
    * TODO

* 957-make_nfcal_parameter_adjustable_through_debugfs.patch
    * TODO

* 958-add_noise_floor_override_option.patch
    * Backport of the (ath9k: add noise floor override option)[https://patchwork.kernel.org/project/linux-wireless/patch/20170316151337.24163-4-sw@simonwunderlich.de/] OWRT patch 
    * Allows overriding automatically calibrated noise  floor with the following command: 
    `echo [${noise_floor}] > /sys/kernel/debug/ieee80211/phy0/ath9k/nf_override`
    * See (Config Noise Floor)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/AR9331_set_parameters.md#config-noise-floor 
    * See (Load Noise Floor into Hardware)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/creating_experimental_topology.md#load-noise-floor-into-hardware] for an explanation of why the default noise floor calibration can adversely affect our mesh network

* 959-use_long_slot_when_init_hw.patch
    * TODO

### Other patches
* package/network/utils/iw/patches/300-add_txq_control.patch
    * Modifies `iw` to set be able to set TX queues Contention Windows, AIFS, and Burst Time
    * See (Config Contention Window/AIFS/Burst Duration)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/AR9331_set_parameters.md#config-contention-windowaifsburst-duration]

* target/linux/generic/patches-3.10/728-MIPS-ath79-add-gpio-irq.patch
    * Backported patch from OpenWRT 15 to allow GCC 6 to compile toolchain, from commit 42e95284abe4c1873960aca402339ab1ef5a1b8f
toolchain/gcc/patches/4.8-linaro/030-gcc-6-compile.patch

### Trivially changed patches
* These appear when running `git diff --name-only` against the last not made by us. They are listed here for your information
* Note, these changes showed up from this commit merging upstream changes: (de3fcd38b2caecc0bc878e8bc992fe33aa23bf25)[https://github.com/UCSMA-Project/OpenWRT-14.07-JS9331/commit/de3fcd38b2caecc0bc878e8bc992fe33aa23bf25]
* All patches occur in "package/kernel/mac80211/patches"

* 008-fix_netdev_unregister.patch
* 150-disable_addr_notifier.patch
* 300-pending_work.patch
* 402-ath9k-fix-invalid-mac-address-handling.patch
* 411-ath5k_allow_adhoc_and_ap.patch
* 431-add_platform_eeprom_support_to_ath5k.patch
* 440-ath5k_channel_bw_debugfs.patch
* 510-ath9k_intr_mitigation_tweak.patch
* 522-mac80211_configure_antenna_gain.patch
    * (FYI) A patch by OpenWRT originally to allow users to set the maximum to reduce the transmission power
* 530-ath9k_extra_leds.patch
* 541-ath9k_rx_dma_stop_check.patch
* 550-ath9k_entropy_from_adc.patch
* 565-ath9k_restart_after_nfcal_failure.patch
* 600-0003-rt2x00-rt2800-serialize-shared-memory-access.patch
* 600-0005-rt2x00-rt2800lib-add-hw_beacon_count-field-to-struct.patch
* 600-0006-rt2x00-rt2800lib-init-additional-beacon-offset-regis.patch
* 600-0014-rt2x00-rt2800lib-add-MAC-register-initialization-for.patch
* 600-0016-rt2x00-rt2800lib-add-BBP-register-initialization-for.patch
* 600-0020-rt2x00-rt2800lib-add-channel-configuration-code-for-.patch
    * Here, these changes are not whitespace only, but are just the result of an upstream merge, as evidenced by their presence in other forked repositories, such as (here)[https://gitlab.nic.cz/turris/openwrt/-/blob/deploy-2015-12-18/package/kernel/mac80211/patches/600-0020-rt2x00-rt2800lib-add-channel-configuration-code-for-.patch#L129]
* 600-0029-rt2x00-rt2800lib-fix-VGC-setup-for-RT3883.patch
* 616-rt2x00-support-rt5350.patch
* 800-b43-backports-form-wireless-testing-master-master-20.patch
* 910-01-add-support-for-mt7620.patch


## Other changes
* In "include/kernel-defaults.mk", the version magic is fixed to work with kmod
    * NOTE: If another version of OpenWRT were used, this value would need to be changed

* rules.mk
    * Whitespace

* scripts/download.pl
    * New mirrors

* target/linux/ar71xx/base-files/etc/uci-defaults/10_js9331
    *  Handlers added, probably to configure buttons on board to do something, like rebooting

* target/linux/ar71xx/files/arch/mips/ath79/mach-tl-wr720n-v3.c
    * Configure GPIO, buttons and LEDs on board

## Helper scripts
* In the "helper_scripts" directory
* `rget` and `rset` let you get and set register values on ath9k registers
* See (Read/Write Registers)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/AR9331_set_parameters.md#readwrite-registers]
