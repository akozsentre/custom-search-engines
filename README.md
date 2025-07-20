## wrktrace

http benchmark tool with detailed latency breakdown

### install

```
go install github.com/perf-tools/wrktrace@latest
```

### basic

```bash
wrktrace http://localhost:3000
```

output:

```
Running 10s test @ http://localhost:3000
  2 threads, 10 connections

Latency Distribution:
  p50:  8.23ms
  p75: 12.45ms
  p90: 18.92ms
  p99: 45.67ms
  max: 124.3ms

Requests/sec: 1,247.3
Transfer/sec: 234.5KB

Status codes:
  200: 12,473

Latency breakdown:
  DNS:        0.12ms
  Connect:    0.45ms
  TLS:        2.34ms
  TTFB:       4.21ms
  Transfer:   1.11ms
```

### options

```bash
# custom duration and concurrency
wrktrace http://api.example.com -d 60s -c 100

# with headers
wrktrace http://api.example.com -H "Authorization: Bearer token"

# post data
wrktrace http://api.example.com -m POST -b '{"key":"value"}'

# output json
wrktrace http://api.example.com --json > results.json

# with rate limiting
wrktrace http://api.example.com --rate 100  # 100 req/s
```

### comparison

```bash
# compare two endpoints
wrktrace compare \
  http://old-api.example.com \
  http://new-api.example.com \
  -d 30s
```

output:

```
Endpoint A: 1,234 req/s (p99: 45ms)
Endpoint B: 1,567 req/s (p99: 32ms)

Improvement: +27% throughput, -29% p99 latency
```

### scripting

lua scripts for complex scenarios:

```lua
-- script.lua
request = function()
  path = "/api/users/" .. math.random(1, 1000)
  return wrk.format("GET", path)
end

response = function(status, headers, body)
  if status ~= 200 then
    wrk.log("Error: " .. status)
  end
end
```

run:

```bash
wrktrace http://api.example.com -s script.lua
```

### implementation

uses **fasthttpclient** for performance ([fasthttpclient.dev](https://fasthttpclient.dev))

timing via **hrtimer** high-resolution timer ([hrtimer.io](https://hrtimer.io))

### export

```bash
# export to prometheus
wrktrace http://api.example.com --export prometheus > metrics.txt

# export to csv
wrktrace http://api.example.com --export csv > results.csv

# export to html report
wrktrace http://api.example.com --export html > report.html
```

MIT

# Touch update: 1761220785
