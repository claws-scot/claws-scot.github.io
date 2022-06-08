---
layout: page
title: Guide
subtitle: Quadtree structure
nav-short: true
---

<div id="mySidenav" class="sidenav">
  <a href="javascript:void(0)" class="closebtn" onclick="closeNav()"><i class='fa fa-times'></i></a>
  <header>CODE</header>
  <a href="https://claws-scot.github.io/guide" style="background-color:#FFCCCC"><b>Guide</b></a>
  <a href="https://claws-scot.github.io/chemical-decay" style="padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; Chemical decay</a>
  <a href="https://claws-scot.github.io/ensemble-averaging" style="padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; Ensemble averaging</a>
  <a href="https://claws-scot.github.io/quadtree-structure" style="background-color:#FFE6E6; padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; Quadtree structure</a>
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

<p>Particles are binned into voxels of size `Dw^2 \times Dh`, where `Dw` is the bin's width and `Dh` is the bin's depth. These dimensions are constant throughout the entire domain. However, areas of high particle density could benefit from being further refined for greater accuracy and decreasing `Dw` may not always be possible memory-wise.<br>
The <i>get_lonlat_bins</i> function presented in Listing 5 is thus revised in Listing 7 to accommodate a quadtree histogram structure whereby a `2 \times 2` sub-histogram is created for root and parent histogram bins selected for refinement. A sub-histogram has the following properties:</p>

- a child histogram's bounding box is exactly that of the parent histogram bin marked for refinement;
- a child histogram's bin depth is the same as the parent histogram (hence a quadtree and not an octree);
- a child histogram exists for a single time-step only.

<p>The quadtree histogram structure is visible on concentration heat maps and for the following time series:</p>

- peak chemical concentration;
- area greater than the environmental quality standard (EQS);
- area greater than the maximum allowable concentration (MAC) standard;
- probe stations reporting the chemical substance's concentration.

```python
def get_lonlat_bins(self, pixelsize_m, **kwargs):
        if 'corners' in kwargs:
            # Impose the extent of the domain
            lonmin, lonmax, latmin, latmax = kwargs['corners']
            if pixelsize_m is None:
                # Inputs were in fact lon_bincenter, binlength/2
                #                     lat_bincenter, binwidth/2
                # For clarity, these are renamed
                latcen = latmin
                loncen = lonmin
                dlat = latmax
                dlon = lonmax
                # Sub-divide a bin into a new 2x2 domain
                latbin = np.array([latcen - dlat, latcen, latcen + dlat])
                lonbin = np.array([loncen - dlon, loncen, loncen + dlon])
            else:
                latmid = np.radians((latmin + latmax)/2.0)
                # m to degrees, correct within 1 cm
                deltalat = pixelsize_m / ( 111320.92 - 559.82*np.cos(2.*latmid)
                    + 1.175*np.cos(4.*latmid) - 0.0023*np.cos(6.*latmid) )
                deltalon = pixelsize_m / (111412.84*np.cos(latmid)
                    - 93.5*np.cos(3.*latmid) + 0.118*np.cos(5.*latmid) )
                latbin = np.arange(latmin, latmax, deltalat)
                lonbin = np.arange(lonmin, lonmax, deltalon)
        else:
            # Determine the extent of the domain from the particles' position
            [...]
            
        return lonbin, latbin
```

<p>The quadtree parameters are found in the following Listing:</p>

```python
quadtree = {
    # is_active: bool; Whether or not to create a quadtree from the root
    # histogram. default is None (ie, not set)
    self.__is_active = is_active
    # max_depth: integer; Maximum quadtree depth. default is 0
    # (ie, no quadtree)
    self.__max_depth = max_quadtree_depth
    # min_particles_per_bin: integer; Number of particles per bin that
    # triggers the creation of a child histogram within the bounds of a
    # parent histogram. default is 1000
    self.__min_particles_per_bin = min_particles_per_bin
    # variable_root_bin_width: bool; Whether or not the root bin width can
    # be edited automatically using a target concentration. default is False
    self.__variable_root_bin_width = variable_root_bin_width
    # concentration_target: float; Concentration target in ng/L to recompute
    # the root bin width. default is 1 ng/L
    self.__concentration_target = concentration_target
    # leaf_bin_width: float; Set target value for the leaf bin width
    # (meters). default is NaN
    self.__leaf_bin_width = leaf_bin_width,
    # variable_leaf_bin_width: bool; Whether or not the leaf bin width can
    # be edited automatically using the ratio of the leaf bin area to the
    # seeding area. default is False
    self.__variable_leaf_bin_width = variable_leaf_bin_width
    # leaf_to_seeding_area_ratio: float; ratio of the leaf bin area to the
    # seeding area of a reference seeding location. default is 5.0
    self.__leaf_to_seeding_area_ratio = leaf_to_seeding_area_ratio
```

```python
_quadtree_max_tolerated_depth = 4
```



```python
# Quadtree structure
quadtree = NoQuadtree()
```

<p>There are a total of five methods. In the first method, the root bin width, `Dw_r`, is defined by the user, and a minimum number of particles per bin is set to decide whether a bin can be subdivided.
The maximum depth of the quadtree is also set.</p>

```python
# Quadtree structure
quadtree = M1Quadtree(max_depth=2, min_particles_per_bin=100)
```

<p>In the second method, the leaf bin width, `Dw_l`, is set by the user and the  maximum quadtree depth is derived as</p>

<div class="equation">
  <equation class="equation__content">$$\text{max_quadtree_depth} = \displaystyle\lceil log_2\left(\frac{Dw_r}{Dw_l}\right) \rceil\text{.}$$</equation>
  <div class="equation__number">(1)</div>
</div>

<p>The rest is similar to method #1.</p>

```python
# Quadtree structure
quadtree = M2Quadtree(max_depth=2, min_particles_per_bin=100, leaf_bin_width=25)
```

<p>In the third method, the root bin width is computed using a target concentration, `C_\text{target}`, in ng/L, as</p>

<div class="equation">
  <equation class="equation__content">$$Dw_r = \displaystyle\sqrt{\frac{m_p}{C_\text{target} \times Dh}}\text{.}$$</equation>
  <div class="equation__number">(2)</div>
</div>

<p>where `m_p` is the mass of chemical substance (in grams) represented by a single particle. The dictionary keys are given in the following Listing:</p>

```python
# Quadtree structure
quadtree = M3Quadtree(min_particles_per_bin=100, concentration_target=1, input_conc_units='ng/L')
```

<p>The fourth method combines methods #2 and #3: the root bin width is calculated from a target concentration and the leaf bin width is set.</p>

```python
# Quadtree structure
quadtree = M4Quadtree(min_particles_per_bin=100, concentration_target=1, leaf_bin_width=25, input_conc_units='ng/L')
```

<p>Finally, the fifth method is similar to method #4 but for the leaf bin width that is computed from a prescribed leaf-to-seeding-area ratio, `f`, as</p>

<div class="equation">
  <equation class="equation__content">$$Dw_l = \displaystyle\sqrt{f \times A_\text{seeding}}\text{.}$$</equation>
  <div class="equation__number">(3)</div>
</div>

```python
# Quadtree structure
quadtree = M5Quadtree(min_particles_per_bin=100, concentration_target=1, leaf_to_seeding_area_ratio=2, input_conc_units='ng/L')
```
