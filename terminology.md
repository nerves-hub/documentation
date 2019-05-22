---
description: >-
  This section defines important words used throughout the NervesHub
  documentation.
---

# Terminology

## Firmware

Firmware refers to the file that's uploaded to NervesHub, stored, and sent to devices. The most common case is that the file contains the software that runs on the device, but this is not a requirement. NervesHub supports firmware created by the [fwup](https://github.com/fhunleth/fwup) utility. 

## Device

A device is a client of NervesHub that receives firmware updates

## Deployment

A deployment specifies a firmware and a match condition on devices. NervesHub notifies all devices matching the condition to download the new firmware.

## Firmware UUID

This is a globally unique firmware identifier created from the contents of the firmware. Two firmware with the same UUID install the bit-for-bit identical software on devices. Likewise firmware with different UUIDs have some difference even if their metadata reports their version numbers to be the same.

## Product

Products organize related devices, firmware, and deployments. Organizations and users can have one or more products. Devices, firmware, and deployments only belong to one product at a time.







