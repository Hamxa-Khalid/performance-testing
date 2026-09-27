# Performance & Load Testing Showcase

> End-to-end performance and load testing of a REST API with **Apache JMeter** and **Artillery** — two of the industry's most widely used tools, demonstrated side by side.

[![JMeter](https://img.shields.io/badge/Apache_JMeter-5.6-D22128?logo=apachejmeter&logoColor=white)](https://jmeter.apache.org/)
[![Artillery](https://img.shields.io/badge/Artillery-2.0-405cf5?logo=artillery&logoColor=white)](https://www.artillery.io/)
[![Node.js](https://img.shields.io/badge/Node.js-%3E%3D18-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Artillery Smoke Test](https://github.com/Hamxa-Khalid/performance-testing/actions/workflows/artillery.yml/badge.svg)](https://github.com/Hamxa-Khalid/performance-testing/actions/workflows/artillery.yml)

---

## Overview

This repository is a compact, professional demonstration of API **performance and load
testing** practices. It targets the reliable, free [JSONPlaceholder](https://jsonplaceholder.typicode.com)
public API and implements the same core workflow in **two complementary tools** so the
strengths of each are easy to compare:

- **Apache JMeter** — a mature, GUI-driven JVM tool, the de-facto standard in enterprise QA.
- **Artillery** — a modern, YAML-configured, Node.js-based tool that drops cleanly into CI/CD.

Both drive the same endpoints (`/posts`, `/posts/1`, `POST /posts`, `/users`), apply
assertions/thresholds, and produce shareable reports.

> **Note on load levels:** All profiles here are deliberately **modest and illustrative**
> — low arrival rates and small thread counts — so the project is safe to run against a
> shared public API. See [Responsible Testing](#responsible-testing).

---

## Tools Used

| | Apache JMeter | Artillery |
| --- | --- | --- |
| **Config format** | XML test plan (`.jmx`), built in GUI | Declarative YAML |
| **Runtime** | JVM (Java) | Node.js |
| **Best for** | Complex, protocol-rich enterprise scenarios; rich GUI analysis | Fast, code-reviewable tests wired into CI/CD pipelines |
| **Reporting** | Built-in listeners + HTML dashboard | JSON output + `artillery report` HTML |
| **Learning curve** | Steeper (feature-rich GUI) | Gentle (YAML + npm) |

**When to use each**

- Reach for **JMeter** when you need a deep, configurable test with many samplers,
  correlation, and a visual workbench — or when your team already standardizes on it.
- Reach for **Artillery** when you want the test defined as version-controlled YAML that
  runs headlessly in a pipeline on every push, with minimal setup.

---

## Test Types Demonstrated

- **Smoke test** — a handful of requests to confirm the API and the scenarios are healthy
  before any heavier run (`artillery/smoke-test.yml`). Ideal for CI gates.
- **Load test** — a steady, expected level of traffic to measure latency and throughput
  under normal conditions (`artillery/load-test.yml`, `jmeter/JSONPlaceholder_LoadTest.jmx`).
- **Stress / ramp** — the Artillery load profile ramps arrival rate over time
  (warm-up -> ramp -> sustained), showing how the system behaves as pressure increases.

---

## Project Structure

```
performance-testing/
├── artillery/
│   ├── load-test.yml            # Ramped load profile (warm-up -> ramp -> sustained)
│   └── smoke-test.yml           # Tiny fast smoke check for CI
├── jmeter/
│   ├── JSONPlaceholder_LoadTest.jmx   # JMeter test plan (opens in GUI)
│   └── README.md                # How to run the JMeter plan (GUI + headless)
├── reports/
│   ├── .gitkeep
│   └── SAMPLE_RESULTS.md        # Illustrative example results + how to read them
├── .github/
│   └── workflows/
│       └── artillery.yml        # CI: runs the Artillery smoke test on push/PR
├── .gitignore
├── LICENSE                      # MIT
├── package.json                 # npm scripts + Artillery dependency
└── README.md
```

---

## Prerequisites

**For Artillery**

- [Node.js](https://nodejs.org/) **>= 18** and npm.
- Install project dependencies once:
  ```bash
  npm ci      # or: npm install
  ```
  This installs Artillery locally (no global install required).

**For JMeter**

- **Java 8+** on the PATH.
- **Apache JMeter 5.5+** — [download here](https://jmeter.apache.org/download_jmeter.cgi),
  unzip, and add its `bin/` directory to your PATH (or launch `bin/jmeter.bat` on Windows).

---

## Running the Tests

### Artillery (npm scripts)

```bash
# Quick smoke test (fast, CI-friendly)
npm run smoke

# Full ramped load test
npm run load

# Load test with JSON output, then generate an HTML report
npm run load:report
npm run report
# -> opens/creates reports/load-report.html
```

Under the hood these map to `artillery run ...`. You can also invoke Artillery directly:

```bash
npx artillery run artillery/load-test.yml
```

> The Artillery configs optionally use the **`artillery-plugin-expect`** plugin for inline
> `expect:` assertions (declared in `package.json` devDependencies). Remove the `plugins:`
> block in the YAML if you prefer to run without it.

### JMeter (GUI)

1. Launch JMeter and choose **File -> Open**.
2. Open `jmeter/JSONPlaceholder_LoadTest.jmx`.
3. Click **Start** (`Ctrl+R`) and review the **View Results Tree**, **Summary Report**, and
   **Aggregate Report** listeners.

### JMeter (headless + HTML report)

Recommended for real load runs. From the repository root:

```bash
jmeter -n -t jmeter/JSONPlaceholder_LoadTest.jmx -l results/results.jtl -e -o results/html-report
```

Then open `results/html-report/index.html`. See [`jmeter/README.md`](jmeter/README.md) for
full details and the meaning of each flag.

---

## Understanding the Results

A worked, annotated example (with a metrics table for both tools) lives in
[`reports/SAMPLE_RESULTS.md`](reports/SAMPLE_RESULTS.md). Key metrics to watch:

- **RPS / Throughput** — requests served per second; capacity indicator.
- **p50 (median) latency** — the typical user experience.
- **p95 / p99 latency** — the tail; the slow requests real users feel. These matter more
  than the average for SLAs and are where scaling problems first appear.
- **Error rate** — share of failed requests (non-2xx/3xx, timeouts, assertion failures).
  A rising error rate under increasing load marks the saturation point.

---

## Responsible Testing

Load testing generates real traffic and can degrade or disrupt a service.

- **Only test systems you own or have explicit written permission to test.**
- The profiles in this repo are intentionally **modest** (low arrival rates, small thread
  counts) so they are appropriate for a shared public demo API.
- Before pointing these tools at any other target, scale down first, coordinate with the
  owning team, and respect the target's terms of service and rate limits.

---

## What This Demonstrates

- Designing realistic, **multi-step API scenarios** (reads + writes) with think times.
- Applying **assertions and thresholds** (status codes, latency percentiles) as pass/fail gates.
- Modeling load **phases** (warm-up, ramp, sustained) rather than flat traffic.
- Producing and **interpreting performance reports** (percentiles, throughput, error rate).
- Wiring performance checks into **CI/CD** with GitHub Actions.
- Fluency across **two different toolchains** and knowing when to reach for each.

---

## Author

**Muhammad Hamza Khalid** — Senior SQA Engineer

- LinkedIn: <https://www.linkedin.com/in/hamzakhalidqa/>
- GitHub: [@Hamxa-Khalid](https://github.com/Hamxa-Khalid)
- Email: muhammadhamzaqae@gmail.com

Licensed under the [MIT License](LICENSE).
