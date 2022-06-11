---
layout: home
title: CLAWS
subtitle: Meeting - June 13th
nav-short: true
---

## Nutrients module

Reproduce the work presented in the PARTRAC report (uses MIKE 21).
&nbsp;
<h5>ECE & NEI ESTIMATIONS</h5>  
&#9989; Add Loch's area, _A_, volume, _V_, and tidal range to `Loch` (Edwards & Sharples)  
&#9989; Estimate flushing rate and flushing time, Tf  
&#9989; Derive ECE (any units) and NEI  
&#9634; Understand S: the rate at which nutrient nitrogen is discharged  

<p align="center" style="border-style:solid; border:2; border-color:black">
<img src="/docs/meeting/excel-approx-formulas.png" style="height:300px">
</p>

Loch Long:  
```python
class LochLong(Loch):
    def __init__(self):
        super().__init__(area=44.0, tidal_range=3.1, volume=1758.0,
                         mean_depth=40.0,
                         reference="Gillibrand, P.A., Gubbins, G.J. (2002)")
```

Results from test_ECE.py  
```
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

<p align="center" style="border-style:solid; border:2; border-color:black">
<img src="/docs/meeting/bathymetry-data.png" style="height:300px">
</p>

  - <b>Scotland area not available in OpenDrift</b>
  - The UKHO offer survey data from UKHO marine data portal under open licence.  This tends to cover inshore areas of the UK, and includes data surveyed by Marine Scotland.
  - <strike>OceanWise offer a 1 arcsecond digital elevation model under commercial licence. This covers UK waters.  Other suppliers might be available.</strike>  
  - EMODnet offer a 1/16 arcminute (3.75 arcseconds) digital elevation model for free, <b>under certain licence conditions</b>.  This data covers the NE Atlantic region.  They also have an interactive map which can display contours.
  - GEBCO offer a 1/4 arcminute (15 arcseconds) digital elevation model for free, <b>under certain licence conditions</b>.  This data covers the globe.
  
Ref: <a href="http://marine.gov.scot/taxonomy/term/17/">http://marine.gov.scot/taxonomy/term/17/</a>

&nbsp;
<h5>SHORELINES</h5>
 - "derived from Satellite EMODnet shoreline data, relative to MSL."


&#9634; Download EMODnet bathymetry and shoreline data  
&#9634; Import bathymetry data (should we have done this in the sea lice report!?)  
&#9634; Calculate the Loch's area, A', and volume, V', from the bathymetry & coastline data (optional?)  

&nbsp;
<h5>OPENDRIFT</h5>
&#9634; Run an Opendrift simulation grid  
  * evenly-spaced particles (what depth?) - not 1 per cell (test multiple spacings: 5 m, 10 m)
  * seeds on land aren't moved back into the sea:
    ```sh
    o.set_config('seed:ocean_only', False)
    ```
    <div style="line-height:50%;">
        <br>
    </div> 
  * flushing time Tf': when 67% are gone
  
&#9634; Repeat simulation for different tide conditions (10 times), find best fit for nparticles vs. time, get averaged Tf'   
&#9634; Derive ECE and NEI from A', V' and Tf'  


