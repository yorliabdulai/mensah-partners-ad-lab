# 01 — Environment Setup

## Objective

Set up a virtualized lab environment to host a Windows Server 2022 
Domain Controller and a Windows 10 client machine on an isolated 
network.

## Host Machine Specs

- Device: Dell Precision 5540
- CPU: Intel Core i7 9th Gen
- RAM: 32GB
- OS: Windows 11 26H2
- Available Storage at start: 61GB

## Software Used

- VMware Workstation Pro (free for personal use)
- Windows Server 2022 Standard Evaluation (ISO)
- Windows 10 Pro (ISO via Media Creation Tool)

## Initial Approach — VirtualBox

The first attempt used VirtualBox 7.x. The VM consistently failed 
to boot despite the ISO being correctly attached. Troubleshooting 
steps taken:

- Adjusted boot order (Optical first)
- Toggled UEFI on and off
- Disabled Hyper-V on host via bcdedit and DISM
- Changed graphics controller to VBoxSVGA
- Recreated VM using VHD format instead of VDI
- Unblocked ISO file via Windows file properties

None resolved the issue. VirtualBox has known compatibility problems 
with Windows 11 26H2. Switched to VMware Workstation Pro.

## VMware Setup — VM Configuration

**Domain Controller VM (Mensah-DC01):**
- RAM: 4096MB
- CPUs: 2
- Disk: 40GB (single file)
- Network: VMnet1 Host-Only
- Firmware: BIOS

**Client VM (Mensah-WS01):**
- RAM: 2048MB
- CPUs: 2
- Disk: 40GB (single file)
- Network: VMnet1 Host-Only

## ISO Boot Issue

After switching to VMware, the VM entered an EFI boot manager loop 
and would not boot from the ISO. Changing the firmware from UEFI to 
BIOS resolved the loop but produced a PXE network boot error — the 
VM was trying to boot from the network instead of the CD drive.

Root cause identified: the original Windows Server 2022 ISO was 
corrupted during download. Despite showing the correct file size 
(5.66GB), it would not boot. Downloading a fresh ISO resolved the 
issue immediately.

**Lesson learned:** File size does not verify file integrity. The 
correct method is SHA256 checksum verification:

```powershell
Get-FileHash filename.iso -Algorithm SHA256
```

Compare the output against the hash published on Microsoft's 
evaluation center page before attempting to use any ISO.

## Network Configuration

Both VMs placed on VMnet1 (Host-Only) to create an isolated lab 
network. VMware's built-in DHCP on VMnet1 was disabled to allow 
static IP assignment.

**Static IP — Domain Controller:**
- IP: 192.168.10.1
- Subnet: 255.255.255.0
- DNS: 127.0.0.1 (self)

**Static IP — Client Workstation:**
- IP: 192.168.10.2
- Subnet: 255.255.255.0
- Gateway: 192.168.10.1
- DNS: 192.168.10.1

Connectivity verified with successful ping between both machines.
