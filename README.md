VSC-HVDC for Renewable Energy Integration Under Varying Grid Strength
MATLAB/Simulink research model for the master's thesis Technical and Stability Assessment of VSC-HVDC for Renewable Energy Integration Under Varying Grid Strength Conditions.
Author: Elvin Gahramanov  
Institutions: ADA University and The George Washington University  
Year: 2026
Overview
This project investigates a 300 MW, point-to-point, grid-following VSC-HVDC transmission system under different receiving-grid strengths and operating conditions. Grid strength is represented by the short-circuit ratio (SCR).
The study uses one system configuration to compare rated operation, a receiving-side three-phase fault, coordinated power-reference changes, and two fixed receiving-grid source-voltage settings. Power responses, receiving-end PCC voltage and current, and DC-link voltage are assessed together.
The model is adapted from the MathWorks VSC-HVDC example. Renewable generation is represented by an equivalent sending-side AC source. Individual wind turbines, photovoltaic units, and their internal controls are outside the model scope.
Software and configuration status
Required products are MATLAB, Simulink, Simscape, and Simscape Electrical. The available `.slx` model was saved in MATLAB R2025b. Compatibility with other releases has not been verified.
The available model snapshot and the final thesis configuration are not identical. For example, the saved receiving-grid block uses an X/R ratio of 15, whereas the thesis specifies 10. The accompanying parameter script also contains earlier transformer settings. The tables below describe the final thesis configuration and reported results; they are not a claim that the saved default model reproduces every result without configuration.
Use the case settings and parameter calculations in Chapter 3 and Appendix A of the thesis when preparing a reproducible model release. No new MATLAB simulations were executed while preparing this README.
System and control
Parameter	Thesis configuration
Rated sending-end active power	300 MW
Three-phase system power base	300 MVA
Nominal grid-side line-to-line RMS voltage	345 kV
Nominal converter-side line-to-line RMS voltage	150 kV
Nominal pole-to-pole DC voltage	300 kV
DC transmission-link length	300 km
Nominal frequency	60 Hz
Converter transformers	400 MVA, 345/150 kV
Converter representation	Medium-fidelity average-value model
Receiving-grid representation	Thevenin-equivalent source behind a series impedance
Receiving-grid X/R ratio and source phase reference	10 and 0°, held constant
Nominal model and control sample time	Approximately 66.667 µs
Simulation interval	0–5 s
Final assessment window	4–5 s
The sending-end converter regulates active and reactive power. The receiving-end converter regulates DC-link voltage and reactive power. Both stations use phase-locked-loop synchronization and grid-following control.
The thesis changes the inner d-axis and q-axis current PI-controller output-saturation coefficient from 10 to 40 at both stations. This expands the controller output range; it does not specify a converter-current rating. After this modification, controller settings remain fixed across the comparative cases.
The thesis also includes a DC-link power-law varistor. Its transition voltages of 330 kV and 450 kV and exponent of 30 are simulation assumptions, not manufacturer-validated arrester ratings. Surge-protection capability is not evaluated.
Project files
Keep the model and required support scripts together or on the MATLAB path.
File	Purpose
`VoltageSourceConverterHVDCTrans.slx`	Main Simulink model
`VoltageSourceConverterHVDCTransParameters.m`	Electrical parameters, base quantities, sample times, and variant definitions
`VoltageSourceConverterHVDCTransEnum.m`	Converter-fidelity enumeration
`VoltageSourceConverterHVDCTransPlotResults.m`	Plots logged power and receiving-end DC-voltage responses
`VoltageSourceConverterHVDCTransInitializeModel.m`	Optional initialization using a saved Simscape operating point
`VoltageSourceConverterHVDCTransVariantControl.m`	Optional fidelity and solver-configuration helper
`VoltageSourceConverterHVDCTransExample.m`	Original example walkthrough
The `.slxc` file is a generated simulation cache, not the editable model. The original example walkthrough and optional helpers do not constitute an automated implementation of the five thesis cases.
Running a simulation
Download the model and support scripts. Set MATLAB's Current Folder to their directory.
Open the model. Its saved `PreLoadFcn` runs the parameter script:
```matlab
   addpath(pwd);
   model = 'VoltageSourceConverterHVDCTrans';
   open_system(model);
   set_param(model, 'StopTime', '5');
   ```
