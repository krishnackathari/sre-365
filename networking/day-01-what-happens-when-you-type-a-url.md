# Daily Networking Interview Question (Real) — Day 1

## Question (Asked in Google SRE interviews)
**“Explain what happens, step by step, when you type `www.google.com` into your browser and press Enter.”**

## Compressed, Interview-Ready Answer (with mini explanations)
When I type `www.google.com` and press Enter, the browser first needs to resolve the domain name to an IP address, so it checks the browser cache, OS cache, and hosts file, and if it’s not found it queries a **recursive DNS resolver** (the DNS server that performs the full lookup on behalf of the client), and that resolver walks the DNS hierarchy by querying the root servers, then the `.com` TLD servers, and finally the **authoritative DNS servers** for `google.com` (the source of truth for the domain) to obtain one or more IP addresses, often anycasted so traffic goes to the nearest edge location, and the result is cached based on TTL values to reduce latency and load on DNS systems.

Once the IP is known, the browser establishes a TCP connection to the server on port 443 using the three-way handshake to create a reliable connection, and then performs a TLS handshake where the server presents its certificate, the browser validates it against trusted certificate authorities and the requested hostname, and session keys are negotiated so all communication is encrypted, with TLS often terminating at a CDN or load balancer rather than the application itself in large-scale systems.

After that, the browser sends an HTTPS request which usually goes first to a **CDN** (caches content close to users to reduce latency), and if the content isn’t cached it’s forwarded to a **load balancer** (distributes traffic across healthy backend instances) and possibly through a reverse proxy or API gateway before reaching the application service, which may call other internal services, consult in-memory caches, and query databases to construct the response.

The response is then returned to the browser, which parses the HTML, downloads CSS and JavaScript, and fetches additional assets like images and fonts, often reusing existing connections or multiplexing requests over HTTP/2 to avoid repeated handshakes, and the overall page load time depends on DNS latency, TCP and TLS handshake time, CDN cache hit rate, backend service performance, network conditions, and client-side rendering efficiency.

From an SRE perspective, this entire flow spans multiple distributed components where failures can occur at any layer—such as DNS outages, expired TLS certificates, overloaded load balancers, unhealthy backend services, or network congestion—which is why production systems rely on redundancy, caching, health checks, timeouts, retries, and strong observability using metrics, logs, and traces to ensure reliability and fast recovery.

## Mini Explanations (quick definitions)
- **Recursive resolver:** DNS server that performs the full lookup on behalf of the client.
- **Authoritative DNS:** source of truth for a domain’s DNS records.
- **Anycast IP:** same IP advertised from multiple locations; routing sends you to the nearest one.
- **TTL:** “time to live” value that controls how long DNS answers can be cached.
- **CDN:** content delivery network; caches and serves content close to users.
- **Load balancer:** distributes traffic across healthy servers/services.
- **TLS termination:** where HTTPS encryption is decrypted—often at a CDN/edge or load balancer.

## Common Follow-ups Interviewers Ask
1. What happens if DNS is slow or incorrect?
2. Where does TLS usually terminate in modern architectures?
3. How is HTTP/2 different from HTTP/1.1, and why does it matter?
4. If the site is “slow,” how would you debug where the time is going?

## Practice Prompt (say it out loud)
Explain this flow in ~90–120 seconds without stopping, then tighten it to ~45 seconds.
