# 05 — Sanitization and Retirement

## Overview

This activity addressed the secure retirement of two obsolete Western Digital 1 TB external hard drives.

The first drive was retired following a forensic imaging and data recovery investigation. The second drive exhibited intermittent connectivity and filesystem accessibility problems, making it unsuitable for continued storage.

The second drive underwent a complete zero-overwrite operation using Windows DiskPart, followed by independent physical sector read-back verification using PowerShell and C#.

Both drives were subsequently recycled through an electronics disposal facility.

**Project period:** October–November 2026

## Objectives

- Identify storage devices requiring retirement.
- Confirm that necessary data preservation activities have been completed.
- Prevent accidental modification of unrelated storage devices.
- Perform full-drive data sanitization.
- Verify the removal of existing partition structures.
- Independently inspect sampled physical sectors following sanitization.
- Document sanitization limitations.
- Responsibly retire and recycle obsolete hardware.

## Hardware and Environment

| Component | Description |
| :--- | :--- |
| Western Digital My Book 1 TB | Original failing HDD used for forensic recovery investigation |
| Western Digital 10EAVS External 1 TB | Second HDD used for documented sanitization and verification |
| Windows 11 Pro Desktop | Sanitization and verification workstation |
| USB interface | Connection for external HDD |

## Tools

| Tool | Purpose |
| :--- | :--- |
| Windows PowerShell | Device identification and verification scripting |
| Windows DiskPart | Disk inspection and full-drive zero overwrite |
| Windows Disk Management | Partition and allocation inspection |
| C# | Native Windows physical disk read-back |
| Windows API | Read-only access to addressable physical disk sectors |

## 1. Original Western Digital My Book Retirement

The first Western Digital My Book 1 TB HDD underwent extensive diagnostic and recovery investigation.

Recorded findings included:

- 58,025 power-on hours.
- 74 pending sectors.
- Actual device read failures.
- A nearly complete sector-level acquisition using GNU ddrescue.
- No recoverable media identified through TestDisk or PhotoRec.

The investigation determined that continued recovery efforts were not justified and that the drive was unsuitable for dependable storage.

The device was subsequently erased and recycled.

### Historical documentation limitation

The original drive's retirement and recycling were completed, but the exact sanitization command and independent verification results were not retained.

Consequently, this project does not claim independently verified sanitization of the first drive.

A second Western Digital HDD was used to demonstrate and document the complete sanitization and verification workflow.

The original recovery investigation is documented in [02 — Forensic Imaging and Recovery](02-forensic-imaging-and-recovery.md).

## 2. Second Western Digital Drive Assessment

**Assessment date:** November 14, 2026

The second Western Digital HDD was identified for retirement because of intermittent connectivity and filesystem accessibility problems.

### Device identification

The following PowerShell command was used to identify connected physical storage devices:

```powershell
Get-Disk |
Format-Table Number, FriendlyName, BusType, Size, HealthStatus, PartitionStyle
```

Recorded results for the target device:

| Property | Result |
| :--- | :--- |
| Disk number | 2 |
| Friendly name | WD 10EAVS External |
| Interface | USB |
| Capacity | 1,000,204,886,016 bytes |
| Windows health status | Healthy |
| Partition style | MBR |

The device reported a healthy Windows storage status despite exhibiting intermittent accessibility problems.

### Partition inspection

```powershell
Get-Partition -DiskNumber 2 |
Format-Table PartitionNumber, DriveLetter, Type, Size
```

Observed partition information:

| Property | Result |
| :--- | :--- |
| Partition number | 1 |
| Drive letter | E: |
| Partition type | IFS |
| Partition size | 1,000,202,241,024 bytes |

An attempt to inspect the existing volume failed:

```powershell
Get-ChildItem -LiteralPath "E:\" -Force
```

Recorded error:

```text
Cannot find drive. A drive with the name 'E' does not exist.
```

Further investigation confirmed that Windows continued to detect the physical disk.

```powershell
Get-Disk -Number 2 |
Format-List FriendlyName, OperationalStatus, HealthStatus, IsOffline, IsReadOnly
```

Recorded output:

```text
FriendlyName      : WD 10EAVS External
OperationalStatus : Online
HealthStatus      : Healthy
IsOffline         : False
IsReadOnly        : False
```

### Assessment

Although the disk remained detectable, its volume accessibility was unreliable.

The owner confirmed that the device contained no data requiring preservation.

**Decision:** Retire the device rather than attempt filesystem repair or continued operational use.

The intermittent connectivity introduced a risk that a software overwrite could fail before completion.

Successful sanitization would therefore require both confirmation of overwrite completion and independent verification.

## 3. Sanitization Preparation and Safety

Before beginning destructive operations, the Seagate backup drive was disconnected to reduce the risk of accidental data loss.

