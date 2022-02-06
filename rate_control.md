---
title: Rate Control (WIP)
---

# Rate Control (WIP)

## Overview

The rate control protocol is the key part of the ultimate goal of UCSMA, which attempts to maintain fairness among different nodes in an ad-hoc wireless network. There are two parts of the rate control protocol:

1. buffer enqueue delay: a packet will need to wait a certain time interval (positively proportional to the current buffer size) before being enqueued into the transmission buffer; 
2. contention window adjustment: we set a lower CW for nodes with fuller buffer to allow their packets to be send out quicker.

### Buffer enqueue delay










### Main functions
`ath_cw_update()` (836-865)
* Set up contention window based on the buffer size.
* Larger buffer -> larger contention window.
* Some of the conditions are redundant under the current settings for the CW_max and CW_min. (subject to change after testing)

`ath_tx_default_wait()` (878-880)
* Functionality: set the default waiting time.
* Input buf_size: the size of the buffer.
* Output: the waiting time based on buf_size. Currently the waiting time is set linearly to the buffer size: 100 miliseconds per buffer size. (subject to change after testing)


