# Storage Lifecycle Management — Supporting Evidence

## Overview

This directory contains selected screenshots from the completed Storage Lifecycle Management project.

The evidence documents actual hardware diagnostics, forensic disk imaging, data recovery investigation, backup integrity verification, and cryptographic image verification performed during October and November 2026.

Screenshots are organized by project activity. Selected images are also embedded within the corresponding technical documentation.

## Evidence Index

### 01 — Drive Diagnostics

| Evidence | Description |
| :--- | :--- |
| [WD Windows Partition](wd-windows-partition.png) | Windows Disk Management identifying the original Western Digital HDD and its 931.51 GB NTFS partition. |
| [WD SMART Diagnostics](wd-smart-diagnostics.png) | SMART attributes including 58,025 power-on hours and zero retired sectors. |
| [WD SMART Pending Sectors](wd-smart-pending-sectors.png) | SMART assessment identifying 74 pending sectors and other drive health indicators. |
| [WD DMDE I/O Error](wd-dmde-io-error.png) | DMDE reporting a device I/O error at logical block address 367, with Windows error code 1117. |

[View the complete drive diagnostics documentation](../01-drive-diagnostics.md)

### 02 — Forensic Imaging and Recovery

| Evidence | Description |
| :--- | :--- |
| [ddrescue Acquisition Start](wd-ddrescue-acquisition-start.png) | Initial GNU ddrescue acquisition of the failing Western Digital HDD. |
| [ddrescue Acquisition Results](wd-ddrescue-acquisition-results.png) | Results of the initial sector-level acquisition, including recovery statistics and read errors. |
| [ddrescue Retry Results](wd-ddrescue-retry-results.png) | Retry results documenting 94,208 unrecovered bytes, 368 read errors, and eight bad areas. |
| [Partition Inspection and Read-Only Mount](wd-fdisk-readonly-mount.png) | Linux partition identification and read-only mounting of the acquired image. |
| [Mounted Filesystem Inspection](wd-mounted-filesystem-inspection.png) | Inspection of the NTFS filesystem mounted from the acquired disk image. |
| [TestDisk Boot Sector Inspection](wd-testdisk-boot-sectors.png) | TestDisk identifying valid and matching primary and backup NTFS boot sectors. |
| [TestDisk Root Directory](wd-testdisk-root-directory.png) | TestDisk directory inspection showing no recoverable media in the examined root directory. |
| [PhotoRec Scan](wd-photorec-scan.png) | Signature-based file-carving investigation in progress. |
| [PhotoRec Recovery Results](wd-photorec-recovery-results.png) | Completed recovery scan reporting zero recovered files. |
| [Raw Image Analysis](wd-raw-image-analysis.png) | Full-image analysis identifying approximately 91.6 MiB of nonzero data within the acquired image. |

[View the complete forensic imaging and recovery documentation](../02-forensic-imaging-and-recovery.md)

### 03 — Backup and Restoration

| Evidence | Description |
| :--- | :--- |
| [Backup Comparison Log](backup-comparison-log.png) | FreeFileSync File Content comparison processing approximately 373 GB, with the protected Config.Msi directory recorded as an exception. |
| [Backup Content Comparison](backup-content-comparison.png) | Final comparison showing no differences among accessible files between the Hitachi source and Seagate backup. |

Additional evidence, including the actual SHA-256 hashes from all three successful restoration tests, is recorded in the technical documentation.

[View the complete backup and restoration documentation](../03-backup-and-restoration.md)

### 04 — Encryption and Security

Verification evidence is included directly in the technical documentation rather than as separate screenshots.

Recorded evidence includes:

- Actual `manage-bde -status F:` command output.
- Confirmation of 100% BitLocker encryption.
- AES-128 encryption and enabled protection.
- Password and numerical recovery-password protectors.
- Successful password authentication following drive reconnection.
- TPM-backed workstation encryption results.
- Successful cross-device access and Thunderbolt dock troubleshooting.

[View the complete encryption and security documentation](../04-encryption-and-security.md)

### 05 — Sanitization and Retirement

Verification evidence is included directly in the technical documentation.

Recorded evidence includes:

- Original Windows PowerShell and DiskPart device identification.
- Successful DiskPart `clean all` completion.
- Post-overwrite disk and partition inspection.
- Complete PowerShell/C# physical disk verification script.
- Five successful independent sector read-back samples.
- Exact byte offsets and nonzero-byte counts.
- Confirmed retirement and recycling of both Western Digital HDDs.

[View the complete sanitization and retirement documentation](../05-sanitization-and-retirement.md)

### 06 — Forensic Image Verification

| Evidence | Description |
| :--- | :--- |
| [Forensic Image Acquisition](forensic-image-acquisition.png) | Original SHA-256 baseline generation and successful acquisition of the controlled 64 MiB virtual disk using dd. |
| [SHA-256 Image Verification](forensic-sha256-verification.png) | Successful verification of the original source and acquired image against the recorded SHA-256 baseline. |
| [Read-Only File Extraction](forensic-file-extraction.png) | Read-only image mounting, extraction of test files, and successful post-recovery SHA-256 verification. |

[View the complete forensic image verification documentation](../06-forensic-image-verification.md)

## Evidence Summary

| Category | Screenshots |
| :--- | ---: |
| Drive diagnostics | 4 |
| Physical disk forensic imaging and recovery | 10 |
| Backup and restoration | 2 |
| Controlled forensic image verification | 3 |
| **Total** | **19** |

Encryption and sanitization activities are supported by recorded command output, implementation procedures, and observed verification results within their respective technical documents.

## Evidence Handling

- Screenshots originate from the actual completed project activities.
- Descriptive filenames are used to identify the corresponding technical procedures.
- Personal information and sensitive authentication information are excluded from public documentation.
- Passwords and BitLocker recovery keys are not published.
- Raw disk images and personal recovered files are not included in this repository.
- The original physical WD acquisition and the controlled virtual disk verification lab are documented separately.
- Recorded results reflect the conditions observed at the time of testing.

## Limitations

The evidence demonstrates the actual procedures and results obtained during this personal infrastructure lab.

It does not represent a formal legal forensic examination, certified media destruction, or independent third-party security assessment.

Additional technical limitations are documented within each corresponding project activity.

## Navigation

- [Return to Storage Lifecycle Management](../README.md)
- [Return to IT Infrastructure Labs](../../README.md)
