---
title: Kernel Patches
---
# Kernel Patches

What it is
What an OpenWRT kernel patch is

## Note, some of these changes are cosmetic, see commit [de3fcd38b2caecc0bc878e8bc992fe33aa23bf25](https://github.com/UCSMA-Project/OpenWRT-14.07-JS9331/commit/de3fcd38b2caecc0bc878e8bc992fe33aa23bf25#diff-13af2413196137d2b1c0c5c56f319bd9358fc0ee620e3bbae2fea88fe8810933), with file "008-fix_netdev_unregister.patch" for example 

## List of patches

### package/kernel/mac80211/patches 
* 008-fix_netdev_unregister.patch
    * Whitespace

* 150-disable_addr_notifier.patch
    * Whitespace

* 300-pending_work.patch
    * Whitespace

* 402-ath9k-fix-invalid-mac-address-handling.patch
    * Whitespace

* 411-ath5k_allow_adhoc_and_ap.patch
    * Whitespace

* 431-add_platform_eeprom_support_to_ath5k.patch
    * Whitespace

* 440-ath5k_channel_bw_debugfs.patch
    * Whitespace

* 510-ath9k_intr_mitigation_tweak.patch
    * Whitespace

* 522-mac80211_configure_antenna_gain.patch
    * variables tx_rant, rx_rant removed, maybe unused?

* 530-ath9k_extra_leds.patch
    * Whitespace

* 541-ath9k_rx_dma_stop_check.patch
    * Whitespace

* 550-ath9k_entropy_from_adc.patch
    * Whitespace

* 565-ath9k_restart_after_nfcal_failure.patch
    * Whitespace

* 600-0003-rt2x00-rt2800-serialize-shared-memory-access.patch
    * Whitespace

* 600-0005-rt2x00-rt2800lib-add-hw_beacon_count-field-to-struct.patch
    * Whitespace

* 600-0006-rt2x00-rt2800lib-init-additional-beacon-offset-regis.patch
    * Whitespace

* 600-0014-rt2x00-rt2800lib-add-MAC-register-initialization-for.patch
    * Whitespace

* 600-0016-rt2x00-rt2800lib-add-BBP-register-initialization-for.patch
    * Whitespace

* 600-0020-rt2x00-rt2800lib-add-channel-configuration-code-for-.patch
    * TODO!!!
    * Added `rt2800_bbp_write_with_rx_chain(rt2x00dev, 66, reg)` , why?
    * Moved around some sleeping

* 600-0029-rt2x00-rt2800lib-fix-VGC-setup-for-RT3883.patch
    * Whitespace

* 616-rt2x00-support-rt5350.patch
* 800-b43-backports-form-wireless-testing-master-master-20.patch
    * Whitespace

* 910-01-add-support-for-mt7620.patch
    * Whitespace

* 930-disable_11n.patch
    * TODO

* 931-bitrate_order.patch
    * TODO

* 932-add_nf_2g_info_to_debugfs_misc.patch
    * TODO

* 933-add_hardware_queue_details_to_debugfs_queues.patch
    * TODO

* 934-allow_modification_of_txq_params_in_mesh_and_monitor_mode.patch
    * TODO

* 935-export_ath_hw_in_init.patch
    * TODO

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
    * Commit (fc7aa359602ffeeca64fced72232e773d2334e9e)[https://github.com/JackWindows/OpenWRT-14.07-JS9331/commit/fc7aa359602ffeeca64fced72232e773d2334e9e]
    * Reduces TXFIFO queue to 1 frame, and only allows 1 frame at a time in the TXFIFO queue
    * Used to more quickly interrupt transmission on collision
    * See (TX path analysis)[https://github.com/UCSMA-Project/unpublished-docs/blob/f7b42e12a9afcb3f788b5bfbffe54fa069ad6e51/TX_path_analysis.md#modified-driver-code]

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
    * TODO

* 955-allow_set_tx_power_for_monitor_interface.patch
    * TODO

* 956-enable_channel_14.patch
    * TODO

* 957-make_nfcal_parameter_adjustable_through_debugfs.patch
    * TODO

* 958-add_noise_floor_override_option.patch
    * TODO

* 959-use_long_slot_when_init_hw.patch
    * TODO


### Other patches
* package/network/utils/iw/patches/300-add_txq_control.patch
    * Timing for "interframe spacing" now uses μs instead of ms, why?

* target/linux/generic/patches-3.10/728-MIPS-ath79-add-gpio-irq.patch
    * Backported patch from OpenWRT 15 to allow GCC 6 to compile toolchain, from commit 42e95284abe4c1873960aca402339ab1ef5a1b8f
toolchain/gcc/patches/4.8-linaro/030-gcc-6-compile.patch

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
