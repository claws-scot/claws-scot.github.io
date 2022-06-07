---
layout: page
title: Gallery
nav-short: true
---

<!-- Slideshow container -->
<div class="slideshow-container">

  <!-- Full-width images with number and caption text -->
  <div class="clawsslides">
    <div class="solvernametext">CLAWS</div>
    <img src="/docs/sb_aza/area_over_EQS.png" style="height:300px">
    <div class="text">Area where the Azamethiphos concentration exceeds the 72-h EQS</div>
  </div>
  
  <div class="clawsslides">
    <div class="solvernametext">CLAWS</div>
    <img src="/docs/sb_aza/area_over_MAC.png" style="height:300px">
    <div class="text">Area where the Azamethiphos concentration exceeds the 72-h MAC</div>
  </div>
  
  <div class="clawsslides">
    <div class="solvernametext">CLAWS</div>
    <img src="/docs/sb_aza/peak_concentration.png" style="height:300px">
    <div class="text">Peak Azamethiphos concentration</div>
  </div>
  
  <!-- Next and previous buttons -->
  <a class="prev" onclick="plusSlides(-1)">&#10094;</a>
  <a class="next" onclick="plusSlides(1)">&#10095;</a>
</div>

<!-- The dots/circles -->
<div style="text-align:center">
  <span class="dot" onclick="currentSlide(1)"></span>
  <span class="dot" onclick="currentSlide(2)"></span>
  <span class="dot" onclick="currentSlide(3)"></span>
</div>

<br>
---

<p align="center">
<video height="600" autoplay>
  <source src="/docs/sb_aza/concentration.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video> 
</p>


<!-- Javascript for the slideshow -->
<script>
let slideIndex = Math.floor(Math.random() * 4);
let timeoutms;
let loop = true;

showSlides(slideIndex, loop);

<!-- Next/previous controls-->
function plusSlides(n) {
  if (loop) {
    loop = false;
    slideIndex -= 1;
  }
  slideIndex += n;
  showSlides();
}

<!--Thumbnail image controls-->
function currentSlide(n) {
  if (loop) {
    loop = false;
  }
  slideIndex = n;
  showSlides();
}

function showSlides() {
  let i;
  let slides = document.getElementsByClassName("clawsslides");
  let dots = document.getElementsByClassName("dot");
  
  if (slideIndex >= slides.length) {slideIndex = 0}
  if (slideIndex < 0) {slideIndex = slides.length - 1}
  
  for (i = 0; i < slides.length; i++) {
    slides[i].style.display = "none";
  }
  for (i = 0; i < dots.length; i++) {
    dots[i].className = dots[i].className.replace(" active", "");
  }
  slides[slideIndex].style.display = "block";
  dots[slideIndex].className += " active";
  
  if (loop) {
    timeoutms = setTimeout(showSlides, 8000);
    slideIndex++;
  }
  else {
    clearTimeout(timeoutms);
  }
} 
</script>
