---
layout: page
title: CLAWS
subtitle: Meeting - June 23rd
nav-short: true
---

## Bath treatment module (B)

&nbsp;
<h5>GOALS</h5> 
- Model several half-lives
- Report the newest additions
- Update the report

&nbsp;
<h5>B1. HALF-LIVES</h5>
- Code verification for several half-lives: exponential decay test revisited

One half-life set to 25 hours (1 seeding of 1000 particles):
<p align="center">
  <img src="/docs/meeting/220623/halflife_particles_status.png" style="height:300px"><img src="/docs/meeting/220623/halflife_particles_plot.png" style="height:300px">
</p>

Two half-lives set to 25 hours and 12.5 hours (2 seedings of 1000 particles):
<p align="center">
  <img src="/docs/meeting/220623/halflives_particles_status.png" style="height:300px"><img src="/docs/meeting/220623/halflives_particles_plot.png" style="height:300px">
</p> 

- In OceanDrift, the origin marker of each particle is now used to select the chemical half-life in the input list of half-lives, `self.get_config('drift:half_life')`:

```python
hl = self.get_config('drift:half_life')

half_lives = np.array([hl[int(om)] for om in self.elements.origin_marker])
```

In the user scripts, this tranlates to:
```python
# Chemicals used
chemicals = [Azamethiphos(half_life=8.9, Loch=loch, input_time_units='day'),
             Deltamethrin(Loch=loch)]
             
o.set_config('drift:half_life': [chem.half_life('s') for chem in chemicals])
```

where Azamethiphos has a half-life of 8.9 days and Deltamethrin has an infinite half-life (does not decay).

- I use a single chemical and I want to use OpenDrift's origin marker to track seedings from different farms (or same farm different time) as originally intended, what do I do?  

Duplicate the chemical (here Azamethiphos) and give it a different name using the suffix '\_X' where _X_ is an index ranging between 1 and 9.
Then, create a specific treatment for each farm that uses these chemicals defined (see below).

NB: On concentration maps and plots, the suffix could be removed.

```python
# Loch name
loch = UndefinedLoch(name='Firth of Clyde')

# Chemicals used
chemicals = [Azamethiphos(name="Azamethiphos_1", half_life=8.9, Loch=loch,
                          input_time_units='day'),
             Azamethiphos(name="Azamethiphos_2", half_life=8.9, Loch=loch,
                          input_time_units='day']
             
# Seeding times (hours)
seeding_times = [0., 3., 6., 9., 24., 27., 30., 48., 51., 54.]

# Type of treatments
treatments = [\
    BathMedicine(tarpaulin_height=3.,
                 tarpaulin_radius=19.,
                 seeding_times=seeding_times,
                 nparticles=600,
                 Chemicals=chemicals[0],
                 name="Sea Lice Treatment"),
    BathMedicine(tarpaulin_height=3.,
                 tarpaulin_radius=19.,
                 seeding_times=seeding_times,
                 nparticles=600,
                 Chemicals=chemicals[1],
                 name="Sea Lice Treatment")]             

# Farms
farms = [SalmonFarm(GreatCumbrae(), treatments[0]),
         SalmonFarm(LittleCumbrae(), treatments[1])]
```

All plots and contours will be prepended the name of the chemical, Azamethiphos\_X, where _X_ refers to the origin marker.

&nbsp;
<h5>B2. NEWEST ADDITIONS</h5>

- The OpenDrift configuration is now set in the _setup_ script using the following dictionary:

```python
# ---------------------------- OCEANDRIFT CONFIG ----------------------------- #
oceandrift_config = {
    'general:coastline_action': 'previous',
    
    'environment:fallback:ocean_vertical_diffusivity': vertical_diffusivity,
    
    'drift:advection_scheme': 'runge-kutta4',
    'drift:horizontal_diffusivity': horizontal_diffusivity,
    'drift:half_life': [chem.half_life('s') for chem in chemicals],
    'drift:vertical_mixing': True,
    
    'vertical_mixing:diffusivitymodel': 'environment',
    'vertical_mixing:timestep': timestep_seconds,
}
```

and the _run_ script can be simplied to:  
```python
# Set OpenDrift configuration
for key, value in oceandrift_config.items():
    o.set_config(key, value)
```

As a consequence, there is no need to check the _run_ script anymore before running a computation, everything happens in the _setup_ script.

- New terraim maps, with a legend, a scale, a compass and a frame. The aspect ratio is frozen to keep elements in place.
<p align="center">
  <img src="/docs/meeting/220623/concentration_0330.png" style="height:600px">
</p> 

- For all simulations, a `media` folder is created and images and videos are created inside this directory.

&nbsp;
<h5>B3. REPORT UPDATE</h5>
- Version 2?


&nbsp;
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

  - EMODnet offer a 1/16 arcminute (3.75 arcseconds) digital elevation model and covers the NE Atlantic region. 1 arcsecond ~ 17.8 m at 55 deg. latitude  
  
&#9989; Download the FOC bathymetry and shoreline data  
&#9989; Download the EMODnet bathymetry and shoreline data  
&#9634; Import and plot bathymetry data on terrain map     

&nbsp;
<h5>N3. OPENDRIFT</h5>
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
&#9989; Write user scripts and run an Opendrift simulation      
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

Using 252 active particles seeded uniformly in lon/lat and with a random initial depth comprised between [-10, 0] m (no seabed):    
- At t = 0:
<p align="center">
  <img src="/docs/meeting/concentration_0000.png" style="height:500px">
</p> 
- At t slightly greater than 'flushing time':
<p align="center">
  <img src="/docs/meeting/concentration_2280.png" style="height:500px">
</p> 
- nparticles vs. time:
<p align="center">
  <img src="/docs/meeting/flushing_time.png" style="height:350px">
</p>

&nbsp;
<h5>HISTORY</h5>
  - Progress 220621: <a href="https://claws-scot.github.io/meetings/progress-220621-N/">https://claws-scot.github.io/meetings/progress-220621/</a>  
  - Meeting 220613: <a href="https://claws-scot.github.io/meetings/meeting-220613-N/">https://claws-scot.github.io/meetings/meeting-220613-N/</a>  
