---
layout: page
title: Tutorials
nav-short: true
---

<div id="mySidenav" class="sidenav">
  <a href="javascript:void(0)" class="closebtn" onclick="closeNav()"><i class='fa fa-times'></i></a>
  <header>CODE</header>
  <a href="https://claws-scot.github.io/guide"><b>Guide</b></a>
  <a href="https://claws-scot.github.io/umls"><b>UML diagrams</b></a>
  <a href="https://claws-scot.github.io/tutos" style="background-color:#FFCCCC"><b>Tutos</b></a>
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