The remaining connected storage devices were inspected using DiskPart.

```text
diskpart
list disk
```

Recorded disk inventory:

```text
Disk ###  Status         Size     Free     Dyn  Gpt
--------  -------------  -------  -------  ---  ---
Disk 0    Online          953 GB  1024 KB        *
Disk 2    Online          931 GB      0 B
```

The devices were identified as:

| Disk | Device | Action |
| :--- | :--- | :--- |
| Disk 0 | Internal NVMe system drive | Preserve — do not modify |
| Disk 2 | Western Digital external HDD | Sanitization target |

The Western Digital drive was explicitly selected:

```text
select disk 2
detail disk
```

Relevant identification results:

```text
WD 10EAVS External USB Device
Disk ID: E8900690
Type   : USB
Status : Online

Current Read-only State : No
Read-only  : No
Boot Disk  : No
Pagefile Disk  : No
Hibernation File Disk  : No
Crashdump Disk  : No
Clustered Disk  : No
```

The existing volume was eventually reported as:

```text
Volume ###  Ltr  Label  Fs    Type       Size    Status
----------  ---  -----  ----  ---------  ------  -------
Volume 4     E          RAW   Partition  931 GB  Healthy
```

The RAW filesystem classification indicated that Windows could not recognize the volume's filesystem.

A RAW partition does not establish that previously stored data has been securely erased.

### Safety controls

- Disconnect unrelated external backup storage.
- Verify the target disk number, model, capacity, and interface.
- Confirm that the target is not the system or boot disk.
- Confirm that no necessary data remains on the target.
- Avoid initializing or formatting the drive following sanitization.

> **Warning:** DiskPart clean all permanently destroys data on the selected disk. Disk numbers can change between sessions. Always independently verify the identity of the target device before executing destructive commands.

## 4. Full-Drive Zero Overwrite

Windows DiskPart was selected to perform a full overwrite of the target HDD.

Unlike the ordinary `clean` command, which removes partition and volume information, `clean all` writes zeros across the disk's accessible logical sectors.

### Sanitization command

After confirming that Disk 2 was the intended Western Digital external HDD:

```text
clean all
```

The operation was allowed to run until DiskPart reported completion.

### Recorded result

```text
DISKPART> clean all

DiskPart succeeded in cleaning the disk.
```

### Sanitization outcome

| Property | Result |
| :--- | :--- |
| Target device | WD 10EAVS External 1 TB |
| Target disk | Disk 2 |
| Sanitization method | Full-drive zero overwrite |
| Tool | Windows DiskPart |
| Command | clean all |
| Operation status | Successful |
| Reported write errors | None |
| Reported disconnection during overwrite | None |

Despite the drive's earlier connectivity problems, DiskPart reported successful completion.

The operation was not interrupted, and the drive was not subsequently initialized or formatted.

## 5. Post-Overwrite Partition Verification

Following completion of the overwrite, the disk was inspected again using:

```text
list disk
detail disk
```

### Recorded disk inventory

```text
Disk ###  Status         Size     Free     Dyn  Gpt
--------  -------------  -------  -------  ---  ---
Disk 0    Online          953 GB  1024 KB        *
* Disk 2  Online          931 GB   931 GB
```

### Recorded target disk details

```text
WD 10EAVS External USB Device
Disk ID: 00000000
Type   : USB
Status : Online

Current Read-only State : No
Read-only  : No
Boot Disk  : No
Pagefile Disk  : No
Hibernation File Disk  : No
Crashdump Disk  : No
Clustered Disk  : No

There are no volumes.
```

### Verification results

| Verification | Result |
| :--- | :--- |
| Physical disk remains detectable | PASS |
| Existing partition structure removed | PASS |
| Entire reported capacity unallocated | PASS |
| Disk ID cleared | PASS |
| Remaining volumes | None |

These results confirmed that the previous partition and volume structures had been removed.

However, partition inspection alone does not independently establish that the underlying disk sectors contain zeros.

A separate physical disk read-back test was therefore performed.

## 6. Independent Sector Read-Back Verification

### Verification design

A PowerShell script incorporating C# and native Windows API calls was used to inspect the physical disk directly.

The script performed read-only sampling at five locations distributed across the disk:

- Beginning of disk.
- Approximately 25% of disk capacity.
- Approximately 50% of disk capacity.
- Approximately 75% of disk capacity.
- Final 1 MiB region of the disk.

Each sample contained 1,048,576 bytes (1 MiB).

Every sampled byte was examined to determine whether it contained a nonzero value.

### Safety and verification controls

The script:

