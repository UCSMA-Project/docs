---
title: Rate Control (WIP)
---

# Rate Control (WIP)

## Getting started

### Compiling

1. Run [`build.sh`](https://github.com/UCSMA-Project/ucsma/blob/master/build.sh) from the main repository. 

2. Copy the modifed `ath9k` [files](https://github.com/UCSMA-Project/ucsma-rate-control/tree/master/ath9k) to the `OpenWRT-14.07-JS9331/build_dir/target-mips_34kc_uClibc-0.9.33.2/linux-ar71xx_generic/compat-wireless-2014-05-22/drivers/net/wireless/ath/ath9k/` directory.

3. Change directory to the OpenWRT root (`OpenWRT-14.07-JS9331`) and compile with `make package/compile`.

4. Copy the built `ath9k.ko` from `OpenWRT-14.07-JS9331/staging_dir/target-mips_34kc_uClibc-0.9.33.2/root-ar71xx/lib/modules/3.10.49` to the development board and load the kernel module:

```bash
rmmod ath9k
insmod ath9k.ko
./init_mon0.sh  # you may need to change phy0
```

## Overview

The rate control protocol is the key part of the ultimate goal of UCSMA, which attempts to maintain fairness among different nodes in an ad-hoc wireless network. There are two parts of the rate control protocol:

1. buffer enqueue delay: a packet will need to wait a certain time interval (positively proportional to the current buffer size) before being enqueued into the transmission buffer; 
2. contention window adjustment: we set a lower CW for nodes with fuller buffer to allow their packets to be send out quicker.

### Buffer enqueue delay

In the current implementation, the rate control feature calculates a wait time which equals to a scalar multiple of the size of the current size of the buffer (mentioned in [Network Driver (ath9k)](./ath9k.md)).  

The system then delays for the calculated time during the transmission (`ath9k_tx`). The current implementation uses `udelay` from `linux/delay.h`. Note that `udelay` uses the busy-wait mechanism and does not yield the CPU to others tasks. However, `udelay` is still chosen as it is recommanded for atomic contexts (`ath9k_tx` is atomic) and the delay is typically very short. 

### Contention window adjustment

The current implementation modifies the contention window of the hardware once we successfully send out a packet and a free buffer node is send back to the buffer linked list, i.e. during the execution of `ath_tx_return_buffer`. 

After a new contention window being calculated, we set the `CWmin` and `CWmax` parameters to values from 1, 3, 7, 15, 31, 63, 127, 255, 511, and 1023 given by [Cisco documentation](https://www.cisco.com/assets/sol/sb/AP541N_Emulators/AP541N_Emulator_v1.9.2/help_QoS_Parameters.htm). In this step, the system calls the hardware update function to update the contention window parameter registers. 

### Main functions
`ath_cw_update()` (836-865)
* Set up contention window based on the buffer size.
* Larger buffer -> larger contention window.
* Some of the conditions are redundant under the current settings for the CW_max and CW_min. (subject to change after testing)

`ath_tx_default_wait()` (878-880)
* Functionality: set the default waiting time.
* Input buf_size: the size of the buffer.
* Output: the waiting time based on buf_size. Currently the waiting time is set linearly to the buffer size: 100 miliseconds per buffer size. (subject to change after testing)


