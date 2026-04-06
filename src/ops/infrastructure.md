# Infrastructure & AWS

## Production Environment

The x402 protocol runs on AWS with Cloudflare DNS proxying.

### EC2 Fleet

| Instance | ID | Type | Purpose |
|----------|-----|------|---------|
| **x402-pay-api** | `i-0a877d8aaaaa42b74` | t3.large | Apostle Chain + Agent Ecosystem + Digital Twin |

**Instance Details**:
- **AMI**: Amazon Linux 2023
- **Region**: us-east-1
- **Elastic IP**: 98.91.89.169
- **Security Group**: `sg-0dd4ae6ed36efe886`
- **SSH Key**: `unykorn-devnet-key.pem`

### Port Map

| Port | Service | Access |
|------|---------|--------|
| 22 | SSH | Admin only |
| 7332 | Apostle Chain (Rust/Axum) | Internal + Health checks |
| 3100 | x402 Facilitator (Node.js) | Internal |
| 3280 | Mesh-Pulse WebSocket | Internal + Twin |
| 8402 | Digital Twin Server (aiohttp) | Public via Cloudflare |

### Cloudflare DNS

| Subdomain | Target | Proxy |
|-----------|--------|-------|
| `twin.unykorn.org` | EC2:8402 | Yes (orange cloud) |
| `407.unykorn.org` | GitHub Pages | Yes |

### Deployment Process

```bash
# SSH into the instance
ssh -i ~/.ssh/unykorn-devnet-key.pem ec2-user@98.91.89.169

# Pull latest code
cd ~/UnyKorn-X402-aws && git pull

# Start Apostle Chain
cd ~/apostle-chain && cargo run --release &

# Start Agent Runner
cd ~/UnyKorn-X402-aws/packages/x402-agent-ecosystem
python src/agent_runner.py &

# Start Digital Twin Server
python src/digital_twin_server.py &
```

### Monitoring

- **Chain health**: `GET http://98.91.89.169:7332/health`
- **Chain status**: `GET http://98.91.89.169:7332/status`
- **Digital twin**: `https://twin.unykorn.org/`
- **Agent stats**: Logged to stdout every 30 seconds by `stats_reporter()`

## Monorepo Structure

```
UnyKorn-X402-aws/
├── packages/
│   ├── api/                      # x402 Facilitator (Node.js, port 3100)
│   ├── x402-agent-ecosystem/     # Agent runner + digital twin
│   │   ├── src/
│   │   │   ├── agent_runner.py   # 20-agent async loop
│   │   │   ├── bootstrap.py      # Agent keypair generation
│   │   │   ├── tx_builder.py     # TxEnvelope construction + Ed25519
│   │   │   └── digital_twin_server.py
│   │   ├── static/
│   │   │   └── digital_twin.html # 2D network visualization
│   │   └── keys/
│   │       └── agents.json       # Generated keypairs
│   ├── gateway-worker/           # Cloudflare edge gateway
│   ├── settlement/               # XRPL + Stellar bridges
│   ├── uny-chain/                # UnyKorn L1 chain node
│   └── ... (40+ packages)
├── registry/                     # Agent registry, chain config
├── docs/                         # Documentation site (407.unykorn.org)
├── scripts/                      # Deployment + utility scripts
└── x402-book/                    # This mdBook
```