Configure one case from the table below. Apply the thesis transformer and controller settings, set the receiving-grid short-circuit power, and verify the source voltage, X/R ratio, and phase reference. Enable the fault only for the fault case.
Keep the medium-fidelity average-value configuration and the same numerical settings across comparisons. Confirm the receiving-end measurement location on the 345 kV side.
Run the configured case:
```matlab
   sim(model);
   ```
Inspect the scopes and logged signals. With the original logging names retained, the plotting script can be called after the simulation:
```matlab
   VoltageSourceConverterHVDCTransPlotResults;
   ```
Export the PCC phase-voltage and phase-current time histories for the RMS assessment. Save the case configuration with each result, including SCR, source voltage, reference trajectories, fault settings, solver settings, and MATLAB release.
If parameters are edited while the model is already open, rerun the parameter script before configuring the case. It resets the variables it defines. For comparisons that include initialization transients, retain the same initialization procedure in every run; loading a precomputed operating point changes the starting condition.
Existing helper-script considerations
The supplied variant-control helper refers to `VoltageSourceConverterHVDCTransmissionEnum`, while the parameter script uses `VoltageSourceConverterHVDCTransEnum`. These identifiers must be consistent before that optional helper is used. The operating-point helper also contains sensor-path assumptions inherited from the original example; check them against the actual model wiring before using it.
Simulation cases
For rated operation, the fault case, and both source-voltage cases, the commands are:
Sending-end active power: 1.00 p.u., equivalent to 300 MW.
Sending-end reactive power: +0.20 p.u.
Receiving-end reactive power: −0.20 p.u.
Receiving-end DC voltage: 1.00 p.u.
Case	Operating condition	Receiving-grid SCR values
Rated operation	Nominal receiving-grid source voltage, rated power	1.396, 1.4, 1.5, 2.5, 5.0
Three-phase fault	Fault at the receiving-side 345 kV bus from 2.000 to 2.100 s; 0.04 Ω per phase	1.4, 1.5, 2.5, 5.0
Coordinated reference ramp	Sending P: 0.50 → 1.00 p.u.; sending Q: +0.10 → +0.20 p.u.; receiving Q: −0.10 → −0.20 p.u.; all over 2.0–2.5 s	1.4, 1.5, 2.5, 5.0
Reduced source voltage	Receiving-grid source fixed at 0.90 p.u. throughout the run	1.45, 1.5, 2.5, 5.0
Elevated source voltage	Receiving-grid source fixed at 1.05 p.u. throughout the run	1.4, 1.43, 1.5, 2.5, 5.0
The ramp rate is 300 MW/s. The receiving-end DC-voltage command stays at 1.00 p.u. during the ramp. The 0.90 and 1.05 p.u. cases are separate operating conditions applied from initialization, rather than voltage steps during operation.
Setting grid strength
The study defines:
$$
\mathrm{SCR}=\frac{S_{\mathrm{sc}}}{P_{\mathrm{rated}}},
\qquad P_{\mathrm{rated}}=300\ \mathrm{MW}.
$$
SCR	Receiving-grid short-circuit power
1.396	418.8 MVA
1.4	420 MVA
1.43	429 MVA
1.45	435 MVA
1.5	450 MVA
2.5	750 MVA
5.0	1500 MVA
Keep the nominal voltage base fixed when changing the source-voltage setting. Set the equivalent grid impedance consistently with the selected SCR and fixed X/R ratio; changing the source voltage should not unintentionally change the intended impedance configuration.
Measurements and assessment
Measured quantities include sending-end active power, sending- and receiving-end reactive power, receiving-end PCC phase voltage and current, and receiving-end DC voltage. Receiving-end active power and sending-end DC voltage provide additional operating information.
In the final thesis, AC results are taken from the first phase-to-ground voltage and corresponding phase current at the 345 kV receiving-end PCC, identified as `vi2` in the revised model. Confirm the physical location from the wiring: sensor labels and paths differ between the original example and revised versions.
The AC bases are:
$$
V_{\mathrm{ph},b}=\frac{345\ \mathrm{kV}}{\sqrt{3}},
\qquad
I_b=\frac{300\ \mathrm{MVA}}{\sqrt{3}\times345\ \mathrm{kV}}.
$$
A trailing one-cycle RMS window is used at 60 Hz, with a duration of approximately 16.67 ms. For an instantaneous waveform exported on the nominal peak base, the normalized RMS magnitude is:
$$
M_x(t)=\sqrt{\frac{2}{T}\int_{t-T}^{t}x^2(\tau),d\tau},
\qquad T=\frac{1}{60}\ \mathrm{s}.
$$
This factor of 2 inside the square root applies to peak-base per-unit inputs. Physical-unit recordings must instead be divided by the corresponding RMS base after calculating their RMS. Do not normalize an already normalized RMS signal a second time.
Assessment	Application
Observed settling and reference tracking	Examine time histories for decay or persistence of variations. Compare controlled powers and receiving-end DC voltage with their own commands.
Rated-operation comparison	Compare final AC RMS means with a settled rated-operation baseline at the same SCR. Quantify recovery or a changed operating level.
PCC voltage benchmark	Compare final PCC voltage means with the selected EU HVDC voltage bands separately from the settling classification.
Settled means transient variations decay and the operating magnitude remains approximately constant over 4–5 s at the resolution of the recorded responses. Not settled means continuing oscillation, drift, or magnitude variation remains observable. A case is classified as settled only when all assessed quantities settle. A settled pre-fault condition is required for an independent post-fault recovery assessment.
Report the 4–5 s RMS mean for settled responses and the RMS minimum-to-maximum range for continuing variations. Instantaneous extrema describe transient excursions: use 0–5 s for initialization cases and 2–5 s for fault and ramp cases.
For settled cases with a matching baseline:
$$
\Delta M_V=\overline{M}{V,\mathrm{case}}-\overline{M}{V,\mathrm{rated}},
\qquad
\Delta M_I=\overline{M}{I,\mathrm{case}}-\overline{M}{I,\mathrm{rated}}.
$$
Baseline differences describe operating levels; they are not settling thresholds. No baseline is interpolated for an untested SCR. The thesis assigns no numerical settling tolerance or settling time.
Results reported in the thesis
Operating case	Lowest tested SCR with all assessed responses settled
Rated operation	1.4
Three-phase fault	1.4
Coordinated reference ramp	1.4
Source voltage of 0.90 p.u.	1.45
Source voltage of 1.05 p.u.	1.43
SCR = 1.5 was the lowest common tested value with settled responses across all five cases. These are sampled outcomes for the investigated configuration, not universal stability limits.
At SCR = 2.5, the reported final-window means were:
Receiving-grid source voltage	PCC voltage RMS mean	Phase-current RMS mean
0.90 p.u.	0.9943 p.u.	0.9352 p.u.
1.00 p.u.	1.0941 p.u.	0.8433 p.u.
1.05 p.u.	1.1445 p.u.	0.8045 p.u.
The sending-end active-power command remained 300 MW. Post-fault and post-ramp AC RMS means agreed with their matching rated-operation baselines within 0.0003 and 0.0001 p.u., respectively, at the reported precision.
Scope and limitations
Average-value converters represent the main AC/DC and control dynamics; individual switching events and switching harmonics are not resolved explicitly.
Renewable-generation controls and wider-network dynamics are represented by equivalents.
Controller settings, receiving-grid X/R ratio, and source phase reference remain fixed during the comparisons.
Qualitative classifications cover the 5 s observation interval and sampled SCR values. Longer runs may reveal slower dynamics.
The EU HVDC Network Code is used as a technical benchmark. A final-window voltage mean does not establish compliance with a complete voltage trajectory, required duration, or fault-ride-through profile.
Equipment withstand, protection performance, rapid-voltage-change compliance, flicker, harmonic performance, and DC-link current are outside the reported assessment.
Future work includes X/R-ratio and phase-angle sensitivity, longer simulations, finer SCR sampling, and validation with more detailed converter models.
Attribution and citation
If this work supports your research, cite the thesis:
> E. Gahramanov, “Technical and Stability Assessment of VSC-HVDC for Renewable Energy Integration Under Varying Grid Strength Conditions,” master's thesis, ADA University and The George Washington University, 2026.
Relevant sources already used in the thesis include:
[46] MathWorks, “High-Voltage, Direct-Current Transmission Using Voltage Source Converters.” Original model and supporting example scripts.
[52] European Commission, Commission Regulation (EU) 2016/1447. Technical context for connection-point voltage, reactive-power control, and fault-response interpretation.
[53] MathWorks, “Varistor—Voltage-dependent resistor.” Block description; the numerical settings used in this study remain simulation assumptions.
Reference numbers follow the thesis bibliography. Original MathWorks copyright notices and applicable terms remain associated with the source model and scripts. This README does not assign a new software license to those materials or to the research modifications.
