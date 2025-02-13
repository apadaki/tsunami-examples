# 2015 Illapel Earthquake Report

This example contains code for running a simulation of the tsunami generated by the 2015 Illapel earthquake on the Chilean coast.

## The Earthquake

*Sources: USGS (https://earthquake.usgs.gov/earthquakes/eventpage/us20003k7a/executive), NOAA (https://www.ngdc.noaa.gov/hazard/16sep2015.html)*

On September 16, 2015, an earthquake with a magnitude of 8.3 occurred on the boundary between the Nazca and South America tectonic plates, north of the rupture zone from the 2010 Chile earthquake. It produced significant aftershocks, some of which exceeded magnitude 7.0, and it was reported to have caused 15 deaths and 14 injuries.

In this example, we model the tsunami resulting from the earthquake. We validate our model using data from two tide gauges in the region.

## Topography/Bathymetry Data

*Topography data was sourced from the ETOPO 10-arc-minute model (Esri ASCII format) here: http://depts.washington.edu/clawpack/geoclaw/topo/etopo/etopo10min120W60W60S0S.asc*

In the example, [maketopo.py](maketopo.py) downloads the data directly from source into the scratch directory within GeoClaw. 

```python
# maketopo.py, lines 26-27
url = 'http://depts.washington.edu/clawpack/geoclaw/topo/etopo/' + topo_fname
clawpack.clawutil.data.get_remote_file(url, output_dir=scratch_dir, file_name=topo_fname, verbose=True)
```

If you want to run a simulation with more refined topography, simply download the file into the scratch directory (or edit the download URL) and edit the appropriate filenames within [maketopo.py](maketopo.py) and [setrun.py](setrun.py). If the geographical bounds or topography file format are different, note that these parameters must be changed in [setrun.py](setrun.py).

## Moving Topography Data

*Moving topography (dtopo) data was sourced from the USGS finite fault model for the earthquake here: https://earthquake.usgs.gov/product/finite-fault/us20003k7a/us/1539809967421/basic_inversion.param*

As with topography data, moving topography (dtopo) data is directly downloaded. 

```python
# maketopo.py, lines 57-59
param_fname = 'basic_inversion.param'
csv_fname = 'chile2015.csv'
get_csv('https://earthquake.usgs.gov/product/finite-fault/us20003k7a/us/1539809967421/' + param_fname, param_fname, csv_fname)
```
In order to produce a CSV file of the subfaults readable by the `clawpack.geoclaw.dtopotools` module, the data file is reformatted in [get_dtopo_csv.py](get_dtopo_csv.py). Therefore, to use different finite fault data, edit this file alongside the download URL in [maketopo.py](maketopo.py) (and potentially the file format in [setrun.py](setrun.py)).

## GeoClaw Results

The simulation was set to run 5 hours from the origin time of the earthquake, with a maximum of 6 adaptive mesh refinement layers. Areas of high refinement were manually implemented around tide gauges. Surface plots and tide gauge plots are generated by [setplot.py](setplot.py), and they show predicted tide residuals ranging from -0.075 to +0.125. Overall, these values match NOAA tide data relatively well.

## Validation Results

*Tide data was downloaded directly from the NOAA database for the earthquake here: https://www.ngdc.noaa.gov/hazard/dart/2015chile.html*

We test our simulation against tide residual values at two DART stations: gauge no. 32412 (SW of Lima, Peru) and gauge no. 32402 (W of Caldera, Chile). The simulation matched the residual patterns at the closer gauge (32402) relatively well but those at farther gauge (32412) relatively poorly.

For additional testing, one can add more gauges for plotting. To do so, the download must be specified in [maketopo.py](maketopo.py) and the plotting in [setplot.py](setplot.py).

## Conclusion

Topography and moving topography data for the 2015 Illapel earthquake were obtained from the internet and used by GeoClaw to run a simulation of the regional sea surface. Tide residuals were compared with observed NOAA data and demonstrated good local predictiveness but poor faraway predictiveness. Better topography data and adaptive mesh refinement customization may yield improved results. More tide gauges may provide a better picture of the simulation's predictive scope.

## Installation

Make sure to have the following dependencies downloaded in order to run this example:
- [Clawpack](https://www.clawpack.org/installing.html)
- [pandas](https://pandas.pydata.org/)

## Usage

Make sure you have GNU Make installed, and run the following terminal commands inside of the `illapel2015_chile` directory:

```
make clobber
make .data
make xgeoclaw
make .output
make .plots
```

To optimize simulation speed, consider [setting OpenMP compiler flags for multiprocessing](https://www.clawpack.org/openmp.html).

---

For questions, please contact Ashwin Padaki at ap4025@columbia.edu.

