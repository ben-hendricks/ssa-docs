# Installation

## Install Anaconda

Anaconda is used to facilitate the majority of library installs required for Hydra. To install Anaconda download the graphical macOS installer for your Mac system (Intel, or M1/M2) following this link:

```http
https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html
```

## Create Python Environment

Hydra will be executed from a dedicated Python environment. The current version of Hydra is built and tested in Python 3.10.4[^1]. To create the appropriate Python Environment using the terminal follow these steps: 

[^1]: Other python versions higher than 3.10 may work too but have not been tested.


```sh title="Update Anaconda"

conda update -n base -c defaults conda
```


```sh title='Create Python Environment'
conda create --name SSP3 python=3.10.4
```

???+ note

	Hydra and Tuna share the same Python environment and libraries. The SSP3 environment is created only once and used for both programs.


## Install Python Libraries

Most of the required libraries for Hydra are installed using the `pip install *library*` command. There are two exceptions: the Basemap library and the Matplotlib-legacycontour library.

First, activate the proper Python environment:

```sh
conda activate SSP3
```

Install the following list of libraries using the standard pip procedure:

```sh
pip install pandas==2.0.1
pip install numpy==1.24.3
pip install scipy==1.10.1
pip install soundfile==0.12.1
pip install scikit-image==0.19.3
pip install datetime==5.1
pip install tinytag==1.9.0
pip install apscheduler==3.10.1
pip install utm==0.7.0
pip install tqdm==4.65.0
pip install shapely==2.0.1
pip install geos==0.2.3
pip install ipython==8.13.2
pip install pyproj==3.5.0
pip install matplotlib==3.7.1
pip install scikit-learn==1.1.2
pip install customtkinter==4.6.3
```



### The Basemap Library

Hydra currently requires the Basemap library for all mapping elements. Basemap is not a Python core library and requires different installation procedures on different systems.
  

#### Mac OS X: Catalina & Processor: Intel

```sh
conda install basemap
```  
  
  

#### Mac OS X: Ventura & Processor: Intel

```sh
pip install basemap
```

then
```sh
conda install -c conda-forge basemap-data-hires
```
  

#### Mac OS X: Ventura & Processor: M1/M2

```sh
conda install wget
```

then
```sh
wget http://download.osgeo.org/geos/geos-3.3.8.tar.bz2
```

then, move to geos directory and extract:

```sh
tar -xvf geos-3.3.8.tar.bz2
```

then move into extracted geos directory and install:

```sh
./configure
make
sudo make install
```

then install Basemap with pip

```sh
pip install Basemap
```

then install separate high-resolution Basemap material

```sh
conda install -c conda-forge basemap-data-hires
```

done.

### The Matplotlib-legacycontour Library

Matplotlib-legacycontour is an outdates library that has been removed from more recent versions of matplotlib. It has to be installed seperately from the GitHub repository:

```sh
pip install git+https://github.com/matplotlib/legacycontour
```
