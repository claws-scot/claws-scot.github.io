---
layout: home
title: CLAWS
subtitle: Meeting - June 13th
nav-short: true
---

## Nutrients module

Reproduce the work presented the PARTRAC report (uses MIKE 21).
&nbsp;
<h5>ECE & NEI ESTIMATIONS</h5>  
&#9989; Add Loch's area, A, volume, V, and tidal range data to `Loch` (Edwards & Sharples)  
&#9989; Estimate flushing rate and flushing time, Tf  
&#9989; Derive ECE (any units) and NEI  
- Understand S: the rate at which nutrient nitrogen is discharged  

<p align="center">
<img src="/docs/meeting/excel-approx-formulas.png" style="height:300px">
</p>

Results from test_ECE.py
```python
Existing biomass:
Salmon Farm's ECE (kg/m^3) = 3.595962e-07
Salmon Farm's ECE (ug/L) = 0.359596
Salmon Farm's ECE (umol/L) = 0.025685
Salmon Farm's nutrient enhancement index = 1

Released biomass - Option 1:
Salmon Farm's ECE (kg/m^3) = 1.438385e-06
Salmon Farm's ECE (ug/L) = 1.438385
Salmon Farm's ECE (umol/L) = 0.102742
Salmon Farm's nutrient enhancement index = 1
```

&nbsp;
<h5>BATHYMETRY</h5>
- Import bathymetry data (we should have done this in the sea lice report!)
  * 3 links in the report not useful
  * is there a reader in OpenDrift that could be loaded for this area?

<p align="center">
<img src="/docs/meeting/bathymetry-data.png" style="height:300px">
</p>

- Calculate the Loch's area, A', and volume, V', from the bathymetry data

&nbsp;
<h5>OpenDrift</h5>
- Run an Opendrift simulation grid
  * particles are evenly-spaced (what depth?)
  * particles seeded on land aren't moved back into the sea:
    ```sh
    o.set_config('seed:ocean_only', False) # Do not reseed elements on land in the ocean
    ```
    <div style="line-height:50%;">
        <br>
    </div> 
  * flushing time Tf': when 67% are gone
  
- Repeat seimulation 10 times for different tide conditions ebb, flow, ...

- Derive ECE and NEI from A', V' and Tf'


