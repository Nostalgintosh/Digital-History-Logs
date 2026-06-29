## Legacy Environment Architecture: Windows XP Sandbox

**Project Context:** The Mlaynder Project
**Objective:** Emulate a legacy x86 environment on Apple Silicon to execute *The Art of Maya* (2002) Personal Learning Edition, enabling the extraction of foundational `.mel` scripts, `.ma` scene files, and classic UI node structures.

## **1. Environment Configuration**
* **Host Environment**: UTM / QEMU (macOS).
* **Virtual Machine Name**: Macintosh HD.
* **Architecture**: i386.
* **Controller Settings**: Force PS/2 Controller enabled; RNG Device disabled.
* **Disk Interface**: IDE.
* **Installation Method**: Full format (Standard) on NTFS partition.

## **2. System Identity**
* **Registered User**: Steve Jobs.
* **Organization**: Apple Computers Inc.
* **Product Key (Verified Volume License)**: `[REDACTED]`

## **3. Driver Injection Protocol**
* **Objective**: Resolve hardware states (PCI/VGA) using Guest Tools ISO.
* **Identification**: Use Device Manager (Manage > Device Manager) to locate hardware IDs via the Details tab.

| Device Type             | Driver Path (D:\Drivers)  | Goal                      |
| :---                    | :---                      | :---                      |
| VirtIO Balloon (Memory) | \Balloon\xp\x86           | Clear PCI Device warning  |
| Display (GPU)           | \qxl\xp\x86               | Enable higher resolutions |

## **4. Execution Procedure**
1.  Navigate to **Update Driver** in device properties.
2.  Select **Install from a list or specific location (Advanced)**.
3.  Choose **Don't search. I will choose the driver to install**.
4.  Click **Have Disk...** and map to the paths specified in Section 3.
5.  Select the **.inf** file and click **Continue Anyway** to bypass signature warnings.

## **5. Troubleshooting Notes**
* **Freeze Prevention**: For freezes at "Inspecting hardware configuration," force the PS/2 controller and disable the RNG device.
* **Installation Timer**: The "24 minutes" countdown is an estimate; monitor actual disk activity in the host's Activity Monitor if the VM appears stalled.

## **6. UI Translation Matrix (Maya -> Blender)**
| Maya Legacy Feature   | Target Blender Behavior               | Extraction Notes |
| :---                  | :---                                  | :---             |
| Hotbox (Spacebar)     | Pie Menu / Custom Floating Context    | Maya's Hotbox is a radial marking-menu overlay triggered by holding Spacebar. Closest Blender equivalent is the Pie Menu system (built into the "Industry Compatible" keymap preset, or extendable via the Pie Menu Editor add-on). Plan: enumerate the marking menus bound to each Maya viewport region before mapping them to pies. |
| QWER Transformation   | Overriding default Blender transforms | Maya binds Q/W/E/R to Select/Move/Rotate/Scale as single-key tool switches (no modifier held). Blender's default keymap uses G/R/S for grab/rotate/scale; switching to the "Industry Compatible" keymap preset already remaps W/E/R to move/rotate/scale, which narrows most of this gap. |
| Node Graph Navigation | Outliner / Shader Editor alignment    | Maya's Hypergraph (unified scene + dependency graph) has no 1:1 Blender equivalent; it splits across the Outliner (hierarchy/dependencies) and Shader Editor (material node graphs). Plan: build a custom workspace layout combining both editors rather than expecting a single panel match. |

*Status: research/planning notes — pending hands-on verification once Maya PLE is running in the VM.*

## **7. Disc Imaging Documentation: The Art of Maya**

This section documents the procedure used to create bit-perfect digital backups of the physical "Art of Maya" installation disc for use in the virtualized environment.

### Overview
Due to USB passthrough limitations in the Windows XP virtual machine, the physical optical media was imaged to local storage using the `dd` command-line utility. This method ensures a bit-accurate clone of the disc structure.

### Imaging Procedure

**1. Identify Disk**
First, identify the device identifier for the external optical drive:
```bash
diskutil list
```

**2. Unmount Disk**
```bash
sudo diskutil unmountDisk force /dev/disk4
```

**3. Create ISO**
```bash
sudo dd if=/dev/disk4 of="/Users/nostalgintosh/Documents/UTM on Macintosh/vWXP/ArtOfMaya2001/ArtOfMaya.iso" bs=2048 status=progress
```

**4. Create Master CDR**
```bash
sudo dd if=/dev/disk4 of="/Users/nostalgintosh/Documents/UTM on Macintosh/vWXP/ArtOfMaya2001/ArtOfMaya_Master.cdr" bs=2048 status=progress
```
The `_Master.cdr` is best practice for archival `.cdr` masters; the `.iso` is the working copy mounted into the Windows XP VM.

**Summary of Parameters**

| Parameter         | Description             | Value                                   |
| :---------------- | :---------------------- | :-------------------------------------- |
| `if`              | Input File              | Source physical disk                    |
| `of`              | Output File             | Destination file path                   |
| `bs`              | Block Size              | 2048 bytes — matches CD-ROM sector size |
| `status=progress` | Displays live transfer rate and progress | Without this flag, `dd` prints nothing until the copy fully completes |

## **8. Software Keys**

- Win:  `[REDACTED]`
- Mac:  `[REDACTED]`

## **9. Historical Context**

* **Windows XP (2001–2014)**: Released by Microsoft in October 2001, built on the NT kernel rather than the consumer-grade 9x line. It became the longest-running Windows release, with support extended to April 2014 due to its entrenchment in legacy industrial and creative-software environments — exactly the kind of environment this VM recreates.
* **Alias Wavefront Maya**: Silicon Graphics acquired both Alias Research and Wavefront Technologies in 1995, merging them into Alias|Wavefront. Maya 1.0 shipped in 1998. The Personal Learning Edition (PLE) used here let students and hobbyists run the full toolset under a watermarked, non-commercial license — the same lineage as *The Art of Maya* (2002), a reference book bundled with early PLE discs.
* **PS/2 Controller Emulation**: IBM introduced the PS/2 port in 1987, over a decade before USB. Windows XP's installer still probes for PS/2-style keyboard/mouse controllers by default; forcing PS/2 emulation in UTM/QEMU avoids the USB-controller detection hangs common when XP meets modern, only-partially-supported virtual chipsets.
* **IDE Disk Interface**: Integrated Drive Electronics (IDE/ATA) was the dominant consumer disk interface from the late 1980s until SATA replaced it in the mid-2000s. XP's stock installer has native IDE drivers but needs VirtIO drivers injected for paravirtualized disks — hence the driver injection protocol in Section 3.
* **ISO 9660 / `.cdr` Formats**: ISO 9660, ratified in 1988, is the standard cross-platform filesystem for optical discs. macOS's native disk-imaging tools default to HFS-based `.cdr` masters, which Windows cannot read directly — the reason a hybrid ISO 9660 conversion step is needed before the disc image can be mounted in the VM.

## **10. Archival References**

* **Windows XP Professional ISO**: [archive.org/details/WinXPProSP3x86](https://archive.org/details/WinXPProSP3x86)
* **The Art of Maya (2002, with Maya PLE companion disc)**: [archive.org/details/artofmaya00alia](https://archive.org/details/artofmaya00alia)