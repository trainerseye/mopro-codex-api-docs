# MoPro Codex API

The commercial **movement-intelligence API** from TrainersEye Technologies — exercise ontology, typo-tolerant search, variation trees and modelled biomechanics graph domains for partners, universities and product teams.

<p>
  <a href="https://api.mopro-codex.com/v3/developer/portal"><img src="https://img.shields.io/badge/Developer_portal-live-E8891C?style=flat-square" alt="Developer portal"></a>
  <a href="https://api.mopro-codex.com/v3/developer/status"><img src="https://img.shields.io/badge/Status-private_developer_beta-141210?style=flat-square" alt="Private developer beta"></a>
  <a href="https://api.mopro-codex.com/v3/developer/openapi.json"><img src="https://img.shields.io/badge/OpenAPI-3.1-0A66C2?style=flat-square" alt="OpenAPI 3.1"></a>
</p>

| | |
|---|---|
| **Base URL** | `https://api.mopro-codex.com` |
| **Status** | Private developer beta |
| **Portal** | [Developer portal](https://api.mopro-codex.com/v3/developer/portal) · [API reference](https://api.mopro-codex.com/v3/developer/docs) · [Quickstart](https://api.mopro-codex.com/v3/developer/quickstart) · [Get an API key](https://api.mopro-codex.com/v3/developer/get-key) |
| **Machine-readable** | [OpenAPI 3.1](https://api.mopro-codex.com/v3/developer/openapi.json) · [Status](https://api.mopro-codex.com/v3/developer/status) · [Changelog](https://api.mopro-codex.com/v3/developer/changelog) |
| **Contact** | support@trainerseye.com |

## Overview

MoPro Codex is not a general-purpose chatbot API. It is a structured exercise and movement-intelligence layer that other apps, clinics, universities and research teams can call:

- Typo-tolerant, alias-aware **exercise search** (for example `bck squat` or `rdl`)
- **Exercise profiles** — identity, movement family, aliases, variations and figure status
- **Variation trees** — regressions, progressions, equipment and context variations
- **Graph domains** — modelled emphasis on joint angle, torque, phase timing and stability demand

The current ontology includes **35,000 exercise nodes** and **14,000+ aliases**. Every response carries `evidence_boundary` and `safety_boundary` fields. Graph domains are **modelled educational interpretations**, not measured laboratory data, unless measured data are explicitly supplied. See the [evidence boundary](https://api.mopro-codex.com/v3/developer/evidence-boundary).

## Authentication

Protected routes use an API key sent as a Bearer token. Replace the placeholder `YOUR_API_KEY` with the key issued to you — never commit a real key.

```http
Authorization: Bearer YOUR_API_KEY
```

1. Create a **Free Sandbox** key (100 requests / month, no card) at [Get an API key](https://api.mopro-codex.com/v3/developer/get-key).
2. Confirm the key and its plan, limits and usage (these two calls do not count against quota):

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  "https://api.mopro-codex.com/v3/auth/key-status"
```

Commercial plans and licences are listed in the [plan catalogue](https://api.mopro-codex.com/v3/plans) and on the [pricing page](https://api.mopro-codex.com/v3/developer/pricing). During the private beta, paid access is arranged with our team.

## Endpoint categories

Only routes in the public [OpenAPI specification](https://api.mopro-codex.com/v3/developer/openapi.json) are listed here as live. Anything else is marked **coming soon**.

### System — public, no API key

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/v3/ontology/health` | Ontology and service health |
| `GET` | `/v3/plans` | Subscription plan catalogue (quotas and access levels) |

### Auth — API key required

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/v3/auth/key-status` | Plan, limits, expiry and usage for the presented key |
| `GET` | `/v3/auth/usage` | Recent request log (`limit` query, default 50) |

### Exercises — API key required

Search is available on every plan. Tree, children, aliases, graph domains and figures require a Developer plan or above. Some profile, graph-domain and figure fields are fuller on Professional and above — see the [API reference](https://api.mopro-codex.com/v3/developer/docs).

| Method | Path | Purpose | Required query |
|---|---|---|---|
| `GET` | `/v3/exercise/search` | Typo-tolerant, alias-aware search | `q` |
| `GET` | `/v3/exercise/profile` | Full exercise record | `name` (canonical; resolve via search first) |
| `GET` | `/v3/exercise/tree` | Parent plus full child variation structure | `name` |
| `GET` | `/v3/exercise/children` | Direct child variations only | `name` |
| `GET` | `/v3/exercise/aliases` | Registered aliases | `name` |
| `GET` | `/v3/exercise/graph-domains` | Modelled biomechanics graph domains | `name` |
| `GET` | `/v3/exercise/figures` | Figure requirement and status | `name` |

### Coming soon

These surfaces are on the public roadmap. They are **not** in the current OpenAPI spec and are not callable as a supported partner API yet.

| Surface | Intent |
|---|---|
| **Partner Bot API** | Authenticated specialist MoPro AI bots, org workspaces and conversation history |
| **Official SDKs** | First-party Python, JavaScript/TypeScript and Swift clients. Until they ship, generate a client from the [OpenAPI spec](https://api.mopro-codex.com/v3/developer/openapi.json). |
| **Embeddable widgets** | Partner widgets (reference cards, graph dashboards, variation trees). Not part of the current beta surface. |

## Quick start

1. Check the service (no key):

```bash
curl https://api.mopro-codex.com/v3/ontology/health
```

2. Create a key at [Get an API key](https://api.mopro-codex.com/v3/developer/get-key), then search:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  "https://api.mopro-codex.com/v3/exercise/search?q=deadlift"
```

```python
import requests

BASE = "https://api.mopro-codex.com"
HEADERS = {"Authorization": "Bearer YOUR_API_KEY"}  # placeholder

r = requests.get(f"{BASE}/v3/exercise/search", params={"q": "deadlift"}, headers=HEADERS)
r.raise_for_status()
print(r.json())
```

```javascript
const BASE = "https://api.mopro-codex.com";
const res = await fetch(`${BASE}/v3/exercise/search?q=deadlift`, {
  headers: { Authorization: "Bearer YOUR_API_KEY" }, // placeholder
});
if (!res.ok) throw new Error(await res.text());
console.log(await res.json());
```

3. Load a profile with the canonical name returned by search:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  "https://api.mopro-codex.com/v3/exercise/profile?name=Back%20Squat"
```

Worked examples: [quickstart](https://api.mopro-codex.com/v3/developer/quickstart) · [examples](https://api.mopro-codex.com/v3/developer/examples) · [API tester](https://api.mopro-codex.com/v3/developer/api-tester).

## Public roadmap

| Now (private developer beta) | Coming soon |
|---|---|
| Exercise Core API v3 — the endpoints above | Authenticated partner Bot API |
| Free Sandbox keys and commercial licences arranged with our team | Official Python, JS/TS and Swift SDKs |
| Developer portal, OpenAPI spec, reference and quickstart | Embeddable partner widgets |
| | Self-serve checkout for API plans; clinic and institution workflows |

When a new surface becomes callable it will be added to the OpenAPI spec and the [changelog](https://api.mopro-codex.com/v3/developer/changelog). We do not publish speculative launch dates.

## Licensing

Research and teaching licences and commercial API licences are available. Contact support@trainerseye.com or see [pricing](https://api.mopro-codex.com/v3/developer/pricing).

<sub>Docs in this repo are MIT licensed. The API and its data are © TrainersEye Technologies LLC and governed by the API terms. Outputs are educational decision-support, not a medical diagnosis.</sub>
