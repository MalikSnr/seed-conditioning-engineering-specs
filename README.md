# Industrial Seed Conditioning: Sieve Sizing, Airflow & Optical Sorting Standards

This repository provides open-source engineering reference data, screen perforation matrices, aerodynamic terminal velocity baselines, and throughput calculation formulas for commercial grain and seed processing plants (5–100 t/h intake lines).

---

## 1. Scalping & Air-Screen Perforation Standards

Primary intake efficiency depends on matching screen open area and aspiration velocity to crop bulk density ($\rho_b$) and kernel geometry. Undersized screen areas lead to grain carryover, while excessive vibration frequency reduces residence time inside the perforation zone.

When sizing a primary [pre-cleaner](https://seedprocessingmachines.com/en/machinery/pre-cleaner/) for high-moisture or high-impurity harvest intakes, sieve perforation geometry follows these baseline tolerances:

| Crop Type | Bulk Density (kg/m³) | Top Scalping Round (mm) | Bottom Sand Slotted (mm) | Airflow Velocity (m/s) |
| :--- | :--- | :--- | :--- | :--- |
| Wheat (Triticum) | 750 – 820 | 6.5 – 8.0 | 1.8 x 20 | 4.5 – 6.2 |
| Barley (Hordeum) | 620 – 680 | 7.0 – 9.0 | 2.0 x 20 | 4.0 – 5.5 |
| Corn / Maize | 720 – 760 | 12.0 – 14.0 | 4.5 x 20 | 6.5 – 8.5 |
| Sunflower | 400 – 440 | 9.0 – 11.0 | 2.2 x 20 | 3.2 – 4.4 |
| Soybean | 700 – 750 | 9.5 – 11.0 | 3.5 x 20 | 5.8 – 7.5 |

### Effective Screen Load Formula
Specific screen load ($q_s$) in metric tons per hour per square meter ($\text{t/h}\cdot\text{m}^2$) is calculated as:

$$q_s = \frac{Q \cdot (1 + \frac{I_{in}}{100})}{A_{eff} \cdot k_m}$$

* $Q$: Nominal line throughput ($\text{t/h}$)
* $I_{in}$: Initial foreign material percentage ($\%$)
* $A_{eff}$: Total effective perforated screen area ($\text{m}^2$)
* $k_m$: Moisture correction factor ($1.0$ for $\le 14\%$ moisture; $0.78$ for $18\%$ moisture)

---

## 2. Specific Gravity Separation & Deck Fluidization

Once dimensional calibration is completed by air-screen cleaners and indented cylinders, seeds of identical size but differing viability (e.g., insect-damaged, partially germinated, or frost-blighted kernels) must be stratified by specific density.

Configuring an industrial [gravity separator](https://seedprocessingmachines.com/en/machinery/gravity-separator/) requires balancing three kinematic parameters across the fluidized deck:

1. **Plenum Static Pressure:** Air velocity through the woven wire deck must equal the minimum fluidization velocity ($U_{mf}$) of the light fraction without lifting heavy viable seeds off the friction surface.
2. **Longitudinal & Transverse Slope:** Longitudinal inclination ($2^\circ - 5^\circ$) governs uphill discharge speed of high-density seeds, while transverse tilt ($1^\circ - 3^\circ$) controls lateral stratification toward the light reject outlets.
3. **Eccentric Stroke Frequency:** High-density cereal grains require higher oscillation frequencies (450–520 RPM) compared to light oilseeds or pulses (380–430 RPM).

---

## 3. Optical Ejection & Pneumatic Consumption Model

Final purity upgrading relies on high-speed trichromatic and NIR inspection. When integrating an optical [color sorter](https://seedprocessingmachines.com/en/machinery/color-sorter/) at the end of a conditioning line, compressed air supply stability directly impacts the Good-to-Bad (G:B) carryover ratio.

| Chute Channels | Nominal Capacity (t/h) | Required Air Pressure (MPa) | Peak Air Consumption (m³/min) | Filtration Standard |
| :--- | :--- | :--- | :--- | :--- |
| 64 Channels | 1.5 – 3.0 | 0.6 – 0.8 | 0.8 – 1.2 | ISO 8573-1 (1.2.1) |
| 128 Channels | 3.0 – 6.0 | 0.6 – 0.8 | 1.6 – 2.2 | ISO 8573-1 (1.2.1) |
| 192 Channels | 5.0 – 9.0 | 0.6 – 0.8 | 2.4 – 3.5 | ISO 8573-1 (1.2.1) |
| 256 Channels | 8.0 – 12.0 | 0.6 – 0.8 | 3.2 – 4.8 | ISO 8573-1 (1.2.1) |

---

## 4. Reference Python Utility: Sieve Area & Capacity Estimator

Engineers can use the following script to calculate the required screen surface area ($m^2$) based on crop density, moisture level, and target intake capacity:

```python
def calculate_required_screen_area(capacity_th, crop_type, moisture_pct, impurity_pct):
    """
    Calculates minimum required effective sieve area (m2) for seed cleaning lines.
    """
    base_load_factors = {
        "wheat": 6.5,      # t/h per m2 at standard conditions
        "barley": 5.5,
        "corn": 6.0,
        "sunflower": 3.2,
        "soybean": 5.8
    }
    
    specific_load = base_load_factors.get(crop_type.lower(), 5.0)
    
    # Moisture penalty above 14%
    moisture_factor = 1.0 if moisture_pct <= 14.0 else max(0.6, 1.0 - ((moisture_pct - 14.0) * 0.045))
    
    # Impurity load adjustment
    effective_throughput = capacity_th * (1.0 + (impurity_pct / 100.0))
    
    required_area = effective_throughput / (specific_load * moisture_factor)
    return round(required_area, 2)

if __name__ == "__main__":
    area = calculate_required_screen_area(capacity_th=50, crop_type="wheat", moisture_pct=15.5, impurity_pct=4.0)
    print(f"Minimum Required Screen Surface Area: {area} m²")
