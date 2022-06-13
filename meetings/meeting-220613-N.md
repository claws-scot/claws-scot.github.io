---
layout: home
title: CLAWS
subtitle: Meeting - June 13th
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
&#9989; Understand why 2 values of _S_ are used: 48.2 kgN/tonne-prod. (existing biomass column), and 40.64 kgN/tonne-prod. (options 1-3 columns)     
  - function of stocking, feeding and harvesting strategies employed during cultivation
  - amount of N released = input amount - amount incorporated into fish growth  

&#8680; <u>Meeting outcome</u>: _S_ = 40.64 kgN/tonne-prod. is most likely a user input  

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
<h5>3. BATHYMETRY</h5>

  - Scotland's bathymetry data in OpenDrift is too coarse
  
<p align="center">
  <img src="/docs/meeting/OD_seabed_1.png" style="height:200px">
</p>  

Reference: <a href="http://marine.gov.scot/taxonomy/term/17/">http://marine.gov.scot/taxonomy/term/17/</a>  

  - The UKHO offer survey data from UKHO marine data portal under open licence.  This tends to cover inshore areas of the UK, and includes data surveyed by Marine Scotland.  
    + <a href="https://data.admiralty.co.uk/portal/apps/sites/#/marine-data-portal/pages/seabed-mapping-services">data.admiralty.co.uk seabed-mapping-services</a>  
    + <a href="https://seabed.admiralty.co.uk/?x=-19567.88&y=6780270.16&z=5.00">seabed.admiralty.co.uk/?x=-19567.88&y=6780270.16&z=5.00</a>  
    + file format for free download is set to ASCII or BAG
    + Loch Long area near Ardentinny is missing!  
    
<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/admiralty.png" style="height:300px">
</p>
    
  - <strike>OceanWise offer a 1 arcsecond digital elevation model under commercial licence. This covers UK waters.</strike>   
  - <b>EMODnet offer a 1/16 arcminute (3.75 arcseconds) digital elevation model for free, under certain licence conditions. This data covers the NE Atlantic region.</b>   
  - <strike>GEBCO offer a 1/4 arcminute (15 arcseconds) digital elevation model for free, under certain licence conditions.  This data covers the globe.</strike>
  
<p><b>NB</b>: 1 arcseconds ~ 17.8 m at 55 deg. latitude</p>
  
&#8680; <u>Meeting outcome</u>: Vincent to download the EMODnet bathymetry data for use in the Waste module  
&#8680; <u>Meeting outcome</u>: Tom to send Vincent the FOC bathymetry data  

&nbsp;
<h5>4. SHORELINES</h5>
 - "derived from Satellite EMODnet shoreline data, relative to MSL."

&#9634; Download EMODnet bathymetry and shoreline data  
&#9634; Import bathymetry data    

&nbsp;
<h5>5. OPENDRIFT</h5>
&#9989; Find an appropriate seeding technique:
  - <strike>using gml files: <a href="https://opendrift.github.io/gallery/example_seed_demonstration.html">opendrift.github.io/gallery/example_seed_demonstration</a> (see last example) </strike>   
  - <strike>using shape files: <a href="https://opendrift.github.io/gallery/example_seed_from_shapefile.html">opendrift.github.io/gallery/example_seed_from_shapefile</a></strike>    
  - <b> using a GeoJSON string</b>: <a href="https://opendrift.github.io/gallery/example_seed_geojson.html">opendrift.github.io/gallery/example_seed_geojson</a>, <a href="http://geojson.io/">geojson.io</a>  

A polygon is drawn around a loch with as many edges as necessary ...
<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/geojson.png" style="height:500px">
</p> 

... and the area can readily be extracted ...  
<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/geojson_area.png" style="height:150px">
</p>
 
... but there would seem to be a small eastwards shift (projection inconsistency between the website and OpenDrift?) ...   
<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/better-seeding.png" style="height:500px">
</p>

... Coarser polygon - larger margins required:

<p align="center" style="border-style:solid; border:2; border-color:black">
  <img src="/docs/meeting/geoJSON_2.png" style="height:500px">
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

| **nseeds** | **nactive** | **Loch Area (km^2)** |
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

<p><b>NB</b>: Move the Loch's mouth northwards a bit</p>


&#9634; Run an Opendrift simulation      
&#8680; <u>Meeting outcome</u>: Tom to send Vincent a hydrodynamics solution file for Loch Long over a period of 10 days (slightly longer time than the flushing time predicted by PARTRAC)  
  * derive tidal range _R'_ from the hydrodynamics simulation
  * evenly-spaced particles (what depth?), control spacing or number of particles  
  * seeds on land aren't moved back into the sea:
    ```sh
    o.set_config('seed:ocean_only', False)
    ```
    <div style="line-height:50%;">
        <br>
    </div> 
  * flushing time _Tf'_: when 63% are gone (write `is_particle_in_GeoJSON_polygon()`)
  
&#9634; Find best fit for nparticles vs. time, get _Tf'_   
&#9634; Derive ECE' and NEI' from _A/A'_, _V/V'_, _R/R'_ and _Tf'_  
