# VM-Manager

A command-line utility for Linux to seamlessly switch between multiple virtualization platforms (KVM, VirtualBox, VMware, Xen, Proxmox, Multipass). It prevents virtualization conflicts by managing kernel module blacklists and unloading conflicting modules in real-time.

## Why this tool?
Modern Linux systems support a variety of virtualization platforms. However, Type-1 and Type-2 hypervisors often rely on the same hardware virtualization extensions (VT-x for Intel, AMD-V for AMD). When modules for different platforms (e.g., KVM's `kvm_intel` and VirtualBox's `vboxdrv` or VMware's `vmmon`) are loaded simultaneously, they cause hardware resource conflicts, or simply prevent VMs from starting.

`vm-manager` solves this by safely unloading and blacklisting the kernel modules of the hypervisors you are **not** currently using, while loading the ones you **are** using.

## Features
- **Real-time Module Management**: Automatically unloads and blacklists conflicting hypervisor modules without requiring a reboot.
- **Environment Detection**: Run `vm-manager --help` to automatically detect and list which hypervisor packages are currently installed on your local machine.
- **Secure Boot Support**: Easily generate and enroll MOK keys to sign out-of-tree kernel modules (like VMware and VirtualBox) for systems with Secure Boot enabled.
- **Broad Support**: Supports KVM, VirtualBox, VMware, Xen, Proxmox VE, and Multipass out of the box.

## Before install this repo!!!
you have to know that you got another way to change vm provider with some arg without using this repo 
```sh
# use as kernel parameter
kvm.enable_virt_at_load=0

# or add below at /etc/modprobe.d/kvm.conf
options kvm enable_virt_at_load=0

```

## Installation
```bash
git clone https://github.com/mu1aq/vm-manager.git
cd vm-manager
chmod +x install.sh
./install.sh
```

## Uninstallation
```bash
./uninstall.sh
```

## Usage
```bash
sudo vm-manager use kvm       # For KVM with Libvirt/Virt-Manager
sudo vm-manager use vbox      # For VirtualBox
sudo vm-manager use vmware    # For VMware Workstation Pro/Player
sudo vm-manager use xen       # For Xen
sudo vm-manager use proxmox   # For Proxmox VE (KVM/LXC based)
sudo vm-manager use multipass # For Multipass (QEMU/KVM based)

# To sign VMware and VirtualBox modules for Secure Boot:
sudo vm-manager sign

# To see detailed information about each platform and auto-detect installed packages:
vm-manager --help
```

### Supported Hypervisors
- **KVM with Libvirt/Virt-Manager**: Considered the "holy trinity" for Linux virtualization. A type-1 hypervisor built into the Linux kernel. Ideal for server environments and running multiple headless guests.
- **VirtualBox**: A popular type-2 hypervisor that is free and open-source. User-friendly for desktop users and supports cloning virtual disks.
- **VMware**: VMware Workstation Pro/Player. For enterprise bare-metal virtualization, VMware ESXi is a dedicated hypervisor option.
- **Xen**: An open-source type-1 hypervisor specifically designed for hosting multiple VMs efficiently. Supports user-specific access controls.
- **Proxmox VE**: A free, open-source server virtualization management platform based on Debian Linux. Uses KVM/LXC under the hood.
- **Multipass**: A tool by Canonical for launching Ubuntu VMs quickly using QEMU/KVM. Designed for developers simulating cloud deployments locally.

### FAQ: What about QEMU?
**QEMU by itself is a software emulator and does not conflict with VirtualBox or VMware.** Conflicts only occur when QEMU attempts to use **KVM** for hardware acceleration. Because `vm-manager` manages KVM kernel modules (`kvm`, `kvm_intel`, `kvm_amd`), any QEMU-based KVM virtualization is already safely handled without needing explicit QEMU blacklisting.


# Future Work
- [ ] Make it afford various custom VM managers
- [ ] Package it for package managers (APT, RPM, Arch AUR)
