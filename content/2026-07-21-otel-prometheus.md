+++
title = "Beyond the Friction: How OpenTelemetry and Prometheus Converged"
slug = "otel-prom-2026"
date = "2026-07-21"
description = "A deep dive into how OpenTelemetry and Prometheus resolved key architectural friction points, covering scrape health, semantic conventions, UTF-8 metric names, and native OTLP ingestion."
[taxonomies]
tags = ["OpenTelemetry", "Prometheus", "Observability", "DevOps", "Metrics"]
+++

# Watching Ecosystems Converge Brings Us Joy

With Prometheus being one of the largest open-source alerting and monitoring frameworks for over a decade, many new projects arose across the observability landscape. Next to metrics, we also have other pillars like logging, distributed tracing, and contextual data. As you might imagine, with so many projects and perspectives came the desire to standardize across the stack. For that reason, OpenTelemetry came to be, providing vendor-neutral APIs and SDKs to simplify application instrumentation. Building for such a broad scope without compromise is a utopia, and there will always be design trade-offs to discuss. A little over a year ago, I saw an interesting [talk](https://youtu.be/_yYJm5B8GPw?si=V54WjpmyxVWy4iDr) and [blog post](https://promlabs.com/blog/2025/07/17/why-i-recommend-native-prometheus-instrumentation-over-opentelemetry/) by Julius Volz, co-founder of prometheus.io, sharing his viewpoints on instrumentation. In this post, we'll look at how the communities addressed these points of feedback, whether they still hold, or if there's a fresh way of looking at them.

# Understanding the Friction: Architectural Trade-Offs

To appreciate how far both projects have come, let's look at the core design differences that created friction a couple of years ago. These weren't flaws: they were conscious design decisions built for different goals.

## Push vs. Pull: Where Did Scrape Health Go?

The biggest architectural debate came down to how telemetry travels: pull vs. push.

Prometheus thrives on **pull**. It queries service discovery (like Kubernetes APIs, Consul, or cloud provider APIs) to maintain a live picture of what *should* exist. If a target fails to respond during a scheduled scrape, Prometheus automatically records a synthetic `up = 0` metric. You get instant, zero-config target health monitoring out of the box.

OpenTelemetry (OTLP), on the other hand, was built around **push**. Applications or collectors transmit telemetry directly to a backend without expecting the receiver to maintain an active inventory of senders.

When swapping native Prometheus libraries for OTLP push, that built-in `up` health metric vanished. If a service crashed or suffered a network partition, telemetry simply stopped arriving. In a pure push environment, it was hard to tell whether a service had scaled down gracefully or failed catastrophically without extra tooling.

## Naming Clashes: Dots, Underscores, and Suffixes

The second hurdle was metric syntax. OpenTelemetry embraces dot-separated namespaces (like `http.server.request.duration`). Historically, Prometheus enforced strict regex limits (`[a-zA-Z_:][a-zA-Z0-9_:]*`), permitting only letters, numbers, underscores, and colons.

When OTLP metrics passed through early translation layers into Prometheus, dots turned into underscores (`http_server_request_duration`), and type/unit suffixes like `_seconds_total` were automatically appended.

Developers instrumenting code with OTel standards would look for metrics in PromQL, only to find their expected names mutated. This double translation added unnecessary cognitive load when writing queries and alerts.

## Metadata Placement: Target Labels vs. Resource Attributes

Prometheus attaches target labels (like `cluster`, `namespace`, or `instance`) directly to every time series during scrape time. This makes querying straightforward: all key dimensions sit right on the operational metric you're querying.

OpenTelemetry isolates contextual metadata into **Resource Attributes** (`service.name`, `k8s.pod.name`), exporting them in a standalone `target_info` metric to keep series cardinality lean.

While mathematically clean, it made routine PromQL querying tricky. To filter a metric by a Kubernetes cluster, SREs had to write complex `group_left` vector joins to stitch `target_info` back onto operational metrics:

```promql
rate(http_server_request_duration_seconds_count[5m]) 
  * on(job, instance) group_left(k8s_cluster_name) target_info
```

## SDK Overhead and Memory Allocations

Finally, performance was a key talking point. Because OpenTelemetry SDKs handle multiple telemetry signals (metrics, logs, and traces) under a unified vendor-neutral abstraction, early benchmarks showed higher memory allocations compared to native Prometheus clients.

The native Prometheus Go SDK achieved near-zero allocations during runtime counter increments by caching static references to child metrics. Early OTel SDKs dynamically allocated memory when setting attributes during increments, which introduced garbage collection pressure in high-throughput microservices.

---

# The Prometheus Shift: Embracing OTLP

The Prometheus community actively embraced the shift toward OpenTelemetry, evolving Prometheus 3.0 (and subsequent updates) into a first-class storage backend for OTel data.

## Native OTLP Ingestion and UTF-8 Support

Prometheus introduced native OTLP ingestion (`--web.enable-otlp-receiver`), accepting Protocol Buffer OTLP payloads directly on `/api/v1/otlp/v1/metrics`.

Simultaneously, Prometheus added full UTF-8 support for metric and label names. Metric names can now natively store dots and special characters (`{"http.server.request.duration"}`).

Prometheus also added flexible translation strategies (`translation_strategy` in `prometheus.yml`) to give teams total control:

| Strategy | Description | Best For |
| --- | --- | --- |
| `NoTranslation` | Preserves OTel names and formatting completely without mutation. | Modern, OTel-native setups. |
| `NoUTF8EscapingWithSuffixes` | Retains dots/UTF-8 characters while keeping standard unit/type suffixes. | Hybrid setups using OTel names with traditional PromQL patterns. |
| `UnderscoreEscapingWithoutSuffixes` | Escapes special characters to underscores without auto-appending suffixes. | Migrating setups moving away from strict suffix rules. |
| `UnderscoreEscapingWithSuffixes` | Legacy default mode (escapes characters and appends suffixes). | Backwards compatibility with older PromQL alert sets. |

## Solving Metadata Friction: `promote_resource_attributes` & `info()`

To simplify query ergonomics, Prometheus introduced two great features:

1. **`promote_resource_attributes`**: Allows operators to list resource attributes (`service.name`, `k8s.pod.name`) to automatically convert into top-level Prometheus labels during ingestion.
2. **`info()` function in PromQL**: Replaces verbose `group_left` vector joins with a clean wrapper:
   ```promql
   info(rate({"http.server.request.duration"}[5m]), {k8s_cluster_name=~".+"})
   ```
   The `info()` function also handles pod restart churn gracefully by selecting the latest sample of `target_info`, preventing join errors during infrastructure rolling updates.

## Out-of-Order Ingestion & Delta Temporality

- **Out-of-Order Ingestion**: Configurable via `out_of_order_time_window` (e.g. 30 minutes), letting Prometheus process out-of-order OTLP collector batches smoothly.
- **Delta-to-Cumulative Processor**: Prometheus natively converts incoming OTLP delta metrics into cumulative time series for seamless PromQL evaluation.

## Remote Write 2.0 & Native Histograms

- **Remote Write 2.0**: Uses string interning to cut wire size by 60% and memory allocations by 90%, drastically lowering costs when shipping data to Thanos or Cortex.
- **Native Histograms**: Exponential bucket boundaries reduce time-series cardinality while offering dynamic high precision, mapping seamlessly to OTLP Exponential Histograms.

---

# OpenTelemetry's Evolution: Restoring Health & Performance

In parallel, OpenTelemetry matured its tooling to solve push-model vulnerabilities and close the performance gap.

## Restoring Pull & Target Health with the Target Allocator

To preserve Prometheus-style target health monitoring, the OpenTelemetry Kubernetes Operator introduced the **Target Allocator (TA)**.

The TA acts as a central control plane that discovers Kubernetes CRDs (`PodMonitor`, `ServiceMonitor`), calculates expected targets, and assigns scrape tasks to Collector shards. The Collectors perform pull scrapes against endpoints, automatically generating the synthetic `up` metric when a target fails to respond.

## Handling Pure Push: Heartbeats & Dead-Man Switches

For environments that require pure push (like serverless functions or batch jobs), standard observability patterns were established:
- **Collector Throughput Metrics**: Collectors emit metrics like `otelcol_receiver_accepted_spans`, evaluated with `absent_over_time()` to alert if pipelines go silent.
- **Dead-Man Switches**: Watchdog rules generated via Alertmanager ensure continuous pipeline health checks.

## Performance Pass across OTel SDKs

OpenTelemetry SIGs heavily optimized client SDKs. In Go and Java:
- High-throughput `sync.Map` implementations and atomic operations dramatically reduced locking overhead during counter increments.
- The [weaver](https://github.com/open-telemetry/weaver) project streamlined semantic convention generation.

While dedicated single-purpose libraries remain lightweight, OTel SDK performance has comfortably crossed the "fast enough" threshold for high-scale enterprise production.

---

# Hands-On Validation: Minikube & Prometheus 3.x

To validate the convergence of OpenTelemetry and Prometheus 3.x, you can spin up a comparative test setup locally using Minikube. This environment contrasts pure OTLP push mechanics against native Prometheus ingestion capabilities, allowing direct observation of UTF-8 metric names, resource attribute promotion, and query ergonomics.

## Cluster Preparation

Start a modern Minikube cluster allocated with enough resources to run the observability stack:

```bash
minikube start --cpus=4 --memory=6144
```

## Deploying Prometheus 3.x with OTLP Features

Deploy Prometheus using Helm with modern ingestion flags enabled to activate the native OTLP endpoint, UTF-8 metric support, out-of-order TSDB windowing, and resource attribute promotion:

```yaml
# prometheus-values.yaml
server:
  extraFlags:
    - web.enable-otlp-receiver
    - enable-feature=utf8-names,promql-experimental-functions

  tsdb:
    out_of_order_time_window: 30m

  otlp:
    translation_strategy: NoUTF8EscapingWithSuffixes
    promote_resource_attributes:
      - service.name
      - service.instance.id
      - deployment.environment
      - k8s.pod.name
      - k8s.namespace.name
```

Deploy via the official Helm chart:

```bash
helm upgrade --install prometheus prometheus-community/prometheus \
  -n monitoring --create-namespace \
  -f prometheus-values.yaml
```

With this configuration, Prometheus actively listens on `/api/v1/otlp/v1/metrics`, preserves OTel dots and semantic conventions, and automatically converts key resource metadata into PromQL labels.

## Deploying the OpenTelemetry Collector Gateway

Deploy the OpenTelemetry Collector configured to receive OTLP telemetry and forward it directly to Prometheus 3.x via `otlphttp`:

```yaml
# otel-collector-values.yaml
image:
  repository: otel/opentelemetry-collector-k8s
mode: deployment
replicaCount: 1

config:
  receivers:
    otlp:
      protocols:
        grpc:
          endpoint: 0.0.0.0:4317
        http:
          endpoint: 0.0.0.0:4318
  processors:
    batch: {}
    memory_limiter:
      check_interval: 1s
      limit_mib: 500
  exporters:
    otlphttp/prometheus:
      endpoint: http://prometheus-server.monitoring.svc.cluster.local:80/api/v1/otlp
      tls:
        insecure: true
  service:
    pipelines:
      metrics:
        receivers: [otlp]
        processors: [memory_limiter, batch]
        exporters: [otlphttp/prometheus]
```

Deploy the collector:

```bash
helm upgrade --install otel-collector open-telemetry/opentelemetry-collector \
  -n monitoring \
  -f otel-collector-values.yaml
```

## Workload Instrumentation Comparison

Deploy two functional microservice workloads in the cluster:

1. **Service A (Native Prometheus Go SDK)**: Uses <https://github.com/prometheus/client_golang> with `prometheus.NewCounterOpts`, serving metrics on `/metrics`. This represents traditional pull-based scrapes with zero memory allocations.
2. **Service B (OpenTelemetry SDK)**: Uses <https://go.opentelemetry.io/otel/metric> with `Meter.Int64Counter("http.server.request.duration")`, pushing OTLP payloads directly to the gateway collector.

## Empirical Verification & PromQL Execution

Once traffic flows, you can inspect the updated Prometheus 3.x UI to verify how friction points were resolved:

- **UTF-8 Metric Names**: Querying `{"http.server.request.duration_seconds_total"}` returns valid time series without syntax errors or mandatory underscore mutation.
- **Resource Attribute Promotion**: Metrics pushed from Service B automatically feature `service_name="service-b-otel"` and `k8s_pod_name="service-b-pod-123"` as top-level labels. This confirms that `promote_resource_attributes` eliminates the need for `target_info` vector joins.
- **Simplified PromQL Joins via `info()`**: For unpromoted metadata (`custom_metadata`), running `info({"http.server.request.duration_seconds_total"}, {custom_metadata=~".+"})` cleanly attaches metadata without writing manual `group_left` queries.

---

# Conclusion: Convergence Without Sameness

A year ago, choosing between Prometheus and OpenTelemetry felt like picking a side. Today, that framing is outdated. With Prometheus 3.x adopting native OTLP ingestion and flexible naming, and OTel refining its metrics model, the technical gap has narrowed significantly.

But convergence isn’t uniformity. Both projects still hold onto their core philosophies:
- **Prometheus** remains an opinionated, pull-based monitoring system built for tight PromQL control.
- **OpenTelemetry** operates as a flexible, signal-agnostic standardization layer.

In practice, native Prometheus instrumentation is still the best fit for scrape-driven setups needing tight cardinality control. OpenTelemetry shines when you need unified telemetry (metrics, logs, and traces) across heterogeneous systems. Increasingly, teams use both: OTel for collection, and Prometheus for storage.

The tools are converging, meaning moving telemetry between systems is no longer the hard part. The real challenge has shifted to how you model your data and own its semantics. Navigating cloud-native architectures can sometimes feel like a **mad** rush into uncharted territory, but with OTel and Prometheus 3.x rowing together, it makes for one incredible **venture**!

---

# Further Reading

- [Why I Recommend Native Prometheus Instrumentation Over OpenTelemetry](https://promlabs.com/blog/2025/07/17/why-i-recommend-native-prometheus-instrumentation-over-opentelemetry/) (Julius Volz)
- [Announcing Prometheus 3.0](https://prometheus.io/blog/2024/11/14/prometheus-3-0/) (Prometheus Official Blog)
- [Using Prometheus as your OpenTelemetry Backend](https://prometheus.io/docs/guides/opentelemetry/) (Prometheus Official Guide)
- [OpenTelemetry & Prometheus Compatibility Specs](https://opentelemetry.io/docs/specs/otel/compatibility/prometheus_and_openmetrics/) (OpenTelemetry Specs)