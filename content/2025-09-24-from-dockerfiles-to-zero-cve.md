+++
title = "From Dockerfiles to Zero-CVE"
date = "2025-09-24"
description = "Breakout session on building more secure containers"
[taxonomies]
tags = ["Edge","Edgecase","Edgecase2025","Conference","Notes","Security","Kubernetes","Containers","Chainguard"]
+++
The Chainguard workshop focused on **secure container builds**, modern tooling, and best practices for supply chain integrity.

## Secure from the Source: Base Images
- **[Wolfi](https://github.com/wolfi-dev)** is a stripped-down, cloud-native Linux distribution designed for minimal attack surface and supply chain security.
    - Does not provide its own kernel; relies on the container runtime.
    - Provides components at the right granularity with glibc support.
    - Ideal for building Chainguard Containers with embedded SBOMs and signed images.
- **Chainguard curated images** ([chainguard.dev](https://chainguard.dev/)) are hardened and optimized for security.
- **Choosing base images**: ranges from traditional distributions (Debian, Ubuntu, RHEL, Alpine) to distroless images like Wolfi or Chainguard.
    - Trade-offs: large communities vs. minimal footprint and built-in supply chain guarantees.
    
## Chainguard Toolchain

- **[GitHub - Melange](https://github.com/chainguard-dev/melange)**
  Declarative APK package builds with reproducibility and auditable YAML configurations.
- **[GitHub - Apko](https://github.com/chainguard-dev/apko)**
  Creates single-layer container images from APK packages built with Melange.
- **Alternative** to traditional Dockerfiles for core image builds improving traceability, reproducibility, and security.

## Build Methods

- Use **multi-stage builds** when Docker is required.
- Focus on reproducibility, minimal attack surface, and **embedding SBOMs** during build-time.

## Best Practices

1. **Pin images by digest** 
   Ensures immutability and prevents drift.
2. **Sign images with Sigstore** ([sigstore.dev](https://sigstore.dev)) 
   Cryptographically sign images and log to Rekor for transparency and provenance.
3. **Generate SBOMs** 
   Capture build-time components for vulnerability scanning, audits, and compliance.
4. **Enforce in CI/CD** 
   Fail builds or deployments if images are unsigned, unpinned, or fail validation.
5. **Admission control policies** 
   Kyverno or OPA ensure only trusted images run in clusters.

> The open-source container ecosystem suffers from persistent CVEs, large attack surfaces, and opaque provenance. “Shifting left” is important, but without these practices, it’s easy to fall into an endless pull-patch-build-/repeat.

## Recommended Repositories

- **[Denis' workshop examples](https://github.com/maligin/partner-enablement-workshop)**
- **[Apko](https://github.com/chainguard-dev/apko)**
- **[Melange](https://github.com/chainguard-dev/melange)**
- **[Sigstore](https://github.com/sigstore)** 