---
layout: home
title: CLAWS
subtitle: Meeting - June 13th
nav-short: true
---

## Nutrients module

- Reproduce the work presented in the PARTRAC report (uses MIKE 21).  
- Nutrient: Nitrogen (N), 2 sources
  + dissolved ammonia  
  + particulate waste emissions re-dissolving into the water column from the seabed   
  
&nbsp;
<h5>ECE & NEI ESTIMATIONS</h5>  
&#9989; Add Loch's area, _A_, volume, _V_, and tidal range, _R_, to `Loch` (Edwards & Sharples)  
&#9989; Estimate flushing rate and flushing time, _Tf_  
&#9989; Derive ECE and NEI (see Results from test_ECE.py below)  
&#9634; Understand why they used 2 values of S = 48.2 kgN/tonne-prod. (existing biomass column) and 40.68 kgN/tonne-prod. (options 1-3 columns).  
  - function of stocking, feeding and harvesting strategies employed during cultivation
  - amount of N released = input amount - amount incorporated into fish growth  

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
Salmon Farm's ECE (kg/m^3) = 1.212779e-06
Salmon Farm's ECE (ug/L) = 1.212779
Salmon Farm's ECE (umol/L) = 0.086627
Salmon Farm's nutrient enhancement index = 1
```

&nbsp;
<h5>BATHYMETRY</h5>

<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/bathymetry-data.png" style="height:300px">
</p>

  - <b>Scotland's bathymetry currently not available in OpenDrift</b>
  - The UKHO offer survey data from UKHO marine data portal under open licence.  This tends to cover inshore areas of the UK, and includes data surveyed by Marine Scotland.  
    + <a href="https://data.admiralty.co.uk/portal/apps/sites/#/marine-data-portal/pages/seabed-mapping-services">data.admiralty.co.uk seabed-mapping-services</a>  
    + <a href="https://seabed.admiralty.co.uk/?x=-19567.88&y=6780270.16&z=5.00">seabed.admiralty.co.uk/?x=-19567.88&y=6780270.16&z=5.00</a>  
    + file format for free download is set to ASCII or BAG  
    
<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/admiralty.png" style="height:300px">
</p>
    
  - <strike>OceanWise offer a 1 arcsecond digital elevation model under commercial licence. This covers UK waters.</strike>   
  - <b>EMODnet offer a 1/16 arcminute (3.75 arcseconds) digital elevation model for free, under certain licence conditions. This data covers the NE Atlantic region.</b>   
  - <strike>GEBCO offer a 1/4 arcminute (15 arcseconds) digital elevation model for free, under certain licence conditions.  This data covers the globe.</strike>
  
Reference: <a href="http://marine.gov.scot/taxonomy/term/17/">http://marine.gov.scot/taxonomy/term/17/</a>

&nbsp;
<h5>SHORELINES</h5>
 - "derived from Satellite EMODnet shoreline data, relative to MSL."


&#9634; Download EMODnet bathymetry and shoreline data  
&#9634; Import bathymetry data (should we have done it in the sea lice report!?)  
&#9634; Calculate the Loch's area, _A'_, and volume, _V'_, from the bathymetry & coastline data (optional?)  

&nbsp;
<h5>OPENDRIFT</h5>
&#9634; Run an Opendrift simulation      
  * evenly-spaced particles (what depth?) - not 1 per cell (test multiple spacings: 5 m, 10 m)
  * seeds on land aren't moved back into the sea:
    ```sh
    o.set_config('seed:ocean_only', False)
    ```
    <div style="line-height:50%;">
        <br>
    </div> 
  * flushing time _Tf'_: when 67% are gone
  * tiday range _R'_
  
&#9634; Repeat simulation for different tide conditions (10 times)  
&#9634; Find best fit for nparticles vs. time, get averaged _Tf'_   
&#9634; Derive ECE' and NEI' from _A'_, _V'_, _R'_ and _Tf'_  
