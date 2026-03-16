# Kartracer.io V1 — Implementation Plan

## Milestone 1: Platform bootstrap

- Initialize frontend and backend projects.
- Add JWT kart identity bootstrap/recovery.
- Add Postgres schema + migration pipeline.
- Add track/condition seed data loaders.

## Milestone 2: Core garage loop

- Implement slider model and setup persistence.
- Implement server-authoritative test turn endpoint.
- Render 50-kart replay in garage viewport.
- Persist test turn records and wear updates.

## Milestone 3: Official race + leaderboard

- Implement official cooldown enforcement.
- Persist official run records and ranking queries.
- Build leaderboard UI with current-week default.
- Add display-name editing with moderation filter.

## Milestone 4: Share artifacts

- Build worker for GIF and time-card generation.
- Add post-official result asset status + download UX.
- Store artifact references and signed URL delivery.

## Milestone 5: Polish + quality gates

- Tune telemetry clarity and trend views.
- Add observability dashboards and alerting.
- Load/perf testing for 50-kart visualization.
- Anti-abuse hardening and audit logging.
