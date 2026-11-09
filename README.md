# TEG Energy Harvesting with P&O MPPT

A simulation of a thermoelectric generator (TEG) array feeding a DC-DC boost converter, with a Perturb & Observe (P&O) maximum power point tracking (MPPT) controller. Built in MATLAB/Simulink with Simscape Electrical.

**Status:** Simulation complete. Hardware prototype planned.

## Overview

| Item | Value |
|---|---|
| Source | 40x TEC1-12706 thermoelectric modules, two series groups |
| TEG open-circuit voltage / internal resistance | 40.64 V / 8 ohm |
| Theoretical maximum power | 51.61 W at 20.32 V |
| Converter | DC-DC boost converter, 20 kHz, IRFZ44N MOSFET |
| MPPT algorithm | Perturb & Observe |
| Temperatures | 500 hot side, 300 cold side [state the unit, K or °C] |
| Load sweep | 5 to 700 ohm |
| Tools | MATLAB, Simulink, Simscape Electrical |

## Why MPPT

A TEG behaves like a voltage source with an internal resistance. It delivers maximum power only when the load it sees equals that internal resistance. A boost converter lets the controller change the load the TEG sees by adjusting the duty cycle, and the P&O algorithm finds the duty cycle that keeps the TEG at its maximum power point (MPP).

## System

```
TEG group 1 + TEG group 2 (series) -> boost converter (20 kHz, IRFZ44N) -> load (5 to 700 ohm)
              ^                                       |
              |        voltage and current            |
              +------ P&O MPPT controller <-----------+  (sets the duty cycle)
```

![Simulink model](docs/TEG_MPPT_Simulink_Model.png)

The model shows the operating point at a 100 ohm load: input power 51.61 W, output power 50.44 W, duty cycle 0.729 and efficiency 97.7%.

## What I did

- Designed and modeled the 40-module TEG array and the boost converter.
- Implemented the P&O MPPT controller.
- Debugged and tuned the controller:
  - fixed a conflict between the Unit Delay block and the controller state,
  - corrected the perturbation direction for the boost topology,
  - tuned the deadband and the step size so that tracking is stable.
- Analyzed loss mechanisms in the converter: conduction losses, diode reverse recovery and inductor core losses.
- Validated the simulation against theory and against a fixed-duty-cycle baseline without MPPT, across a 5 to 700 ohm load sweep.

## Results

| Metric | Result |
|---|---|
| Average converter efficiency, 5 to 700 ohm sweep | 97.2% |
| Peak converter efficiency | 97.9% at 350 ohm |
| Efficiency at 100 ohm | 97.7% |
| Power extracted from the TEG vs theoretical 51.61 W, loads of 10 ohm and above | above 99.9% |
| Peak output power | 50.53 W at 350 ohm |

Converter efficiency is output power divided by the power drawn from the TEG. MPPT quality is the power drawn from the TEG divided by the theoretical maximum of 51.61 W.

![Power, current and voltage against load](docs/TEG_MPPT_Power_Current_Voltage.jpg)

![Theoretical against simulated MPPT](docs/TEG_Theoretical_vs_Simulated_MPPT.jpg)

### Notes and limits

- **Low loads (5 ohm).** The duty cycle is limited to a minimum (0.15 in the theoretical model), so the converter cannot reach the MPP at 5 ohm. The TEG delivers 46.96 W there, about 91% of the maximum, and efficiency drops to 94.5%.
- **High loads (above 450 ohm).** The duty cycle approaches its limit and efficiency falls to 95.3% at 700 ohm.
- **Theoretical curve at 5 ohm.** The theoretical output power in the second plot exceeds the 51.61 W the TEG can supply at 5 ohm. This is an artifact of the clamped duty cycle in the theory curve, so the plotted 61.2% error at that point reflects the theory model, not the converter.

## Repository structure

```
.
├── README.md
├── simulation/   # Simulink model (.slx) and MATLAB plotting script
└── docs/         # images used in this README
```

## Next steps

1. Build a hardware prototype of the boost converter and MPPT controller.
2. Test it with a real TEG module and compare measured results with the simulation.
3. Add the measured results here.

## Tools

MATLAB, Simulink, Simscape Electrical.

## Author

Prince Sackey, Electrical and Electronics Engineering, Istanbul University-Cerrahpaşa.
