# JMeter Test Plan - JSONPlaceholder Load Test

This folder contains an Apache JMeter test plan (`JSONPlaceholder_LoadTest.jmx`) that
exercises the [JSONPlaceholder](https://jsonplaceholder.typicode.com) public API under a
modest, illustrative load.

> **Responsible testing:** The load profile here is intentionally small. Only run
> load tests against systems you own or have explicit permission to test.

## What the plan contains

| Element | Purpose |
| --- | --- |
| **Thread Group** | 10 threads, 10s ramp-up, 5 loops (~200 total requests). |
| **HTTP Request Defaults** | Protocol `https`, domain `jsonplaceholder.typicode.com`. |
| **HTTP Header Manager** | Sends `Content-Type: application/json` and `Accept: application/json`. |
| **Uniform Random Timer** | Adds realistic think time (0.5s + up to 1s random) between samples. |
| **GET /posts** | Fetch the full posts collection. Asserts HTTP 200. |
| **GET /posts/1** | Fetch a single post. Asserts HTTP 200 and body contains `userId`. |
| **POST /posts** | Create a post from a raw JSON body. Asserts HTTP 201. |
| **GET /users** | Fetch the users collection. Asserts HTTP 200. |
| **Listeners** | View Results Tree, Summary Report, Aggregate Report. |

## Prerequisites

- **Java 8+** (JMeter runs on the JVM).
- **Apache JMeter 5.5+** — download from <https://jmeter.apache.org/download_jmeter.cgi>.

## Open in the JMeter GUI

1. Launch JMeter (`jmeter` on your PATH, or run `bin/jmeter.bat` on Windows / `bin/jmeter` on macOS/Linux).
2. **File -> Open** and select `jmeter/JSONPlaceholder_LoadTest.jmx`.
3. Press the green **Start** button (or `Ctrl+R`) to run, and inspect the listeners.

> Use the GUI for building and debugging test plans, but always run real load tests
> in **non-GUI (headless)** mode — the GUI itself consumes resources and skews results.

## Run headless (recommended for load runs)

From the repository root:

```bash
jmeter -n -t jmeter/JSONPlaceholder_LoadTest.jmx -l results/results.jtl -e -o results/html-report
```

- `-n` — non-GUI mode.
- `-t` — the test plan to run.
- `-l` — the raw results log (`.jtl`).
- `-e -o` — generate an HTML dashboard report into `results/html-report`.

Open `results/html-report/index.html` in a browser to view the dashboard.

> The `results/` directory is git-ignored. Delete it (or the `.jtl` file) before re-running,
> since JMeter refuses to overwrite an existing results file or a non-empty report folder.

## Tuning the load

Edit the **Thread Group** to change intensity:

- `Number of Threads (users)` — concurrent virtual users.
- `Ramp-up period (seconds)` — how long to reach full concurrency.
- `Loop Count` — iterations per thread.

Keep changes modest unless you are testing your own infrastructure.
