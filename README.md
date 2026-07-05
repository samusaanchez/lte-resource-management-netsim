# LTE / LTE-A Resource Management — Scheduler & Carrier Aggregation Study

**System-level simulation of MAC scheduling (Round Robin vs Proportional Fair) and Intra-Band Carrier Aggregation in NetSim, with a fully documented metrics-extraction pipeline and honest reporting of simulator limitations.**

| | |
|---|---|
| **Type** | System-level network simulation (2×2 factorial experiment) |
| **Author** | Samuel Sánchez García (sole author) |
| **Context** | ECE 491 — Undergraduate Problems, University of New Mexico, Spring 2026 |
| **Simulator** | NetSim Academic v14.1 (TETCOS) |
| **Status** | Complete — **all results are simulated** |

---

## Experiment design

Single LTE macro cell (Band 3, 1800 MHz), three static downlink users at 50 m / 500 m / 1500 m, mixed traffic: one VoIP flow (G.711, UDP, UGS QoS class) plus two saturating FTP/TCP flows. Channel: 3GPP TR 38.901 UMa, Rayleigh fading, BLER enabled, HARQ active. Identical seeds across all runs so differences are attributable to configuration only.

| Iteration | Scheduler | Carrier config | Total BW |
|---|---|---|---|
| 1 | Round Robin | 1 CC | 20 MHz |
| 2 | Proportional Fair | 1 CC | 20 MHz |
| 3 | Round Robin | CA_3C (2 CC) | 40 MHz |
| 4 | Proportional Fair | CA_3C (2 CC) | 40 MHz |

Metrics were extracted from **two independent sources** — the per-packet Packet Trace CSV (37 fields, layer timestamps in µs) and the Application Metrics XLSX — and the systematic discrepancy between them is quantified and documented in the report.

## Key findings (simulated)

- **Proportional Fair lifts the cell edge:** UE3 throughput rises from 0.010 to 0.296 Mbps (≈28-fold, per NetSim Application Metrics) versus Round Robin in the 1-CC case.
- **PF improves queue management:** UE2 mean end-to-end latency drops from 1752.6 ms to 1364.8 ms (−22%) and jitter from 134 ms to 90 ms (−33%). Best UE2 latency of the whole study: **172 ms** (PF + 2 CC).
- **QoS isolation works:** the UGS-class VoIP flow sustains ~0.064 Mbps with 1.0–1.6 ms end-to-end latency in every iteration, regardless of scheduler or contention.
- **TCP bufferbloat dominates FTP latency** (multi-second delays at the cell edge), a transport-layer effect no MAC scheduler can fully repair.

## Most actionable methodological finding

**Activating Carrier Aggregation in NetSim Academic v14.1 degrades aggregate throughput ~5×** (e.g., 2.10 → 0.40 Mbps under RR). The effect is systematic (reproduced with identical seeds) and consistent with the UE-side SCell association not completing in the academic build. Rather than hiding a result that contradicts textbook CA behavior, the anomaly is isolated, evidenced against the vendor documentation, and reported as a limitation — the scheduler comparison (unaffected, 1-CC iterations) remains fully valid.

Other documented limitations: no 5G NR module in the academic license (LTE-A CA used as proxy for the original LTE vs NR comparison), OLLA/Target-BLER not exposed in the GUI, Max CBG per TB capped at 1.

## Repository contents

```
docs/       Full report (PDF): methodology, results, limitations, appendices
data/       Application Metrics (XLSX) — consolidated per-flow results
figures/    Throughput, latency, SINR-throughput correlation, temporal evolution
```

## Skills demonstrated

`LTE / LTE-Advanced` `MAC scheduling` `Proportional Fair` `Carrier Aggregation` `QoS (UGS/GBR)` `3GPP TR 38.901 channel modeling` `HARQ / BLER` `NetSim` `data pipeline (packet-trace post-processing)` `factorial experiment design`
