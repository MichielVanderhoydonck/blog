+++
title = "Navigating the Healthcare Multiverse"
date = "2025-09-24"
description = "How Roche United Cloud and Edge with a Platform Engineering Saga."
[taxonomies]
tags = ["Edge","Edgecase","Edgecase2025","Conference","Notes"]
+++

## Roche in Context

Roche is an international healthcare and life sciences company, with divisions in diagnostics, pharmaceuticals, and research. Their systems mirrored this diversity, being a patchwork of stacks, instruments, and platforms.

Fragmentation was the main problem. You can’t just ship data anywhere, patient data must stay local for compliance reasons. Roche needed to connect thousands of devices and labs reliably, securely, and consistently.

Their goal is to provide simple, secure compute at the edge, flexible enough for developers but usable by non-technical field engineers.

## The Lab Reality

The lab runs a pipeline of analyzers, that for instance process a blood sample. This workflow used to be a beautiful mess of technologies intertwined, different in every location, with little standardisation. This of course was hard to scale, to secure, and even harder to manage across all countries and their laws.

## Two Very Different Users

Roche recognised early that they were designing for **two personas**:
- **Field service representatives** who are not always technical so tools must be easy, reliable, and low-friction.
- **Developers** that need power, flexibility, and the ability to innovate without being boxed in.

## From Chaos to a Unified Edge

Roche transformed in phases tackling one problem at a time whilst evolving to a platform.

### Phase 1: Assembling the Toolkit
- **Fleet management** 
  How do you manage potentially thousands of clusters? At first, they opted for Rancher.
- **Connectivity simplification** 
  Hospitals allow no inbound traffic and restrict outbound severely. 
  Roche reduced the required IP footprint from 80+ addresses to a single CIDR.
- **Operating system hardening** 
  Adopted RLX, a Debian-based OS.
- **GitOps at scale** 
  Started with FluxCD and learn from it.
- **Data residency** 
  Introduced a three-layer architecture (global → regional → local) keeping patient data where it belongs.

### Phase 2: Hitting the Wall

As scale grew, the limits of the choices made became apparent.

- **Fleet management 2.0** 
  Rancher was replaced with an in-house fleet management console to account more for edge where laptops get closed, cables get yanked etc.
- **Connectivity reimagined** 
  By using Cilium with mTLS tunnels, Roche solved the IP-range bottleneck and enabled secure connectivity to any backend cloud, even from hospitals with strict firewalls. 
  [Bring your own IP space to Cloudflare](https://developers.cloudflare.com/reference-architecture/diagrams/network/bring-your-own-ip-space-to-cloudflare/)
  [Mutual Auth & mTLS](https://docs.cilium.io/en/latest/network/servicemesh/mutual-authentication/mutual-authentication/)
- **OS evolution** 
  Moved to **Talos**, a Kubernetes-native OS. Granting better compliance through immutability, and security. Roche even contributed to Talos to meet healthcare regulations. 
  [10,000 Kubernetes clusters](https://www.youtube.com/watch?v=H1mtCFNgK7k)
- **GitOps bottlenecks** 
  At 100,000+ clusters, Git itself became a performance issue. 
  Solution: “**GitLess GitOps**” with OCI-packaged manifests, supported natively by FluxCD.
- **FoxOps** 
  To manage repo templating and standardisation at scale, Roche built [FoxOps](https://github.com/Roche/foxops), a system that helps keep hundreds of repos aligned with their templates.
  [When GitOps Is Not Enough](https://www.youtube.com/watch?v=_kbeXHLxs_8)

## Results

The fragmented environment became a unified, reproducible platform.
Roche's deployment lifecycles are consistent, making global rollouts swift.
Local deployment enables compliance with local patient-data laws while still benefiting from central innovation. The balance of usability is ensured so that non-technical users get simplicity, while developers retain full power.

See [Navify](https://navify.com) for an example of Roche’s edge-enabled solutions in practice.

## Takeaways

It's not just about Kubernetes or GitOps, platform evolution encounters strict real-world constraints.

- Don’t sell a platform, solve a problem.
- Abstract away complexity, make it simple.
- The Golden Path is paved with self-service.
- Your most important user may not be a developer.
- Evolve your stack — don’t cling to dogma.