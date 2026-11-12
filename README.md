## 📌 Project Overview


This repository documents the architecture and configuration of a multi-tunnel WireGuard edge gateway designed to enforce strict physical and cryptographic isolation between dedicated work and personal workstations. Built on OpenWrt (GL.iNet Slate 7 Pro), the deployment assigns each physical machine to an independent WireGuard tunnel via Policy-Based Routing (PBR), backed by per-tunnel firewall kill switches, 1280-byte MTU clamping, and Layer-2 sandbox isolation to prevent cross-endpoint data compromise.

## 🌐 1. Network Topology & Cryptographic Micro-Segmentation

Routing topology is managed directly at the hardware layer of a Tri-Band portable edge gateway utilizing customized WireGuard client configurations and policy-based routing (PBR).

### Key Optimizations:

- **Packet Size Regulation:** Clamped WireGuard interface MTU to **1280 bytes** to prevent IPv4/IPv6 packet fragmentation across restricted upstream gateways and nested VPN tunnels.
- **Deterministic Policy-Based Routing (PBR) & Split-Tunnel Isolation:** Bound Zone A strictly to a dedicated remote WireGuard VPN concentrator to enforce deterministic egress routing, while isolating general traffic onto a separate local VLAN.
- **Inbound Shielding:** Configured strict firewall drop rules on the WAN/tunnel interface to block inbound LAN subnet scanning and unauthorized lateral movement.

---

## 💻 2. Systems Deployment & Hypervisor Restoration
To completely isolate untrusted guest operating systems from the bare-metal environment, an enterprise-grade Type-2 virtualization pipeline was provisioned on the host.

*   **Host OS:** Pop!_OS 22.04 LTS Linux (Linux Kernel 6.x)
*   **Guest OS:** Windows 11 Pro Virtual Sandbox
*   **Hypervisor Engine:** Native KVM / QEMU Architecture

### Engineering Incident Resolution:
During deployment, the hypervisor engine encountered a critical `libvirtd` system daemon crash due to administrative user group lockouts. The infrastructure was recovered via the command-line interface (CLI) by:
1. Re-architecting local administrative user security groups.
2. Manually provisioning missing virtualization dependency packages (`virt-manager`, `qemu-kvm`, `libvirt-daemon-system`).
3. Initializing and enabling the automated background network bridge via `virsh`.

