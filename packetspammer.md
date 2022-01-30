---
title: Packetspammer
---

# Packetspammer
A throughput-measurement tool using unencrypted broadcast packets.
Adapted from Andy Green's Packetspammer. Original git repository unavailable.

Note: the compiled binary only works on the AR9331 board, with OpenWRT 14

Note: latest code in [ucsma-rate-control](https://github.com/UCSMA-Project/ucsma-rate-control/tree/master/packetspammer)

## Getting started

### Prerequisites
* An AR9331 board running OpenWRT 14
* The `libpcap-dev package` from apt
* A `mips-openwrt-linux-gcc` binary, from compiling OpenWRT 14.
    * Run `build.sh` from [ucsma](https://github.com/UCSMA-Project/ucsma)
* Copying the `packetspammer` binary to the AR9331 (run `make install; make send`, or use `scp`)
* A char device at `/dev/ebbchar`

### Compiling
```bash
# Change PROJECT_ROOT if needed
PROJECT_ROOT="~/ucsma"
STAGING_DIR="$PROJECT_ROOT/staging_dir"
# Ensure STAGING_DIR exists
mkdir -p $STAGING_DIR
BUILD_DIR="$PROJECT_ROOT/build_dir"

# Prepare to compile packetspammer for the AR9331
cd "$PROJECT_ROOT"
cp -r "packetspammer" "build_dir"
CC="$BUILD_DIR/toolchain/OpenWrt-Toolchain-ar71xx-for-mips_34kc-gcc-4.8-linaro_uClibc-0.9.33.2/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/bin/mips-openwrt-linux-gcc"

# Compile packetspammer
LPCAP="$PROJECT_ROOT/OpenWRT-14.07-JS9331/build_dir/target-mips_34kc_uClibc-0.9.33.2/libpcap-1.5.3/"
LPTHREAD="$PROJECT_ROOT/OpenWRT-14.07-JS9331/build_dir/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/uClibc-0.9.33.2/lib/libpthread.so"
LIBS="-L$LPCAP -L$LPTHREAD"
cd "$BUILD_DIR/packetspammer"
$CC -Wall radiotap.c packetspammer.c -o packetspammer $LIBS -I/usr/include -lpcap -ldl -lpthread -std=gnu99
```

* NOTE: `STAGING_DIR` is only defined to avoid this error: 
```
mips-openwrt-linux-uclibc-gcc.bin: warning: environment variable 'STAGING_DIR' not defined
```
* NOTE: `-I/usr/include` is used to fix gcc not finding pcap headers


## Usage
```bash
./packetspammer -d <delay> <monitor_id>
```
* `delay`: in μs, between packets to send
* `monitor_id`: a string to specify the network device

## About the code
* ieee80211_radiotap.h: header for 80211 packet manipulation
* radiotap.*: Unused
    * NOTE: packetspammer should be using ieee80211_radiotap.h instead
* packetspammer.h:
    * NOTE: Typdefs for u8, u16 and u32 are made, but they could be using uint*_t
    * NOTE: `unlikely` macro only used in radiotap.c, could be removed
    * radiotap_hdr_t unused
        * NOTE: Flags are 1-bit wide, but are defined as uint8_t, a bitmap should just be defined
* packetspammer.c:
    * NOTE: `PENUMBRA_RADIOTAP_DATA` is unused
    * Defines a template radiotap and 802.11 headers: `u8aRadiotapHeader`, `u8aIeeeHeader` 
        * Note: for the radiotap header, a radiotap struct could have been used for intialization
    * `print_speed`: thread routine that calculates and prints the sending rate every `RATE_STAT_INTERVAL` seconds
    * Waits a variable amount of time between packet injections
        * Gets the free buffer space from `/dev/ebbchar` (in # packets? # bytes?)
        * Waits for as many μs as there is free buffer space
    * Makes use of multiple pcap functions:
        * `pcap_open_live` for a handle to capture packets on a network device
        * `pcap_datalink` to determine the datalink-layer header type
            * Used to determine if the packet is Ethernet of Prism
        * `pcap_compile` to compile a string to a filter program 
            * Used to capture either packets with an Ethernet header, or a Prism header
                * NOTE: this is probably outdated, Ethernet should be sufficient 
        * `pcap_setfilter` to set the filter program compiled with `pcap_compile`, for a given device handle
        * `pcap_freecode` to free the pointer of the filter program compilted with `pcap_compile`
        * `pcap_inject` to inject a packet on a given network device
            * Sends the template Radiotap + Wifi header 

## Issues
* gcc complains about flags from gcc < 4.4
* `make packetspammer` cannot run on its own. (eg. it needs a location for the mips gcc)
* `make send` depends on a hardcoded IP address, and relies on `make install` being run before
(For documentation: no idea where `/dev/ebbchar` is being written into)

## TODOs
- [ ] Clean up radiotap.*
- [ ] Clean up packetspammer.*: `n80211HeaderLength`, `nCaptureHeaderLength`, `unlikely`, `u*`, `radiotap_hdr_t`, ...
- [ ] Update / simplify build.sh
- [ ] Fix Makefile
    * Simplify install and send
    * Parametrize packetspammer recipe