1. Retrieved the target disk's current identification information.
2. Verified its expected model and exact capacity.
3. Opened the physical disk using read-only access.
4. Allocated an aligned memory buffer.
5. Calculated sector-aligned sampling offsets.
6. Read exactly 1 MiB from each selected location.
7. Checked that the requested number of bytes was returned.
8. Counted nonzero bytes.
9. Reported PASS only when all bytes in the sample were zero.
10. Released the memory buffer and disk handle.

The verification did not modify the disk.

### Windows API functions

| Function | Purpose |
| :--- | :--- |
| CreateFile | Open the physical disk with read-only access |
| SetFilePointerEx | Position the read pointer at the selected byte offset |
| ReadFile | Read raw data from the physical disk |
| VirtualAlloc | Allocate an aligned memory buffer |
| VirtualFree | Release the allocated memory |
| Marshal.Copy | Copy the read data into a managed byte array |

<details>
<summary>View the PowerShell/C# verification script used in the lab</summary>

```powershell
$disk = Get-Disk -Number 2 -ErrorAction Stop

if ($disk.FriendlyName -ne 'WD 10EAVS External' -or
    $disk.Size -ne 1000204886016) {
    throw "Disk identity mismatch. Stopping."
}

Add-Type @'
using System;
using System.IO;
using System.Runtime.InteropServices;
using Microsoft.Win32.SafeHandles;
using System.ComponentModel;

public static class WDReadback
{
    [DllImport("kernel32.dll", SetLastError=true,
        CharSet=CharSet.Unicode)]
    static extern SafeFileHandle CreateFile(
        string path, uint access, uint share,
        IntPtr security, uint creation,
        uint flags, IntPtr template);

    [DllImport("kernel32.dll", SetLastError=true)]
    static extern bool SetFilePointerEx(
        SafeFileHandle h, long offset,
        out long position, uint method);

    [DllImport("kernel32.dll", SetLastError=true)]
    static extern bool ReadFile(
        SafeFileHandle h, IntPtr buffer,
        uint count, out uint read, IntPtr overlapped);

    [DllImport("kernel32.dll", SetLastError=true)]
    static extern IntPtr VirtualAlloc(
        IntPtr address, UIntPtr size,
        uint allocationType, uint protect);

    [DllImport("kernel32.dll", SetLastError=true)]
    static extern bool VirtualFree(
        IntPtr address, UIntPtr size, uint freeType);

    public static int CountNonZero(long offset)
    {
        int length = 1048576;

        using (var h = CreateFile(
            @"\\.\PhysicalDrive2",
            0x80000000, 3, IntPtr.Zero,
            3, 0, IntPtr.Zero))
        {
            if (h.IsInvalid)
                throw new Win32Exception(
                    Marshal.GetLastWin32Error());

            IntPtr buffer = VirtualAlloc(
                IntPtr.Zero, (UIntPtr)length,
                0x3000, 0x04);

            if (buffer == IntPtr.Zero)
                throw new Win32Exception(
                    Marshal.GetLastWin32Error());

            try
            {
                long position;
                uint read;

                if (!SetFilePointerEx(h, offset,
                    out position, 0))
                    throw new Win32Exception(
                        Marshal.GetLastWin32Error());

                if (!ReadFile(h, buffer, (uint)length,
                    out read, IntPtr.Zero))
                    throw new Win32Exception(
                        Marshal.GetLastWin32Error());

                if (read != length)
                    throw new IOException("Incomplete read.");

                byte[] data = new byte[length];
                Marshal.Copy(buffer, data, 0, length);

                int nonzero = 0;
                foreach (byte b in data)
                    if (b != 0) nonzero++;

                return nonzero;
            }
            finally
            {
                VirtualFree(buffer, UIntPtr.Zero, 0x8000);
            }
        }
    }
}
'@

foreach ($percent in @(0, 25, 50, 75, 100)) {
    $offset = [long](
        [math]::Floor(
            (($disk.Size - 1MB) * $percent / 100) / 4096
        ) * 4096
    )

    $nonzero = [WDReadback]::CountNonZero($offset)

    [PSCustomObject]@{
        Location     = "$percent%"
        Offset       = $offset
        BytesChecked = 1048576
        NonZeroBytes = $nonzero
        Result       = if ($nonzero -eq 0) {
            "PASS"
        } else {
            "FAIL"
        }
    }
}
```

> This script contains disk identifiers specific to the original lab. Before reuse, independently verify the intended target and update both the PowerShell disk number and the C# physical drive path.

</details>

### Recorded verification results

| Sample location | Byte offset | Bytes checked | Nonzero bytes | Result |
| :--- | ---: | ---: | ---: | :--- |
| 0% | 0 | 1,048,576 | 0 | PASS |
| 25% | 250,050,957,312 | 1,048,576 | 0 | PASS |
| 50% | 500,101,918,720 | 1,048,576 | 0 | PASS |
| 75% | 750,152,876,032 | 1,048,576 | 0 | PASS |
| 100% | 1,000,203,837,440 | 1,048,576 | 0 | PASS |

