# Cachee Integration Examples

Official integration examples for **[Cachee.ai](https://cachee.ai)** — drop-in RESP proxy that accelerates any Redis-compatible cache with sub-microsecond L1 reads.

## What is Cachee?

[Cachee](https://cachee.ai) is an AI-powered caching proxy that sits in front of your existing cache (Redis, ElastiCache, CloudFlare KV, etc.) and serves hot data from L1 memory in **17 nanoseconds**. No code changes — swap one connection string.

- **17ns** GET latency (59,000x faster than Redis)
- **59M** ops/sec throughput
- **98.1%** cache hit rate with ML-powered prediction
- **RESP protocol** compatible — works with any Redis client

> [How it works](https://cachee.ai/how-it-works) | [Benchmarks](https://cachee.ai/benchmarks) | [Start free](https://cachee.ai/start)

## Examples by Language

### Node.js
```javascript
// Before: connect to Redis
const redis = new Redis('redis://your-redis:6379');

// After: connect to Cachee (same API, 59,000x faster reads)
const redis = new Redis('redis://cachee-proxy:6380');

const value = await redis.get('user:123:profile');
// 17ns from L1 cache instead of 1ms from Redis
```

> [Full Node.js example](./nodejs/) | [Node.js SDK docs](https://cachee.ai/docs)

### Python
```python
import redis

# Point at Cachee instead of Redis
cache = redis.Redis(host='cachee-proxy', port=6380)

# Same API — sub-microsecond from L1
profile = cache.get('user:123:profile')
```

> [Full Python example](./python/) | [Python SDK docs](https://cachee.ai/docs)

### Go
```go
client := redis.NewClient(&redis.Options{
    Addr: "cachee-proxy:6380", // Cachee instead of Redis
})

val, err := client.Get(ctx, "user:123:profile").Result()
// 17ns L1 hit
```

> [Full Go example](./go/) | [Go SDK docs](https://cachee.ai/docs)

### Rust
```rust
let client = redis::Client::open("redis://cachee-proxy:6380/")?;
let mut con = client.get_connection()?;

let val: String = redis::cmd("GET").arg("user:123:profile").query(&mut con)?;
```

> [Full Rust example](./rust/) | [Rust integration guide](https://cachee.ai/docs)

## Integration Patterns

### Overlay Mode (Accelerate Existing Cache)
Cachee sits in front of your existing cache as an L1 acceleration layer. Your app connects to Cachee, hot data serves from memory, misses forward to your backend.

```
App → Cachee (17ns L1) → Your Redis/ElastiCache/etc (1ms)
```

Supported backends: **AWS ElastiCache**, **CloudFlare KV**, **Redis Cloud**, **Azure Cache**, **GCP Memorystore**, **Upstash**, **Any Redis**

> [All supported integrations](https://cachee.ai/integrations)

### Sidecar Mode (Kubernetes)
Deploy Cachee as a sidecar container alongside your application pod.

```yaml
# See helm-charts repo for full Kubernetes deployment
# https://github.com/HapPhi/cachee-helm-charts
containers:
  - name: app
    image: your-app:latest
    env:
      - name: REDIS_URL
        value: "redis://localhost:6380"
  - name: cachee
    image: cachee/proxy:latest
    ports:
      - containerPort: 6380
```

> [Kubernetes deployment guide](https://cachee.ai/how-it-works) | [Helm charts](https://github.com/HapPhi/cachee-helm-charts)

### Docker Compose
```yaml
services:
  cachee:
    image: cachee/proxy:latest
    ports:
      - "6380:6380"
    environment:
      - CACHEE_UPSTREAM=redis://redis:6379
      - CACHEE_L1_MAX_KEYS=1000000
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
```

> [Full Docker Compose example](./docker-compose/)

## Use Case Examples

| Use Case | Example | Docs |
|----------|---------|------|
| Trading / HFT | [Order lifecycle cache](./trading/) | [cachee.ai/trading](https://cachee.ai/trading) |
| AI/ML Inference | [KV cache + embeddings](./ai-ml/) | [cachee.ai/ai](https://cachee.ai/ai) |
| Gaming | [Game state at 128-tick](./gaming/) | [cachee.ai/gaming](https://cachee.ai/gaming) |
| 5G / Telecom | [MEC edge caching](./telecom/) | [cachee.ai/5g-telecom](https://cachee.ai/5g-telecom) |
| Session Management | [Auth session store](./sessions/) | [cachee.ai/session-scaling](https://cachee.ai/session-scaling) |

## Links

- **Website**: [cachee.ai](https://cachee.ai)
- **Start Free Trial**: [cachee.ai/start](https://cachee.ai/start)
- **Integrations**: [cachee.ai/integrations](https://cachee.ai/integrations)
- **Pricing**: [cachee.ai/pricing](https://cachee.ai/pricing)
- **Blog**: [cachee.ai/blog](https://cachee.ai/blog)
- **Benchmarks**: [github.com/HapPhi/cachee-benchmarks](https://github.com/HapPhi/cachee-benchmarks)
- **Helm Charts**: [github.com/HapPhi/cachee-helm-charts](https://github.com/HapPhi/cachee-helm-charts)

## Products Built With Cachee

| Product | What It Caches | Scale |
|---------|---------------|-------|
| [RevMine.ai](https://revmine.ai) | Trust scores, leaderboards, swap quotes | 1M entries, CacheeLFU, <1µs hit |
| [V100.ai](https://v100.ai) | Video frame metadata, transcoding state | Sub-µs lookups |
| [Auth1.ai](https://auth1.ai) | Rate limits, session state | Zero Redis in hot path |

## License

Apache 2.0