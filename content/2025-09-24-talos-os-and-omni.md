+++
title = "Kubernetes on the Edge made easy with Talos Linux and Omni"
date = "2025-09-24"
description = "Breakout session demoing Talos Linux and Omni"
[taxonomies]
tags = ["Edge","Edgecase","Edgecase2025","Conference","Notes", "Linux", "Kubernetes","Talos"]
+++

The Talos team demonstrated how to provision, manage, and operate immutable Kubernetes nodes using **Talos Linux** and **Omni**. The demo was as informative as it was chaotic, including a kernel panic... courtesy of the Demo Gods ;)

## Provisioning Workflow

Talos emphasizes immutability, API-first management, and security. The demo followed the standard flow:
1. **Generate secrets** `talosctl gen secrets`  
   Provides TLS and ETCD keys for nodes.
2. **Generate cluster configuration** `talosctl gen config --with-secrets ./secrets.yaml ...`  
   Merges secrets and allows customization.  
    - Demo used QEMU/virt-install to spin up VMs.
    - Configuration is heavily commented, making it easy to adjust networking, control plane endpoints, or ETCD settings.
3. **Apply configuration** `talosctl apply-config --insecure --file ...`  
   Boots the node.  
    - Without configuration, nodes boot into **maintenance mode**.
    - Multidoc configs allow partial updates for networking or other changes.
    - Omni tracks diffs and applies patches incrementally.

Being all that chaotic I mostly focussed on a recap of the concepts, I recommend checking the [docs](https://www.talos.dev/latest) and playing with it yourself.

> Fun demo note: the first VM gave a kernel panic. After a restart and correcting the config's disk paths, everything ran smoothly.

## Omni: Cluster Helper

Omni simplifies bootstrap and day-2 operations:
- Manages **PKI and secrets**, revoking access automatically when a user leaves.
- Assists in bootstrapping the **control plane and ETCD**.
- Handles incremental configuration updates (applies diffs instead of full re-deploys).
## Networking

- Default CNI: **Flannel** (other vanilla-compatible CNIs are supported).
- **Cilium** not supported yet, as it requires Helm which Talos avoids bundling.
- Talos API runs on **port 50000**, allowing programmatic management of nodes.

## Immutable & Ephemeral OS

- Talos is fully **immutable**: OS is redeployed for upgrades, no in-place patching.
- Uses **SquashFS, EFI, and signed bundles** to ensure integrity. 
  [Wiki](https://en.wikipedia.org/wiki/SquashFS)
- Nodes keep ephemeral configuration; VM/machine state is preserved.

## Automation & Tooling

- [Talhelper](https://budimanjojo.github.io/talhelper/latest/) a CLI helper for provisioning.
- Terraform and Pulumi providers
- For curated Talos resources: [awesome-talos](https://github.com/siderolabs/awesome-talos).