### Verification summary

| Metric | Result |
| :--- | :--- |
| Samples attempted | 5 |
| Samples successfully read | 5 |
| Bytes per sample | 1,048,576 |
| Total independently examined | 5,242,880 bytes (5 MiB) |
| Total nonzero bytes detected | 0 |
| Failed samples | 0 |
| Overall sampled verification | PASS |

All five sampled regions contained exclusively zero-valued bytes.

The observed results independently supported DiskPart's successful overwrite report.

## 7. Sanitization Assessment

Three distinct forms of evidence supported the final sanitization assessment:

| Evidence | Finding |
| :--- | :--- |
| Overwrite execution | DiskPart reported successful completion |
| Partition inspection | Entire disk unallocated; no remaining volumes |
| Independent physical read-back | All five sampled regions contained zeros |

The combination of successful overwrite execution and independent read-back provided stronger evidence than relying solely on the disappearance of files or partitions.

### Sanitization classification

The procedure demonstrated a software-based overwrite of the HDD's host-addressable storage, consistent with a logical Clear approach.

It did not demonstrate firmware-level sanitization, degaussing, or verified physical destruction.

The distinction matters because storage devices may contain inaccessible or remapped physical sectors that cannot be inspected or overwritten through ordinary host-level disk access.

### Failure contingency

Because the drive exhibited intermittent connectivity, the following contingency was established before sanitization:

- If the overwrite failed, the drive would not be considered successfully sanitized.
- If independent read-back returned errors or nonzero bytes, the discrepancy would require investigation.
- If reliable software sanitization could not be completed, physical destruction would be the preferred retirement method for sensitive data.

These contingencies were not required during the completed overwrite because the operation and all five read-back samples succeeded.

## 8. Hardware Retirement and Recycling

Following the successful sanitization and verification procedure, the second Western Digital HDD was disconnected and removed from service.

The drive was subsequently recycled.

**Retirement confirmed:** November 15, 2026

### Retirement outcomes

| Device | Sanitization Evidence | Final Disposition |
| :--- | :--- | :--- |
| Original WD My Book 1 TB | Historical erase completed; detailed verification unavailable | Recycled |
| Second WD 10EAVS External 1 TB | Successful overwrite and independent sampled verification | Recycled |

Neither device was returned to operational storage service.

Recycling obsolete electronics reduced unnecessary hardware retention and ensured the devices were removed from the active storage environment.

No third-party destruction certificate is claimed for either drive.

## Lessons Learned

- A drive reporting a healthy status can still experience serious connectivity or filesystem problems.
- An empty or RAW filesystem does not establish secure data erasure.
- Removing partitions is not equivalent to overwriting stored data.
- Destructive operations require explicit identification of the target device.
- Disconnecting unrelated storage reduces the consequences of selecting an incorrect disk.
- Software sanitization can fail when storage hardware is unstable.
- Successful overwrite execution should be followed by independent verification.
- Physical disk read-back provides evidence beyond filesystem inspection.
- Sampling can support verification without requiring a second full-capacity read.
- Sanitization claims must reflect the actual method and verification coverage.
- Hardware retirement should include responsible electronics disposal.

## Limitations

- The exact sanitization procedure used on the first Western Digital drive was not retained.
- The second drive's full overwrite was verified through DiskPart's completion report and independent sampling, not a complete byte-by-byte read-back of the entire disk.
- Independent read-back examined 5 MiB of the approximately 1 TB addressable disk.
- Inaccessible or remapped physical sectors could not be independently inspected.
- No firmware-level purge or verified physical destruction was performed as part of the documented sanitization procedure.
- A formal electronics recycling or destruction certificate is not included.
- The custom verification script was used as a controlled technical lab utility, not as a certified enterprise sanitization product.

## References

- [Microsoft DiskPart clean command documentation](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/clean)
- [NIST SP 800-88 Rev. 2 — Guidelines for Media Sanitization](https://csrc.nist.gov/pubs/sp/800/88/r2/final)

## Conclusion

This activity demonstrated the secure retirement of obsolete storage hardware through controlled device identification, full-drive zero overwriting, partition verification, and independent physical sector inspection.

The second Western Digital HDD completed a successful DiskPart overwrite despite earlier connectivity problems. All five independently examined disk regions contained exclusively zeros, supporting the reported sanitization outcome.

Both Western Digital drives were subsequently recycled.

The project demonstrates practical experience with storage sanitization, destructive-operation safety, Windows disk administration, raw disk verification, and evidence-based hardware retirement.

## Navigation

- [Return to Storage Lifecycle Management](README.md)
- [Previous: Encryption and Security](04-encryption-and-security.md)
- [Next: Forensic Image Verification](06-forensic-image-verification.md)
