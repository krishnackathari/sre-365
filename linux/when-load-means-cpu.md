## When high load indicates CPU saturation

High load only indicates CPU saturation when:
- Run queue (`r`) is near or above CPU core count
- CPU idle time (`id`) is close to zero
- CPU wait (`wa`) is low

If CPU has idle time, high load is usually caused by
blocked processes, not lack of CPU.
