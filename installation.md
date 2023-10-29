---
layout: page
title: Installation
nav-short: true
--- 

### Ubuntu
{: #Ubuntu }

Some of the following guidelines are taken from [opendrift.github.io/install](https://opendrift.github.io/install.html)

1. Install miniconda3: [docs.conda.io/en/latest/miniconda](https://docs.conda.io/en/latest/miniconda.html)  
2. Clone the OpenDrift fork related to _CLAWS_
    ```sh
    git clone https://github.com/claws-scot/opendrift.git
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
    pip install --no-deps -e .
    ```
    <div style="line-height:50%;">
        <br>
    </div>  
4. Change directory to parent directory and clone _CLAWS_
    ```sh
    cd ../
    git clone https://github.com/claws-scot/CLAWS.git
    cd CLAWS
    pip install -r requirements.txt
    ```
    <div style="line-height:50%;">
        <br>
    </div>
5. Edit entries in CLAWS/etc/bashrc: `CLAWS_RUN` and `CLAWS_USER_DATA`  
6. Add the _CLAWS_ Github repository folder to your python path in ~/.bashrc:   
    ```sh
    source PATH_TO_CLAWS_PARENT_DIR/CLAWS/etc/bashrc
    export PATH=$PATH:PATH_TO_CLAWS_PARENT_DIR/CLAWS/bin
    export PYTHONPATH=$PYTHONPATH:PATH_TO_CLAWS_PARENT_DIR/CLAWS
    ```
    where `PATH_TO_CLAWS_PARENT_DIR` is the full path to CLAWS's parent directory.  
7. Source your ~/.bashrc file to complete the installation
    ```sh
    source ~/.bashrc
    ```
