---
layout: page
title: Guide
subtitle: Chemical decay
nav-short: true
---

<div id="mySidenav" class="sidenav">
  <a href="javascript:void(0)" class="closebtn" onclick="closeNav()"><i class='fa fa-times'></i></a>
  <header>CODE</header>
  <a href="https://claws-scot.github.io/guide" style="background-color:#FFCCCC"><b>Guide</b></a>
  <a href="https://claws-scot.github.io/guide" style="background-color:#FFE6E6; padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; Chemical decay</a>
  <a href="https://claws-scot.github.io/umls"><b>UML diagrams</b></a>
  <a href="https://claws-scot.github.io/tutorials"><b>Tutos</b></a>
</div>

<span style="position: fixed;font-size:30px;cursor:pointer; margin:0px; top:60px;left:30px;" onclick="reopenNav()">&#9776;</span>

<script>
function openNav() {
  document.getElementById("mySidenav").style.width = "210px";
  document.getElementById("mySidenav").style.transition = "0s";
}

function closeNav() {
  document.getElementById("mySidenav").style.width = "0px";
  localStorage.removeItem('show_sidenav');
}

function reopenNav() {
  document.getElementById("mySidenav").style.width = "210px";
  document.getElementById("mySidenav").style.transition = "0.2s";
  localStorage.setItem("show_sidenav", true);
}

if (localStorage.getItem("show_sidenav")) openNav()
</script>

<p>The exponential decay formula describes the fate of a chemical substance over time and states that half of its initial quantity, `N_0`, will have vanished after one half-life, `t_{1/2}`, on average. It can be written as</p>

<div class="equation">
  <equation class="equation__content">$$N(t) = N_0 exp(-&#955; t)\text{,}$$</equation>
  <div class="equation__number">(1)</div>
</div>
  
<p id="ref1">where `t` is the time and &#955; is the decay constant<a href="#fn1"><sup>1</sup></a> equal to</p>

<div class="equation">
  <equation class="equation__content">$$&#955; = \frac{ln(2)}{t_{1/2}}\text{.}$$</equation>
  <div class="equation__number">(2)</div>
</div>

<p>From a Lagrangian standpoint, the probability of a particle to decay during a time interval &#916;`t` can be expressed as</p>

<div class="equation">
  <equation class="equation__content">$$P_{\text{decay}} = &#955; &#916;t\text{,}$$</equation>
  <div class="equation__number">(3)</div>
</div>

<p>and a particle decays if</p>

<div class="equation">
  <equation class="equation__content">$$R < P_{\text{decay}}\text{,}$$</equation>
  <div class="equation__number">(4)</div>
</div>

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

<p>Then, a new function is added at the end of the OceanDrift <i>update</i> function to model the probabilistic decay of particles as</p>

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

<p>where <i>exponential_decay</i> implements Equations 2-4.</p>

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

<br>

<sup id="fn1">1. The decay constant can be viewed as the probability of a single particle to decay per unit time.<a href="#ref1" title="Jump back to footnote 1 in the text.">&#8617;</a></sup>
