# 04 — Encryption and Security

## Overview

This activity implemented and verified encryption for both external backup storage and an internal workstation drive.

The Seagate Portable 2 TB HDD was encrypted using BitLocker To Go to protect approximately 373 GB of previously verified backup data.

The Razer Blade 15 workstation was evaluated and protected using Windows Device Encryption with TPM-backed key protection.

Additional testing verified password authentication, cross-device compatibility, recovery-key management, and successful access through a Thunderbolt docking station.

**Project period:** November 2026

## Objectives

- Protect stored data against unauthorized access.
- Implement encryption without destroying existing data.
- Configure password-based authentication for removable storage.
- Verify encryption completion and protection status.
- Back up encryption recovery keys.
- Verify TPM-backed workstation encryption.
- Test encrypted storage across multiple Windows devices.
- Troubleshoot external drive connectivity issues.

## Hardware and Environment

| Component | Description |
| :--- | :--- |
| Seagate Portable 2 TB | External encrypted backup storage |
| Razer Blade 15 Advanced | Windows workstation with internal device encryption |
| Windows desktop | Windows 11 Pro |
| Razer laptop | Windows 11 Home |
| Razer Thunderbolt 4 Dock | External storage connectivity |
| Trusted Platform Module | TPM 2.0 |

## Tools

| Tool | Purpose |
| :--- | :--- |
| BitLocker Drive Encryption | Storage encryption configuration |
| BitLocker To Go | Removable storage encryption |
| Windows Device Encryption | Internal workstation encryption |
| TPM Management | Verify Trusted Platform Module availability |
| manage-bde | Inspect encryption and protection status |
| Windows File Explorer | Authentication and file-access testing |
| Windows Disk Management | Storage connectivity troubleshooting |
| Microsoft account | Recovery-key backup and management |

## 1. Seagate Backup Protection

The Seagate Portable 2 TB HDD contained approximately 373 GB of data backed up from an aging Hitachi internal HDD.

Before encryption, the backup had undergone:

- Full file-content comparison against the Hitachi source.
- Resolution of an individual media permission issue.
- Successful restoration of three representative files.
- SHA-256 verification of the restored files.

These procedures are documented in [03 — Backup and Restoration](03-backup-and-restoration.md).

The backup drive was selected for encryption to protect its contents if the physical device were lost, stolen, or connected to an unauthorized computer.

## 2. BitLocker To Go Configuration

BitLocker To Go was configured using the Windows 11 Pro desktop.

### Configuration procedure

1. Connect the Seagate external HDD.
2. Open Control Panel.
3. Navigate to System and Security > BitLocker Drive Encryption.
4. Locate the Seagate under Removable data drives — BitLocker To Go.
5. Select Turn on BitLocker.
6. Configure password-based unlocking.
7. Back up the recovery key to the Microsoft account.
8. Select Encrypt entire drive.
9. Select Compatible mode.
10. Start encryption.
11. Allow encryption to complete before disconnecting the device.

### Selected configuration

| Setting | Selection |
| :--- | :--- |
| Drive | Media Backup (F:) |
| Encryption coverage | Entire drive |
| Encryption mode | Compatible mode |
| Authentication | Password |
| Recovery method | Numerical recovery password |
| Recovery-key backup | Microsoft account |
| Automatic unlocking | Disabled |

### Full-drive encryption

The entire drive was selected for encryption instead of encrypting used space only.

Because the HDD had previously been used, full-drive encryption provided protection for both existing files and addressable unused space.

The operation preserved the existing backup data and did not require reformatting the drive.

### Compatible mode

Compatible mode was selected because the external HDD was intended for use across multiple Windows computers.

The resulting encryption method was AES-128, using the compatible BitLocker encryption mode rather than the newer XTS-AES mode.

## 3. Encryption Completion and Verification

**Verification date:** November 10, 2026

After encryption completed, the following command was executed from an elevated Windows Command Prompt:

```cmd
manage-bde -status F:
```

### Recorded command output

```text
Volume F: [Media Backup]
[Data Volume]

    Size:                 1863.00 GB
    BitLocker Version:    2.0
    Conversion Status:    Fully Encrypted
    Percentage Encrypted: 100.0%
    Encryption Method:    AES 128
    Protection Status:    Protection On
    Lock Status:          Unlocked
    Identification Field: Unknown
    Automatic Unlock:     Disabled
    Key Protectors:
        Password
        Numerical Password
```

