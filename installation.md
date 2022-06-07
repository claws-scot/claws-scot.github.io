---
layout: page
title: Installation
nav-short: true
--- 

<!--<div id="mySidenav" class="sidenav">-->
<!--  <a href="javascript:void(0)" class="closebtn" onclick="closeNav()"><i class='fa fa-times'></i></a>-->
<!--  <header>DOWNLOAD</header>-->
<!--  <a href="https://claws-scot.github.io/compatibility/">Compatibility</a>-->
<!--  <a href="https://claws-scot.github.io/maintenance/">Maintenance</a>-->
<!--  <a href="https://claws-scot.github.io/installation/">Installation</a>-->
<!--</div>-->

<!--<span style="position: fixed;font-size:30px;cursor:pointer; margin:0px; top:60px;left:30px;" onclick="reopenNav()">&#9776;</span>-->

<!--<script>-->
<!--function openNav() {-->
<!--  document.getElementById("mySidenav").style.width = "210px";-->
<!--  document.getElementById("mySidenav").style.transition = "0s";-->
<!--}-->

<!--function closeNav() {-->
<!--  document.getElementById("mySidenav").style.width = "0px";-->
<!--  localStorage.removeItem('show_sidenav');-->
<!--}-->

<!--function reopenNav() {-->
<!--  document.getElementById("mySidenav").style.width = "210px";-->
<!--  document.getElementById("mySidenav").style.transition = "0.1s";-->
<!--  localStorage.setItem("show_sidenav", true);-->
<!--}-->

<!--if (localStorage.getItem("show_sidenav")) openNav()-->
<!--</script>-->

### Ubuntu
{: #Ubuntu }

Some of the following guidelinesare taken from [opendrift.github.io/install](https://opendrift.github.io/install.html)

1. Install miniconda3: [docs.conda.io/en/latest/miniconda](https://docs.conda.io/en/latest/miniconda.html)  
2. Clone the OpenDrift fork related to _CLAWS_
    ```sh
    git clone https://github.com/vincentcasseau/opendrift.git
    cd opendrift
    ```
    <div style="line-height:50%;">
        <br>
    </div>    
3. Create environment with the required dependencies and install OpenDrift
    ```sh
    conda config --add channels conda-forge
    conda env create -f environment.yml
    conda activate opendrift
    pip install -e .
    ```
    <div style="line-height:50%;">
        <br>
    </div>  
4. Change directory to parent directory and clone _CLAWS_
    ```sh
    cd ../
    git clone https://github.com/vincentcasseau/CLAWS.git
    cd CLAWS
    ```
    <div style="line-height:50%;">
        <br>
    </div>
5. Add the _CLAWS_ Github repository folder to your python path in ~/.bashrc:   
    ```sh
    export PYTHONPATH=$PYTHONPATH:/path/to/CLAWS/Github/repository/
    ```
6. Source your ~/.bashrc file to complete the installation
    ```sh
    source ~/.bashrc
    ```
    <div style="line-height:50%;">
        <br>
    </div>

Please do not forget to load conda everytime you want to use _CLAWS_
    ```sh
    conda activate opendrift
    ```
