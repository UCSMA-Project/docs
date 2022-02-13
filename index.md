---
title: Home
nav_order: 1
---

# UCSMA Docs
This site contains documentations for all UCSMA projects. The content in this page intends to provide a short overview of each projects, while the details can be found in the subpages in the navigation sidebar. 

## Network driver (`ath9k`)
The kernel network driver used throughout the project is `ath9k`. We directly modify this driver to implement some features such as the rate control. 

## Rate control
The rate control protocol is the key part of UCSMA, which attempts to maintain fairness among different nodes in an ad-hoc wireless network. There are two parts of the rate control protocol:

1. buffer enqueue delay: a packet will need to wait a certain time interval (positively proportional to the current buffer size) before being enqueued into the transmission buffer; 
2. contention window adjustment: we set a lower CW for nodes with fuller buffer to allow their packets to be send out quicker.

## Packetspammer
Packetspammer is a throughput-measurement tool that injects unencrypted broadcast packets over Wi-Fi. This utility is adapted from Andy Green's Packetspammer.

## Raspberry Pi GPIO & Transmission Monitoring
It is important to keep track of the transmission timeline of the nodes while conducting experiments, which provides info of the finish times of packet transmissions. In the current setup, all nodes connect to a Raspberry Pi via GPIO and send a GPIO pulse once a packet has finished transmission (at `TX_OK` interrupts); the RPi then generates transmission timeslines for the nodes. 
