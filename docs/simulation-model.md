# Kartracer.io V1 — Simulation Model Design

## 1. Objectives

- Deterministic output with bounded condition-driven variance.
- Intuitive slider impacts with meaningful interdependence.
- Replay-ready position samples for live rendering and GIF export.

## 2. Input model

Simulation input tuple:

- `track_definition`
- `condition_hidden_parameters`
- `setup_vector[8]`
- `run_type` (`practice` | `official`)
- `seed`

## 3. Latent performance components

Represent lap quality as weighted sub-scores:

- acceleration efficiency
- braking stability
- corner entry stability
- corner exit traction
- high-speed balance
- rough-surface compliance

Each sub-score is a function of multiple setup variables and condition modifiers.

## 4. Track-sector evaluation

1. Track contains sectors with profile weights (straight, technical, rough, flowing).
2. For each sector, compute expected sector time from latent sub-scores.
3. Aggregate sector times into lap time.
4. Add bounded consistency noise from deterministic seed stream.

## 5. Practice turn variant generation

- Generate 49 setup variants around base setup.
- Each variable independently perturbed with cap near ±0.02 normalized range.
- Clamp to `[0, 1]`.
- Evaluate base + variants.
- Select fastest; this setup becomes next base.

## 6. Wear integration

Practice-mode consistency modifier by wear band:

- `0-60`: no penalty
- `61-85`: no time penalty, warning surfaced in telemetry
- `86-100`: slight consistency variance increase
- `>100`: simulation request rejected until repaired

## 7. Replay generation format

For each run:

- fixed timestep sampling (e.g. 250 ms for live; downsample for GIF)
- per kart: position along spline, speed estimate, rank at sample
- metadata: lap time, sectors, winner flag, run label

Suggested payload:

```json
{
  "fps": 4,
  "durationMs": 28000,
  "samples": [
    {
      "t": 0,
      "karts": [{ "id": "base", "s": 0.0, "speed": 0.0, "rank": 1 }]
    }
  ]
}
```

## 8. Telemetry summary output

- winner lap time
- prior base lap time
- delta
- sector times and deltas
- top speed
- average speed
- corner stability score
- traction summary
- setup delta summary
- wear status

## 9. Versioning and calibration

- Every simulation result stamped with `simulation_version`.
- Calibration harness can replay historical setup/condition sets and compare distribution drift.
- Track/condition tuning should preserve “learnable but not solved” behavior.
