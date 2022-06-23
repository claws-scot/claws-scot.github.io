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

Duplicate the chemical (here Azamethiphos) and give it a different name with a suffix '\_X' where _X_ is an index ranging between 1 and 9.
Then, create a specific treatment for each farm that uses these chemicals (see below, seedings from farms at Little Cumbrae and Great Cumbrae can be analysed separately).

NB1: On concentration maps and plots, the underscore could easily be replaced to give 'Azamethiphos, marker 1', etc.

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
                 nparticles=10000,
                 Chemicals=chemicals[0],
                 name="Sea Lice Treatment"),
    BathMedicine(tarpaulin_height=3.,
                 tarpaulin_radius=19.,
                 seeding_times=seeding_times,
                 nparticles=10000,
                 Chemicals=chemicals[1],
                 name="Sea Lice Treatment")]             

# Farms
farms = [SalmonFarm(GreatCumbrae(), treatments[0]),
         SalmonFarm(LittleCumbrae(), treatments[1])]
```

All plots and contours will be prepended the name of the chemical, Azamethiphos\_X, where _X_ refers to the origin marker.

NB2: a farm can use multiple chemicals as well. Here Azamethiphos is dispersed at t = 0 h and t = 9 h, while Deltamethrin is used at t = 3 h and t = 6 h following the initial treatment.

```python
# Chemicals used
chemicals = [Azamethiphos(half_life=8.9, Loch=loch, input_time_units='day'),
             Deltamethrin(Loch=loch)]

# Farms             
farms = [SalmonFarm(GreatCumbrae(),
                    BathMedicine(tarpaulin_height=3.,
                                tarpaulin_radius=19.,
                                seeding_times=[0., 3., 6., 9.],
                                nparticles=10000,
                                Chemicals=[chemicals[0], chemicals[1],
                                           chemicals[1], chemicals[0]],
                                name="Sea Lice Treatment",
                                input_time_units='day')]
```

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

and the _run_ script is simplied to:  
```python
# Set OpenDrift configuration
for key, value in oceandrift_config.items():
    o.set_config(key, value)
```

As a consequence, there is no need to check the _run_ script anymore before running a computation, everything happens in the _setup_ script.

- New terrain maps, with a legend, a scale, a compass and a frame.  

The aspect ratio is frozen to keep elements in place. The domain extent specified by the user is referred to as 'Domain' in the legend and the area outside this domain is whitened on the map.

<p align="center">
  <img src="/docs/meeting/220623/concentration_0330.png" style="height:600px">
</p> 

- For all simulations, a `media` folder is created and images and videos are created inside this directory.

&nbsp;
<h5>B3. REPORT UPDATE</h5>
- Version 2 soon?


&nbsp;
<h5>HISTORY</h5>
  - Meeting, June 23rd (N): <a href="https://claws-scot.github.io/meetings/meeting-220623-N/">meeting-220623-N</a>  
  - Meeting, June 23rd (B): <a href="https://claws-scot.github.io/meetings/meeting-220623-B/">meeting-220623-B</a>  
  - Progress, June 21st (N): <a href="https://claws-scot.github.io/meetings/progress-220621-N/">progress-220621-N</a>  
  - Meeting, June 13th (N): <a href="https://claws-scot.github.io/meetings/meeting-220613-N/">meeting-220613-N</a>  
