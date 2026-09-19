# Intune Device Inventory and Diagnostics Review

## Overview

This document records the review of the Device inventory and Device diagnostics sections for the Windows 11 lab endpoint, `Novexusendpoint`, in Microsoft Intune.

The purpose was to inspect the device information and diagnostic capabilities available through the Intune device page.

## 1. Device Inventory Review

### Procedure

1. Opened the `Novexusendpoint` device record in the Microsoft Intune admin center.
2. Navigated to **Tools → Device inventory**.
3. Reviewed the available inventory categories.
4. Selected the CPU category to inspect its details.

### Available Inventory Categories

The categories visible during the review included:

- Battery
- BIOS Info
- CPU
- Disk Drive
- Encrypt Volume
- Logical Drive
- Memory Info
- Network Adapter
- OS Version
- Registry
- System Enclosure
- System Info
- Time
- TPM
- Video Controller
- Windows QFE

### CPU Inventory Result

**Observation:** The CPU category's details pane was blank during the review.

CPU model, specifications, and other CPU inventory values were not confirmed through this Intune view.

No conclusion was drawn about whether the endpoint's CPU information was unavailable due to collection, reporting, or another cause.

## 2. Device Diagnostics Review

### Procedure

1. Opened the `Novexusendpoint` device record in Intune.
2. Navigated to **Tools → Device diagnostics**.
3. Inspected the page for available diagnostic options or actions.

### Result

**Observation:** No diagnostic options or actions were displayed.

No diagnostic package was collected, and no diagnostic operation was performed.

The reason the options were not displayed was not established.

## Summary

The Device inventory and Device diagnostics sections were reviewed for `Novexusendpoint`.

- The CPU inventory details pane was blank.
- The Device diagnostics page displayed no available options.
- No diagnostic package was collected.
- These observations were recorded without assuming a cause.

## Limitations

This review documents only what was visible in the Intune portal at the time of inspection. It does not establish whether the blank CPU pane or unavailable diagnostic options indicate a configuration issue, a collection limitation, or a portal-specific behavior.
