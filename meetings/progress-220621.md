---
layout: page
title: CLAWS
subtitle: Progress - June 21st
nav-short: true
---

## Nutrients module

<h5>GOALS</h5> 
- Reproduce the work presented in the PARTRAC report (uses MIKE 21)   
  + Estimate flushing time, ECE and NEI from the work of Gillibrand (2002)  
  + Compute flushing time in OpenDrift
  + Compare estimated vs. computed ECE and NEI values 

<p>NB: on this page, non-primed quantities are found in or derived from Edwards and Sharples (1986), "Sea Lochs: a Catalogue".  
Primed quantities are quantities extracted from OpenDrift simulations.</p>
  
<h5>1. ECE & NEI ESTIMATIONS</h5>

&#9989; Add Loch's area, _A_, volume, _V_, and tidal range, _R_, to `Loch` class  
&#9989; Estimate flushing rate and flushing time, _Tf_  
&#9989; Derive ECE and NEI   

<h5>2. BATHYMETRY AND SHORELINES</h5>

  - EMODnet offer a 1/16 arcminute (3.75 arcseconds) digital elevation model and covers the NE Atlantic region. 1 arcsecond ~ 17.8 m at 55 deg. latitude  
  
&#9989; Download the FOC bathymetry and shoreline data  
&#9989; Download the EMODnet bathymetry and shoreline data  
&#9634; Import and plot bathymetry data on terrain map     

<h5>3. OPENDRIFT</h5>
&#9989; Seed particles using a GeoJSON string: <a href="http://geojson.io/">geojson.io</a>. A polygon is drawn around a loch with as many edges as necessary:
<p align="center">
  <img src="/docs/meeting/geoJSON_2.png" style="height:300px">
</p>  

&#9634; Calculate the Loch's area, _A'_, and volume, _V'_, using an approximate method:
  + _A'_: area GeoJSON polygon * nactive / (nactive + nstranded)
  + _V'_: seed on the sea floor, get _z_ for all active particles and neighbour particles to create polygons whose base lies at sea level  
      ```sh
        z='seafloor+0'
      ```
      <div style="line-height:50%;">
          <br>
      </div>
  + Graph _A'/V'_ vs. nseeds  
    

&#9989; Create hydrodynamics solution file for Loch Long over a period of 10+ days (slightly longer time than the flushing time predicted by PARTRAC)  
&#9989; Derive tidal range _R'_ from the hydrodynamics simulation: 2.1 m  
&#9989; Run an Opendrift simulation      
 - evenly-spaced particles, random depth, control number of particles  
 - seeds on land aren't moved back into the loch:  
    ```sh
    o.set_config('seed:ocean_only', False)
    ```
    <div style="line-height:50%;">
        <br>
    </div> 
    
&#9989; Draw nparticles vs. time, get _Tf'_   
&#9634; Derive ECE' and NEI' from _A/A'_, _V/V'_, _R/R'_ and _Tf'_  

Initially:
<p align="center">
  <img src="/docs/meeting/concentration_0000.png" style="height:500px">
</p> 
At 'flushing time':
<p align="center">
  <img src="/docs/meeting/concentration_2280.png" style="height:500px">
</p> 
nparticles vs. time:
<p align="center">
  <img src="/docs/meeting/flushing_time.png" style="height:500px">
</p> 
