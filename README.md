# Radiator-X - RADIUS Orchestrator for MFA

Radiator is a clean-room, open-core starter for building a **RADIUS AAA proxy with MFA orchestration**.
It uses **FreeRADIUS** for the RADIUS plane and a **FastAPI** service for policies, MFA (TOTP now, Push later),
and integrations (AD/LDAP/SQL). This repository is production-leaning but intentionally minimal.

> ⚠️ This codebase is a **starter**: safe defaults, docs, and a working scaffold.
---

## Features (Starter)
- FreeRADIUS 3.x with a REST auth pipeline to API
- FastAPI service (`/api`) with health/version, basic TOTP utilities, and a REST endpoint for RADIUS auth
- PostgreSQL for state, Redis for cache/nonce
- Docker Compose for local runs
- Ansible skeleton for bare-metal/VM installs (Debian)
- Docs/recipes for FortiGate IKEv2 + MFA

## Quickstart (Docker)

```bash
cp .env.example .env
docker compose -f deploy/docker-compose.yml up -d --build
```

Check API:
```bash
curl -s http://localhost:8000/health
```

FreeRADIUS runs inside the `radius` container. Logs:
```bash
docker compose -f deploy/docker-compose.yml logs -f radius
```

### Test (PAP demo)
In demo mode, the API accepts the user `test` with password `testpass`.

From the `radius` container shell:
```bash
docker compose -f deploy/docker-compose.yml exec radius bash
radtest test testpass api 0 testing123
```

Expected (for demo): `Access-Accept`.

> Replace `api` by the IP/hostname of the API service if needed.

---

## Project Layout
```
api/            FastAPI service (policies, MFA, REST auth)
radius/         FreeRADIUS config (sites, mods, policies, clients)
deploy/         Docker Compose + Ansible skeleton
docs/           Recipes, Security, RGPD notes
tests/          Basic tests
```

## Security
- Default TLS between components is not enforced in this starter; add sidecar TLS/mTLS for prod.
- Keep secrets out of the repo. Use `.env` or an external secret store.

## License
AGPL-3.0-only for the starter. Consider an open-core model for proprietary connectors.
