---
description: >-
  This section defines important words used throughout the NervesHub
  documentation.
---

# Terminology

## Auxiliary Device Certificate

An auxiliary device certificate is a secondary device certificate stored on a NervesKey to support development use cases.

## Device

A device is a client of NervesHub that receives firmware updates

## Device Certificate

A device certificate is an X.509 certificate that identifies a device. Each device certificate contains a public key, the device's serial number, validity and other information. NervesHub uses device certificates to authenticate connections from devices.

## Device CA Certificate

A device CA certificate is an X.509 certificate used by device manufacturers to cryptographically sign device certificates. NervesHub uses device CA certificates to validate device certificates.

## Deployment

A deployment specifies a firmware and a match condition on devices. NervesHub notifies all devices matching the condition to download the new firmware.

## Firmware

Firmware refers to the file that's uploaded to NervesHub, stored, and sent to devices. The most common case is that the file contains the software that runs on the device, but this is not a requirement. NervesHub supports firmware created by the [fwup](https://github.com/fhunleth/fwup) utility.

## Firmware signing keys

Firmware signing keys refer to the raw or base64-encoded public and private keys used to sign and authenticate firmware files. NervesHub and devices hold public keys and verify firmware files before processing them. Firmware signatures provide an end-to-end assurance that the firmware installed on a device has not been modified by NervesHub or anyone else.

## Firmware UUID

This is a globally unique firmware identifier created from the contents of the firmware. Two firmware with the same UUID install the bit-for-bit identical software on devices. Likewise firmware with different UUIDs have some difference even if their metadata reports their version numbers to be the same.

## NervesKey

A NervesKey is a hardware module that protects a device's private key and is used to authenticate a device with NervesHub.

## Product

Products organize related devices, firmware, and deployments. Organizations and users can have one or more products. Devices, firmware, and deployments only belong to one product at a time.

