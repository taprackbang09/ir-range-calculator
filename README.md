# IR Range Calculator

Single-page tool that estimates how far a thermal camera can see Geran/Shahed-family drones, and shows what the target looks like on the sensor at any distance and viewpoint.

Open `index.html` in a browser. No build step, no dependencies (Google Fonts only, with system fallbacks).

## What it does

- **Maximum range matrix** – farthest range per drone × lens where the chosen criterion holds:
  `max range = min(R_px, R_snr)`.
- **Sensor view at range** – simulated 640×512 LWIR frame plus pixel-exact zoom, from 0.5 px to a full-screen target.
- **Viewpoint × distance matrix** – the same drone at 1 / 2 / 4 … 128 px and full screen, for each engagement aspect.

Default camera: Infisense Mini2 640, 12 µm pitch, lenses 4.1 / 9.1 / 13 / 18 mm (8 µm / 6 mm option).
Engagement aspects: Rear (6 o'clock), Side (3/9 o'clock), Top-down (planform), High-side (30–45°, modelled at 40°).

## Formulas

Angle seen by one pixel:

    IFOV = p / f                         (12 µm / 9.1 mm = 1.319 mrad)

Pixel footprint on the target and pixels across a target of size D:

    GSD = R × IFOV
    n   = D / (R × IFOV)

Pixel-limited maximum range for N pixels:

    R_max = D / (N × IFOV) = f × D / (N × p)
    R_max [km] = f [mm] × D [m] / (N × p [µm])

Worked example: Geran fuselage length 3.5 m, 9.1 mm lens, 12 µm pitch, N = 1:

    R_max = 9.1 × 3.5 / (1 × 12) = 2.65 km

Sub-pixel (contrast) limit. Beyond the 1-pixel range the target fills a fraction φ of one pixel:

    φ     = A / (R × IFOV)²
    SNR   = φ × τ(R) × ΔL / NEΔL,      τ(R) = e^(−βR),   NEΔL = NETD × dL/dT
    R_snr = (1 / IFOV) × √(A × τ × ΔL / (SNR_min × NEΔL))

## Assumptions

- **One pixel is enough to see the drone** (default N = 1 on √A, preset "Seen 1"); Johnson D / R / I presets are 2 / 8 / 12.8 px.
- Target size D is measured on a simplified 3D model at the chosen aspect: critical dimension √(projected area) or largest projected extent.
- Airframe dimensions come from the reference data in the page (HUR / OSINT); verify against primary sources before citing.
- Skin temperature = ambient + adiabatic recovery heating at an assumed cruise speed; piston cowling ≈ 80–120 °C, exhaust stubs ≈ 340 °C; turbojet nozzle interior ≈ 520 °C (rear hemisphere only); plume optically thin in LWIR.
- Background: clear night sky (−3 K at horizon to −43 K at zenith vs ambient), ground ambient +1 K; no sun, clouds or terrain clutter.
- Sensor: 8–14 µm Planck radiance, supersampled pixel integration, diffraction blur σ ≈ 0.45·λ·F#/p (λ = 10 µm), white NETD noise, percentile AGC.
- Orthographic projection; no turbulence, motion blur, fixed-pattern noise or NUC artefacts.

Results are estimates for research and planning, not measured signatures.