### Verification results

| Verification | Result |
| :--- | :--- |
| Full-drive encryption | 100% complete |
| Encryption method | AES-128 |
| BitLocker protection | On |
| Password protector | Present |
| Recovery password protector | Present |
| Automatic unlocking | Disabled |
| Overall encryption assessment | PASS |

The command output confirmed that the drive was fully encrypted and that BitLocker protection was active.

The numerical password protector represents the BitLocker recovery password.

The unlocked status reflected the drive being accessible during the verification procedure, not a failure of encryption.

## 4. Practical Authentication Testing

Encryption status alone does not establish that the expected authentication workflow functions correctly.

A practical reconnection test was performed.

### Procedure

1. Safely eject the encrypted Seagate drive.
2. Disconnect the USB cable.
3. Reconnect the drive to the Windows 11 Pro desktop.
4. Confirm that Windows requires the BitLocker password.
5. Enter the configured password.
6. Verify access to the backup data.

### Results

| Test | Result |
| :--- | :--- |
| Drive detected after reconnection | PASS |
| Password required | PASS |
| Correct password accepted | PASS |
| Backup data accessible | PASS |

**Overall result:** The encrypted removable drive successfully required authentication after reconnection.

Automatic unlocking remained disabled.

## 5. Recovery-Key Management

During BitLocker configuration, the recovery key was backed up to the Microsoft account.

Windows displayed confirmation that the recovery key had been saved.

Recovery-key management provides an alternative means of accessing encrypted data if the normal unlock password is unavailable.

### Recovery-key handling

- Store recovery information separately from the encrypted drive.
- Restrict access to the Microsoft account containing the recovery information.
- Never publish passwords or numerical recovery passwords.
- Remove sensitive recovery-key information from technical documentation and screenshots.

### Verification scope

| Recovery-key verification | Result |
| :--- | :--- |
| Recovery password protector present | Confirmed |
| Recovery-key backup | Confirmed by Windows |
| Recovery password used to unlock drive | Not tested |

The recovery-key backup was verified through the Windows configuration confirmation.

An actual recovery-password unlock was not performed.

## 6. TPM-Backed Workstation Device Encryption

The Razer Blade 15 workstation was running Windows 11 Home.

Its internal system drive was evaluated using Windows Device Encryption and Trusted Platform Module information.

Windows Device Encryption uses BitLocker technology to protect supported devices, including eligible Windows Home systems.

### TPM verification

The workstation was inspected to confirm the presence and availability of TPM 2.0.

The Trusted Platform Module provides hardware-backed protection for encryption keys.

The TPM does not independently encrypt the storage device. The operating system performs the storage encryption while using the TPM to protect access to the encryption keys.

### Encryption verification

The workstation's system-drive encryption was inspected using:

```cmd
manage-bde -status C:
```

### Recorded findings

| Property | Result |
| :--- | :--- |
| Operating system | Windows 11 Home |
| Encryption technology | Windows Device Encryption / BitLocker |
| TPM version | 2.0 |
| Encryption completion | 100% |
| Encryption method | XTS-AES 128 |
| Protection status | On |
| Key protectors | TPM and Numerical Password |
| Recovery-key backup | Microsoft account |

These findings confirmed that the workstation's internal storage was fully encrypted using TPM-backed protection.

The workstation configuration demonstrated a different authentication model from the password-protected external Seagate drive.

## 7. Cross-Device Compatibility Testing

The encrypted Seagate HDD was subsequently tested on the Razer Blade 15 workstation running Windows 11 Home.

The objective was to confirm that the external backup could be unlocked and accessed on a second Windows computer.

### Initial issue

When connected through the Razer Thunderbolt 4 Dock, the encrypted Seagate drive did not appear in File Explorer.

The drive was also absent from Disk Management.

This indicated that the issue existed at the storage-device detection level, before BitLocker authentication could occur.

### Troubleshooting procedure

1. Inspect File Explorer for the connected drive.
2. Inspect Disk Management for physical disk detection.
3. Disconnect the Seagate from the Thunderbolt dock.
4. Connect the Seagate directly to the laptop.
5. Confirm that Windows detects the drive.
6. Identify the docking station as the relevant connection path.
7. Disconnect power from the Thunderbolt dock.
8. Reconnect dock power after approximately 30 seconds.
9. Reconnect the Seagate to the dock.
10. Verify drive detection, password authentication, and file access.

