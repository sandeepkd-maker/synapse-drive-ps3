def get_gear_ratio(x: float,
                   v: float,
                   slope: float,
                   mu: float,
                   track_info: dict) -> float:
    """
    Terrain-aware, energy-efficient gear selection controller.
    """

    # -------------------------------
    # Tunable parameters (safe values)
    # -------------------------------
    MAX_GEAR = 5.0
    MIN_GEAR = 0.0

    # Target speeds (m/s)
    TARGET_SPEED_FLAT = 6.0
    TARGET_SPEED_UPHILL = 4.0
    TARGET_SPEED_DOWNHILL = 7.5

    # Minimum gear to avoid stall on uphill
    MIN_UPHILL_GEAR = 1.2

    # -------------------------------
    # 1. Downhill: Coast to save energy
    # -------------------------------
    if slope < -0.05:
        # Only push if speed is too low
        if v < 2.0:
            return 0.8
        else:
            return 0.0  # pure coasting (zero energy)

    # -------------------------------
    # 2. Flat terrain
    # -------------------------------
    if abs(slope) <= 0.05:
        if v < TARGET_SPEED_FLAT:
            gear = 1.5
        else:
            gear = 0.0  # glide to save energy

        # Reduce torque on low-friction surfaces
        gear *= min(1.0, mu / 0.7)

        return max(MIN_GEAR, min(MAX_GEAR, gear))

    # -------------------------------
    # 3. Uphill terrain
    # -------------------------------
    if slope > 0.05:
        # Base gear increases with slope
        gear = 1.5 + 5.0 * slope

        # If speed is very low, increase torque to avoid stall
        if v < 1.0:
            gear += 1.0

        # Enforce minimum gear to prevent stalling
        gear = max(gear, MIN_UPHILL_GEAR)

        # Limit torque on low-friction surfaces to reduce slip
        friction_scale = min(1.0, mu / 0.6)
        gear *= friction_scale

        return max(MIN_GEAR, min(MAX_GEAR, gear))

    # -------------------------------
    # Fallback (should not be reached)
    # -------------------------------
    return 1.0
