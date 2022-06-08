---
layout: page
title: UML diagrams
nav-short: true
---

<div id="mySidenav" class="sidenav">
  <a href="javascript:void(0)" class="closebtn" onclick="closeNav()"><i class='fa fa-times'></i></a>
  <header>CODE</header>
  <a href="https://claws-scot.github.io/theory"><b>Theory</b></a>
  <a href="https://claws-scot.github.io/umls" style="background-color:#FFCCCC"><b>UML diagrams</b></a>
  <a href="https://claws-scot.github.io/umls/#1-sites" style="background-color:#FFE6E6; padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; 1. Sites</a>
  <a href="https://claws-scot.github.io/umls/#2-lochs" style="background-color:#FFE6E6; padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; 2. Lochs</a>
  <a href="https://claws-scot.github.io/umls/#3-chemicals" style="background-color:#FFE6E6; padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; 3. Chemicals</a>
  <a href="https://claws-scot.github.io/umls/#4-treatments" style="background-color:#FFE6E6; padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; 4. Treatments</a>
  <a href="https://claws-scot.github.io/umls/#5-farms" style="background-color:#FFE6E6; padding-top:4px; padding-bottom:4px">&nbsp;&nbsp; 5. Farms</a>
  <a href="https://claws-scot.github.io/umls/#6-quadtrees" style="background-color:#FFE6E6; padding-top:4px">&nbsp;&nbsp; 6. Quadtrees</a>
  <a href="https://claws-scot.github.io/umls/#7-probe" style="background-color:#FFE6E6; padding-top:4px">&nbsp;&nbsp; 7. Probe</a>
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

### 1. Sites

<p align="center">
<img src="/docs/UML/sites.png" width="800">
</p>

### 2. Lochs

<p align="center">
<img src="/docs/UML/lochs.png" width="800">
</p>

### 3. Chemicals

<p align="center">
<img src="/docs/UML/chemicals.png" width="800">
</p>

### 4. Treatments

<p align="center">
<img src="/docs/UML/treatments.png" width="800">
</p>

### 5. Farms

<p align="center">
<img src="/docs/UML/farms.png" width="800">
</p>

### 6. Quadtrees

<p align="center">
<img src="/docs/UML/sites.png" width="800">
</p>

### 7. Probe

<p align="center">
<img src="/docs/UML/probe.png" width="800">
</p>