### Troubleshooting results

| Test | Observation |
| :--- | :--- |
| Initial connection through dock | Drive not detected |
| Disk Management inspection | Drive absent |
| Direct laptop USB connection | Drive detected |
| Dock power reset | Completed |
| Reconnection through dock | Drive detected |
| BitLocker password authentication | Successful |
| Backup file access through dock | Successful |

### Resolution

Power-cycling the Thunderbolt dock restored storage-device detection.

The encrypted HDD was subsequently unlocked using its BitLocker password, and the backup files were accessible.

The exact underlying dock fault was not established. The successful direct connection and subsequent dock reset isolated the problem to the docking connection rather than the encryption configuration.

## 8. Encryption Configuration Comparison

| Feature | Seagate External HDD | Razer Workstation |
| :--- | :--- | :--- |
| Encryption technology | BitLocker To Go | Windows Device Encryption |
| Encryption method | AES-128 | XTS-AES 128 |
| Storage type | Removable HDD | Internal system drive |
| Primary key protector | Password | TPM |
| Recovery protector | Numerical password | Numerical password |
| Encryption coverage | 100% | 100% |
| Protection status | On | On |
| Recovery-key backup | Microsoft account | Microsoft account |
| Authentication approach | Password on reconnection | TPM-backed system startup |

The two implementations demonstrate encryption approaches appropriate to removable storage and internal workstation storage.

## Final Verification Results

| Verification | Result |
| :--- | :--- |
| Seagate full-drive encryption | PASS |
| Seagate BitLocker protection | PASS |
| Password authentication after reconnection | PASS |
| Recovery-key backup confirmation | PASS |
| Workstation TPM 2.0 verification | PASS |
| Workstation device encryption | PASS |
| Windows 11 Pro compatibility | PASS |
| Windows 11 Home compatibility | PASS |
| Thunderbolt dock troubleshooting | Resolved |
| Encrypted backup file accessibility | PASS |

## Lessons Learned

- Encryption should be verified through both command output and practical authentication tests.
- Full-drive encryption is appropriate when previously used storage may contain residual data in unused space.
- Removable storage and internal system drives can require different key-protection mechanisms.
- TPM-backed encryption protects encryption keys using hardware-assisted security.
- Windows Home can access and unlock supported BitLocker To Go drives even when full BitLocker management capabilities differ from Windows Pro.
- Recovery keys should be backed up independently of the encrypted storage device.
- A drive missing from Disk Management indicates a device-detection problem that should be investigated before troubleshooting encryption.
- Direct connections can help isolate docking station and USB connectivity issues.
- Encryption protects data at rest but does not replace backup and restoration procedures.

## Limitations

- Recovery-key backup was confirmed, but an actual recovery-password unlock was not tested.
- Encryption verification did not include an independent cryptographic assessment of the BitLocker implementation.
- The dock connectivity problem was resolved through a power reset, but its precise hardware or firmware cause was not established.
- Existing backup files were accessible after encryption, but a new full file-content comparison was not performed afterward.
- Encryption does not protect files from every threat while the drive is unlocked.
- Recovery keys, passwords, and other sensitive authentication information are intentionally excluded from this public documentation.

## Conclusion

This activity demonstrated the implementation and verification of storage encryption across removable and internal storage devices.

The Seagate Portable 2 TB HDD was fully encrypted using BitLocker To Go, with password authentication, recovery-key backup, and disabled automatic unlocking. The internal Razer workstation was verified to use TPM-backed Windows Device Encryption.

Practical testing confirmed that the encrypted external drive could be accessed on both Windows 11 Pro and Windows 11 Home. A Thunderbolt dock detection issue was isolated and resolved without modifying the encrypted data.

The results demonstrate practical experience with storage security, encryption configuration, recovery-key management, cross-device compatibility, and structured hardware troubleshooting.

## Navigation

- [Return to Storage Lifecycle Management](README.md)
- [Previous: Backup and Restoration](03-backup-and-restoration.md)
- [Next: Sanitization and Retirement](05-sanitization-and-retirement.md)
