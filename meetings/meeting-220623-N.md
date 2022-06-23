---
layout: page
title: CLAWS
subtitle: Meeting - June 23rd
nav-short: true
---

## Nutrients module (N)

&nbsp;
<h5>GOALS</h5> 

- Reproduce the work presented in the PARTRAC report (uses MIKE 21)   
  + Estimate flushing time, ECE and NEI from the work of Gillibrand (2002)  
  + Compute flushing time in OpenDrift
  + Compare estimated vs. computed ECE and NEI values 

<p>NB: on this page, non-primed quantities are found in or derived from Edwards and Sharples (1986), "Sea Lochs: a Catalogue".  
Primed quantities are quantities extracted from OpenDrift simulations.</p>

&nbsp;  
<h5>N1. ECE & NEI ESTIMATIONS</h5>

&#9989; Add Loch's area, _A_, volume, _V_, and tidal range, _R_, to `Loch` class  
&#9989; Estimate flushing rate and flushing time, _Tf_  
&#9989; Derive ECE and NEI   

&nbsp;
<h5>N2. BATHYMETRY AND SHORELINES</h5>

  - EMODnet offer a 1/16 arcminute (3.75 arcseconds) digital elevation model and covers the NE Atlantic region. 3.75 arcsecond ~ 66.8 m at 55 deg. latitude  
  
&#9989; Download the FOC bathymetry and shoreline data  
&#9989; Download the EMODnet bathymetry and shoreline data  
&#9634; Import and plot bathymetry data on terrain map     

&nbsp;
<h5>N3. OPENDRIFT</h5>
&#9989; Seed particles using a GeoJSON string: <a href="http://geojson.io/">geojson.io</a>. A polygon is drawn around a loch with as many edges as necessary. The current polygon could be improved to increase the ratio of active to stranded particles:
<p align="center">
  <img src="/docs/meeting/geoJSON_2.png" style="height:300px">
</p>  

&#9634; Calculate the Loch's area, _A'_, and volume, _V'_, using an approximate method:
  + _A'_: area GeoJSON polygon * nactive / (nactive + nstranded)
  + _V'_: seeding on the seafloor, get depth _z'_ for all active particles. Because of particles are equally spaced, _V'_ ~= _A'_ * _z'_mean, where _z'_mean is the mean depth.
      ```sh
        z='seafloor+0'
      ```
      <div style="line-height:50%;">
          <br>
      </div>
  + Graph _A'/V'_ vs. nseeds  
    

&#9989; Create hydrodynamics solution file for Loch Long over a period of 10+ days (slightly longer time than the flushing time predicted by PARTRAC)  
&#9989; Derive tidal range _R'_ from the hydrodynamics simulation: 2.1 m  
&#9989; Write user scripts  
  * _setup_:  
  * _pre_:  
    - evenly-spaced particles in the GeoJSON polygon  
    - particles seeded on the seafloor  
    - particles' lon/lat/z saved to file if marked as active  
    - compute nactive / (nactive + nstranded)  
    - compute _z'_mean  
    - user updates `Loch` in _setup_  
        ```python
        # Loch name
        loch = Loch(name="Loch Long", area=45.3, tidal_range=2.7, mean_depth=-38.005,
                    reference="Hydrodynamics and CLAWS-pre (using 30k seeds)")
        ```    
  * _run_:  
    - loads active particles from _pre_ (saves disk space & comp. time)  
    - initial depth of a particle is a random number in [-z_local_water_column, 0]
    - rest as usual  
  * _post_:  
    - plot concentration on terrain map  
    - plot flushing time  
    - save time series of number of particles to file  
    - derive ECE' and NEI' from _A/A'_, _V/V'_, _R/R'_ and _Tf/Tf'_ 

Long Long (including Loch Goil and the upper basin of Loch Long)  
12830 active particles  
Initially:    
<p align="center">
  <img src="/docs/meeting/220623/concentration_0000.png" style="height:600px">
</p> 
At t slightly greater than 'flushing time':
<p align="center">
  <img src="/docs/meeting/220623/concentration_0360.png" style="height:600px">
</p> 

<p align="center">
<video height="600" autoplay>
  <source src="/docs/meeting/220623/concentration.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video> 
</p>
    

<h5>N3. FLUSHING TIME & ECE COMPARISON</h5>

<p align="center">
  <img src="/docs/meeting/220623/flushing_time.png" style="height:350px">
</p>  



```python
Loch Long's flushing time (day) = 9.57
Existing biomass - eval.:
Salmon Farm's ECE (kg/m^3) = 3.595962e-07
Salmon Farm's ECE (ug/L) = 0.359596
Salmon Farm's ECE (umol/L) = 0.025685
Salmon Farm's nutrient enhancement index = 1

Loch Long's flushing time (day) = 16.05
Existing biomass - comp.:
Salmon Farm's ECE (kg/m^3) = 6.153615e-07
Salmon Farm's ECE (ug/L) = 0.615362
Salmon Farm's ECE (umol/L) = 0.043954
Salmon Farm's nutrient enhancement index = 1
```

<h5>N4. FUTURE WORK</h5>
 - Suggestions?
 - Preliminary discussion about the Waste (W) module?


&nbsp;
<h5>HISTORY</h5>  
  - Meeting, June 23rd (N): <a href="https://claws-scot.github.io/meetings/meeting-220623-N/">meeting-220623-N</a>  
  - Meeting, June 23rd (B): <a href="https://claws-scot.github.io/meetings/meeting-220623-B/">meeting-220623-B</a>  
  - Progress, June 21st (N): <a href="https://claws-scot.github.io/meetings/progress-220621-N/">progress-220621-N</a>  
  - Meeting, June 13th (N): <a href="https://claws-scot.github.io/meetings/meeting-220613-N/">meeting-220613-N</a>  
