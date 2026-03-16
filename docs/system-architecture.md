# Kartracer.io V1 — System Architecture Spec

## 1. Architecture goals

- Keep official racing server-authoritative.
- Keep gameplay loop low-latency and visually immediate.
- Preserve deterministic simulation behavior for fairness and replayability.
- Separate concern boundaries so each subsystem can evolve independently.

## 2. High-level components

1. **Web Client (SPA or SSR app)**
   - Garage UI, race viewport, telemetry, official mode, leaderboard, under-the-hood page.
   - Stores kart JWT in browser local storage.
2. **API Service**
   - Kart identity bootstrapping/recovery.
   - Setup updates, test turn requests, official run submissions.
   - Cooldown and wear enforcement.
3. **Simulation Service / Module**
   - Deterministic race calculations from setup + track + condition + seed.
   - Returns lap time, telemetry, and replay frames.
4. **Leaderboard Service (can begin in API)**
   - Ranking queries by week/track/day.
   - Personal best and recent official runs.
5. **Asset Worker**
   - Generates time cards and GIF replay artifacts from official replay data.
6. **Datastore Layer**
   - PostgreSQL for authoritative records.
   - Redis for cooldown caches, condition snapshots, queue state.
   - Object storage (S3-compatible) for replay payloads, GIFs, and time cards.

## 3. Runtime request flow

### 3.1 First visit

1. Client checks local JWT.
2. If missing, call `POST /v1/karts/bootstrap`.
3. API issues `kart_key` + signed JWT.
4. Client stores JWT and loads garage state.

### 3.2 Test turn

1. Client sends current setup to `POST /v1/test-turns`.
2. API validates wear and current track/condition.
3. Simulation module computes base + 49 variants.
4. API chooses winner, increments wear, persists turn record.
5. API returns replay frames and telemetry summary.

### 3.3 Official run

1. Client calls `POST /v1/official-runs`.
2. API enforces 24h cooldown and validates kart state.
3. Simulation runs authoritative official lap.
4. API stores official result and rank snapshot.
5. API enqueues asset job (GIF + time card).
6. Client polls or subscribes to asset readiness endpoint.

## 4. Determinism and fairness

- Use seeded PRNG where seed = hash(track_id, condition_id, kart_key, run_type, server_nonce).
- Persist simulation inputs and engine version for auditability.
- Keep official runs entirely server-computed.
- Keep practice mode server-authoritative in V1 for behavior parity.

## 5. Security model

- JWT includes kart key and token version.
- kart key treated as bearer secret; never expose private internals.
- Rate-limit endpoints for bootstrap, test-turn, official-run, rename.
- Profanity filter on display names.
- Immutable official result records.

## 6. Deployment topology (recommended)

- Frontend: Vercel/Netlify or containerized static+SSR runtime.
- API + sim: containerized service (single deploy unit in V1).
- Worker: separate process consuming queue jobs.
- Postgres + Redis managed services.
- Object storage bucket with signed URL retrieval.

## 7. Observability

- Structured logs with request IDs and kart IDs (hashed in logs).
- Metrics:
  - test turn latency (p50/p95)
  - official submission latency
  - simulation failure rate
  - asset generation success and duration
- Tracing across API -> simulation -> storage write.

## 8. Versioning strategy

- Prefix all endpoints with `/v1`.
- Track simulation engine versions in every turn/run record.
- Use feature flags for progressive rollout of wear tuning and telemetry expansion.
