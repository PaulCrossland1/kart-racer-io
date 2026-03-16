# Kartracer.io V1 — Data Schema and API Contract

## 1. Core entities

### 1.1 karts

- `kart_key` (pk, text)
- `display_name` (text, nullable)
- `current_setup` (jsonb)
- `wear_count` (int, default 0)
- `last_official_submission_at` (timestamptz, nullable)
- `jwt_version` (int, default 1)
- `created_at` (timestamptz)
- `updated_at` (timestamptz)

### 1.2 tracks

- `track_id` (pk, text)
- `name` (text)
- `week_start` (date)
- `week_end` (date)
- `track_definition` (jsonb)
- `active_flag` (bool)

### 1.3 conditions

- `condition_id` (pk, text)
- `track_id` (fk -> tracks.track_id)
- `active_from` (timestamptz)
- `active_to` (timestamptz)
- `label` (text)
- `display_description` (text)
- `hidden_parameters` (jsonb)

### 1.4 test_turns

- `turn_id` (pk, uuid)
- `kart_key` (fk -> karts.kart_key)
- `track_id` (fk)
- `condition_id` (fk)
- `prior_setup` (jsonb)
- `winning_setup` (jsonb)
- `winning_time_ms` (int)
- `telemetry_summary` (jsonb)
- `wear_before` (int)
- `wear_after` (int)
- `simulation_version` (text)
- `replay_reference` (text)
- `created_at` (timestamptz)

### 1.5 official_runs

- `official_run_id` (pk, uuid)
- `kart_key` (fk)
- `track_id` (fk)
- `condition_id` (fk)
- `setup_snapshot` (jsonb)
- `lap_time_ms` (int)
- `leaderboard_rank_snapshot` (int)
- `simulation_version` (text)
- `replay_reference` (text)
- `gif_reference` (text, nullable)
- `time_card_reference` (text, nullable)
- `created_at` (timestamptz)

## 2. Setup JSON contract (V1)

```json
{
  "gearRatio": 0.5,
  "brakeBias": 0.5,
  "tirePressure": 0.5,
  "frontSuspensionStiffness": 0.5,
  "rearSuspensionStiffness": 0.5,
  "rideHeight": 0.5,
  "steeringResponse": 0.5,
  "weightDistribution": 0.5
}
```

- Normalized `0..1` sliders for storage simplicity.
- UI can render mapped labels and user-facing units.

## 3. API surface

### 3.1 Auth / kart identity

- `POST /v1/karts/bootstrap`
  - creates new kart key and JWT.
- `POST /v1/karts/recover`
  - accepts kart key, returns JWT if valid.
- `POST /v1/karts/rename`
  - updates display name with profanity checks.

### 3.2 Game state

- `GET /v1/game-state/current`
  - returns active weekly track + current daily condition + kart state.

### 3.3 Practice mode

- `POST /v1/test-turns`
  - request: setup snapshot
  - response: winner setup, winner time, telemetry summary, replay payload reference, updated wear.

### 3.4 Official mode

- `GET /v1/official-runs/availability`
  - returns cooldown status and next eligible timestamp.
- `POST /v1/official-runs`
  - submits official run.
- `GET /v1/official-runs/:id/assets`
  - returns asset generation status and URLs when ready.

### 3.5 Leaderboard

- `GET /v1/leaderboards/current-week`
- `GET /v1/leaderboards/track/:trackId`
- `GET /v1/karts/me/history`

## 4. Cooldown and wear rules

- Official cooldown: `created_at + 24h` rolling window.
- Wear increments by 1 per test turn.
- Wear bands:
  - `0-60`: normal
  - `61-85`: warning only
  - `86-100`: mild inconsistency penalty in practice mode
  - `>100`: block further test turns until repair action

## 5. Minimal SQL index recommendations

- `official_runs(track_id, lap_time_ms)`
- `official_runs(track_id, created_at)`
- `test_turns(kart_key, created_at desc)`
- `conditions(track_id, active_from, active_to)`
- `tracks(active_flag, week_start)`
