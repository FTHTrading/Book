# Digital Twin

The Digital Twin is a real-time 2D network visualisation of every agent and transaction on the [Apostle Chain](../apostle/overview.md). It connects to the mesh-pulse WebSocket and animates [commerce](../apostle/commerce.md) as it happens.

<div class="twin-embed">
  <iframe src="https://twin.unykorn.org/" width="100%" height="700" frameborder="0" loading="lazy"></iframe>
</div>

> **Note**: The live dashboard requires the EC2 instance and agent runner to be active. If you see a connection error, the instance may be stopped.

## Features

### Agent Network Graph
- **20 labelled nodes** arranged in a force-directed layout
- **Colour-coded by tier**: blue (control), green (execution), purple (intelligence), orange (interface), gold (operators)
- **Edges** show commerce relationships from the COMMERCE_MAP
- **Click any node** to see its detail panel: agent ID, tier, balance, transaction history

### Transaction Animations
- Real-time **animated particles** flow along edges as transactions settle
- Particle colour matches the sender's tier
- Particle speed indicates transaction amount (larger = faster)
- Each animation corresponds to a real `POST /v1/tx` settlement on the [Apostle Chain](../apostle/overview.md)

### Activity Feed
- Right-side panel shows the live transaction stream
- Each entry: sender → receiver, amount, timestamp, status
- Scrollable history of the last 100 transactions

### Chain Statistics
- **Block height** — current Apostle Chain height
- **Transaction count** — total settled transactions
- **Active agents** — agents with recent heartbeats
- **ATP volume** — total ATP moved in current session

## Technical Stack

| Component | Technology |
|-----------|-----------|
| **Frontend** | Vanilla JS + Canvas 2D |
| **Data Source** | WebSocket (`ws://host:3280/pulse/stream`) |
| **Server** | `digital_twin_server.py` (aiohttp on port 8402) |
| **Hosting** | AWS EC2 (t3.large) + Cloudflare DNS proxy |
| **Domain** | `twin.unykorn.org` |

## Architecture

```text
Browser → twin.unykorn.org
           │
           ▼ (Cloudflare DNS proxy)
      EC2:8402 (aiohttp)
           │
           ├→ static/digital_twin.html (served)
           │
           └→ ws://EC2:3280/pulse/stream
                  │
                  ▼ (live data from agent_runner.py)
             20 agents → mesh-pulse WS → browser canvas
```

For the agents powering this view, see [The Agent Mesh](../apostle/agents.md). For the underlying API, see the [API Reference](./api-reference.md).
