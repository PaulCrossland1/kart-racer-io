# Kartracer.io V1 — Frontend UX Wireframe Spec

## 1. Primary navigation

Top-level tabs:

1. Garage
2. Official Race
3. Leaderboard
4. Under the Hood

## 2. Entry / identity modal

If no kart JWT:

- “Build your kart. Evolve it in test runs. Submit one official lap every 24 hours.”
- Button: `Generate Kart Key`
- Input + button: `Enter Existing Kart Key`
- Link: `How to save your key`

## 3. Garage layout

### Left column

- Track viewport (major visual area)
- Race overlays:
  - lap progress
  - leader indicator
  - current condition badge
  - wear badge

### Right column

- 8 setup sliders with semantic min/max labels
- Current setup snapshot card
- Condition panel
- Wear panel with band status
- Primary CTA: `Run Test Turn`
- Secondary: `Open Telemetry`

## 4. Post-test state

Persistent summary card after each run:

- Winner time
- Previous base time
- Delta (+/-)
- “Fastest variant adopted as new base setup.”
- Button: `Run Test Turn Again`
- Button: `View Full Telemetry`

## 5. Official Race tab

- Current official condition block
- Cooldown status and exact next unlock timestamp
- Current setup preview
- Last official result summary
- Primary CTA: `Submit Official Run`
- Confirmation text: “Submission is final for 24 hours.”

## 6. Leaderboard view

- Default: current week track leaderboard
- Columns: rank, name, lap time, condition label, submitted at
- Highlight current player row
- For own entries: action buttons for `Share Time Card` and `Share GIF`

## 7. Under the Hood page

Sections:

- Why setup over steering
- How tracks and conditions rotate
- How fairness is enforced
- How telemetry guides improvement

## 8. UI style guide (V1)

- Dark theme base with high-contrast timing accents.
- Minimal but premium typography.
- Dense information, low clutter.
- Motion used for race readability, not decorative overload.
