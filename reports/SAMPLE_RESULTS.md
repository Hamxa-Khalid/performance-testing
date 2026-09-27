# Sample Results (Illustrative)

> **These numbers are illustrative example output, not a benchmark.** They were
> hand-crafted to show the shape of a healthy performance report and how to read
> it. Actual figures depend on your network, machine, time of day, and the
> current responsiveness of the JSONPlaceholder public API. Reproduce them by
> running the tests yourself — do not cite these as measured results.

## Artillery — Load Test (`artillery/load-test.yml`)

Profile: warm-up (2 req/s) -> ramp to 5 req/s -> sustained 5 req/s, ~2.5 minutes.

| Metric | Value |
| --- | --- |
| Scenarios launched | 630 |
| Scenarios completed | 630 |
| Requests completed | 2,205 |
| Mean request rate | ~14.7 req/s |
| Response time — min | 62 ms |
| Response time — median (p50) | 118 ms |
| Response time — p95 | 274 ms |
| Response time — p99 | 611 ms |
| Response time — max | 1,090 ms |
| HTTP 200 responses | 1,890 |
| HTTP 201 responses | 315 |
| Errors / timeouts | 0 |
| Error rate | 0.0% |

## Artillery — Smoke Test (`artillery/smoke-test.yml`)

| Metric | Value |
| --- | --- |
| Scenarios completed | 5 |
| Requests completed | 15 |
| p95 response time | 205 ms |
| Error rate | 0.0% |

## JMeter — Load Test (`jmeter/JSONPlaceholder_LoadTest.jmx`)

Profile: 10 threads, 10s ramp-up, 5 loops (~200 samples across 4 endpoints).

| Label | Samples | Avg (ms) | p90 (ms) | p95 (ms) | p99 (ms) | Error % | Throughput (req/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| GET /posts | 50 | 165 | 290 | 340 | 520 | 0.00% | 4.1 |
| GET /posts/1 | 50 | 98 | 150 | 190 | 300 | 0.00% | 4.2 |
| POST /posts | 50 | 210 | 360 | 430 | 690 | 0.00% | 4.0 |
| GET /users | 50 | 120 | 180 | 230 | 410 | 0.00% | 4.1 |
| **TOTAL** | **200** | **148** | **300** | **360** | **560** | **0.00%** | **16.2** |

## How to read these metrics

- **RPS / Throughput** — requests served per second. Higher is generally better,
  but it must be read together with latency and error rate.
- **p50 (median)** — half of requests were faster than this. A good "typical
  experience" number.
- **p95 / p99 (tail latency)** — 95% / 99% of requests were faster than this.
  Tail latency exposes the slow outliers real users actually feel; it matters far
  more than the average for SLAs.
- **Error rate** — percentage of failed requests (non-2xx/3xx, timeouts, assertion
  failures). Anything above ~0–1% under expected load usually warrants investigation.

## Interpretation notes

- A flat p50 with a rising p99 as load increases signals **tail-latency
  degradation** — often GC pauses, connection-pool saturation, or a slow
  dependency — well before the average moves.
- A rising error rate under increasing arrival rate marks the system's
  **saturation point**; the arrival rate just below it is a rough capacity ceiling.
- Compare **smoke vs. load**: if smoke passes but load fails, the code is correct
  but does not scale — a performance problem, not a functional one.
