---
layout: page
title: Theory
subtitle: Chemical decay
nav-short: true
---

<p>The exponential decay formula describes the fate of a chemical substance over time and states that half of its initial quantity, `N_0`, will have vanished after one half-life, `t_{1/2}`, on average. It can be written as</p>

<p>$$ N(t) = N_0 exp(-&#955; t), $$ &nbsp;&nbsp;(1)</p>
<!--    \label{eq:exponentialdecay}-->

<p>where `t` is the time and &#955; is the decay constant equal to</p>

<!--[^1]: The decay constant can be viewed as the probability of a single particle to decay per unit time.-->

<p>$$ &#955; = \frac{ln(2)}{t_{1/2}}. $$ &nbsp;&nbsp;(2)</p>
<!--    \label{eq:decayconstant}-->

<p>From a Lagrangian standpoint, the probability of a particle to decay during a time interval `&#916;t` can be expressed as</p>

<p>$$P_{decay} = &#955; &#916;t, $$&nbsp;&nbsp;(3)</p>
<!--    \label{eq:decayprobability}-->

<p>and a particle decays if</p>

<p>$$R < P_{decay}, $$ &nbsp;&nbsp;(4)</p>
<!--    \label{eq:decaytest}-->

<p>where `R` is a random number drawn from a uniform distribution over [0,1).</p>

<p>The implementation of chemical decay in the OceanDrift model first consists in adding the half-life to the model's configuration dictionary as</p>

```python
'drift:half_life': {'type': 'float',
                    'default': np.inf,
                    'min': 0, 'max': np.inf,
                    'units': 'seconds',
                    'description': 'Half-life of the drifting elements. Elements are be deactivated according to the exponential decay formula',
                    'level': self.CONFIG_LEVEL_ADVANCED}
```

<p>Then, a new function is added at the end of the OceanDrift _update_ function to model the probabilistic decay of particles as</p>

```python
def update(self):
    """Update positions and properties of elements."""

    # Simply move particles with ambient current
    self.advect_ocean_current()
    
    [...]

    # Probabilistic decay
    self.exponential_decay()

    # Optional machine learning correction
    self.machine_learning_correction()
```

<p>where <i>exponential\_decay<i> implements Equations 2-4.</p>

```python
def exponential_decay(self):
    """Deactivate decayed elements

        Exponential_decay: probability lambda*time_step that a particle
        decays during a time interval dt, where lambda is a decay constant
        equal to ln(2)/half_life
        
        An element is deactivated if lambda*time_step > rdn, where rdn is a
        random samples from a uniform distribution over [0,1)
    """
    if np.isinf(self.get_config('drift:half_life')):
        logger.debug('Exponential_decay deactivated')
        return
        
    lbd = np.log(2)/self.get_config('drift:half_life')
    proba_decay = lbd*self.time_step.total_seconds()
    rdn = np.random.random(self.num_elements_active())
    self.deactivate_elements(rdn < proba_decay, reason='decayed')
```

<p>Finally, an Oceandrift object modelling the chemical decay of a substance with a half-life of 8.9 days can be instantiated as follows:</p>

```python
o = OceanDrift(loglevel=20, seed=None)
o.set_config('drift:half_life', 8.9*86400)
```

<p>Please note that the half-life's default value is an infinite number which means that its implementation is transparent to any simulations that aren't using it.</p>
