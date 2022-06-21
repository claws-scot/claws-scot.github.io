---
layout: page
title: CLAWS
subtitle: Progress - June 21st
nav-short: true
---

## Nutrients module

- Nutrient: Nitrogen (N), 2 sources
  + dissolved ammonia  
  + particulate waste emissions re-dissolving into the water column from the seabed   

&nbsp;
<h5>1. GOALS</h5> 
- Reproduce the work presented in the PARTRAC report (uses MIKE 21)   
  + Estimate flushing time, ECE and NEI from the work of Gillibrand (2002)  
  + Compute flushing time in OpenDrift
  
&nbsp;
<h5>2. ECE & NEI ESTIMATIONS</h5>

<p><b>NB</b>: on this page, non-primed quantities are found in or derived from Edwards and Sharples (1986), "Sea Lochs: a Catalogue".  
Primed quantities are quantities extracted from OpenDrift simulations.</p>
  
&#9989; Add Loch's area, _A_, volume, _V_, and tidal range, _R_, to `Loch` class  
&#9989; Estimate flushing rate and flushing time, _Tf_  
&#9989; Derive ECE and NEI (see Results from test_ECE.py below)  

<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/excel-approx-formulas.png" style="height:300px">
</p>

- Loch Long implementation using the data circled in red:  
    ```python
    class LochLong(Loch):
        def __init__(self):
            super().__init__(area=44.0, tidal_range=3.1, volume=1758.0,
                             mean_depth=40.0,
                             reference="Gillibrand, P.A., Gubbins, G.J. (2002)")
    ```
    <div style="line-height:50%;">
        <br>
    </div>
- Results of test_ECE.py (see: <a href="https://github.com/vincentcasseau/CLAWS/blob/main/tests/test_ECE.py">here</a>) are matching the Excel sheet's outputs  
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
    <div style="line-height:50%;">
        <br>
    </div>

&nbsp;
<h5>3. BATHYMETRY and SHORELINES</h5>

  - EMODnet offer a 1/16 arcminute (3.75 arcseconds) digital elevation model and covers the NE Atlantic region. 1 arcsecond ~ 17.8 m at 55 deg. latitude  
  
&#9989; Tom to send Vincent the FOC bathymetry data  
&#9634; Download EMODnet bathymetry and shoreline data  
&#9634; Import and plot bathymetry data on terrain map     

&nbsp;
<h5>4. OPENDRIFT</h5>
&#9989; Seed particles using a GeoJSON string: <a href="https://opendrift.github.io/gallery/example_seed_geojson.html">opendrift.github.io/gallery/example_seed_geojson</a>, <a href="http://geojson.io/">geojson.io</a>. A polygon is drawn around a loch with as many edges as necessary:
<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/geoJSON_2.png" style="height:350px">
</p>  
<div style="line-height:50%;">
    <br>
</div>

&#9634; Calculate the Loch's area, _A'_, and volume, _V'_, using an
  - Exact method: the bathymetry & coastline data   
  - Approximate method:
    + _A'_: area GeoJSON polygon * nactive / (nactive + nstranded)
    + _V'_: seed on the sea floor, get _z_ for all active particles and neighbour particles to create polygons whose base lies at sea level  
      ```sh
        z='seafloor+0'
      ```
      <div style="line-height:50%;">
          <br>
      </div>
    + more and more precise as nseeds is increased. Graph _A'_ vs. nseeds, does it converge?, use Richardson's extrapolation to get _A'_(nseeds &#8594; &#8734;)
    

Total GeoJSON area (land + loch) = 106.26 km<sup>2</sup>  
Reference area (PARTRAC) = 44 km<sup>2</sup>  
Edwards and Sharples, Low-Water area = 41.2 km<sup>2</sup>  
Edwards and Sharples, High-Water area = 43.4 km<sup>2</sup>  

| **nseeds** | **nactive** | <b>Loch Area (km<sup>2</sup>)</b> |
|---|---|---|
| 512 | 223 | 46.28 |
| 1024 | 433 | 44.93 |
| 2048 | 888 | 46.07 |
| 4096 | 1763 | 45.74 |
| 8192 | 3500 | 45.40 |
| 16384 | 7004 | 45.43 |
| 32768 | 13998 | 45.39 |
| 65536 | 27884 | 45.21 |
| 131072 | 55770 | 45.21 |

&#9989; Tom to send Vincent a hydrodynamics solution file for Loch Long over a period of 10 days (slightly longer time than the flushing time predicted by PARTRAC)  
&#9989; Derive tidal range _R'_ from the hydrodynamics simulation: 2.1 m  
&#9989; Run an Opendrift simulation      
 - evenly-spaced particles, random depth, control spacing or number of particles  
 - seeds on land aren't moved back into the sea:  
    ```sh
    o.set_config('seed:ocean_only', False)
    ```
    <div style="line-height:50%;">
        <br>
    </div> 
&#9989; Draw nparticles vs. time, get _Tf'_   
&#9634; Derive ECE' and NEI' from _A/A'_, _V/V'_, _R/R'_ and _Tf'_  
