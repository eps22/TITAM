**This is an info file about the medicanes tracking algorithm developed in the MAR group of the University of Murcia in 2019. 
Authors: Enrique Pravia-Sarabia, Juan Pedro Montávez-Gómez and Juan José Gómez-Navarro.
Release date: January 2020.**


# PART I: TRACKING ALGORITHM

To run the tracking algorithm, you should go to the *Code* folder and type the following command in the console:

```console
user@machine:dir$ bash findmedicanes.sh PATHTOWRFOUTPUTNCFILE RESOLUTION TIMESTEP NCORES 
```

**PATHTOWRFOUTPUTNCFILE** represents the path to a WRF output nc file which contains all the fields, timesteps and vertical levels. **RESOLUTION** refers to the horizontal grid resolution -in km-, and **TIMESTEP** to the temporal resolution -in hours-. Path to file is to be introduced with no quotation marks, as well as resolution or timestep, which should both be numbers. **NCORES** is the number of cores to be used in the parallelized calculation of the medicanes track. It is an optional arguments that defaults to the machine total number of cores if argument is not passed, saving one core to prevent overloading. 

Algorithm parameters can be changed in the *FindMedicanes.namelist* file inside the TrackingAlgorithm folder. This file must be in this exact location when running the findmedicanes bash script. The *FindMedicanes.namelist.README* file contains detailed information about the meaning of each parameter, its possible values and its recommended/default value. 

Output of this tool is a list object of R -*.RData*- with the found centers. 

**Python version**: Python 3.6.8 (a correct working of pinterpy interpolation with previous python versions is expected but not guaranteed).
**Python requested libraries**:
   netCDF4
   wrf -> https://wrf-python.readthedocs.io/en/latest/ 
   ast
   numpy
   xarray

**R version**: R 3.6.1 (a correct installation of requested packages in previous R versions is not guaranteed).
**R requested packages**:
   ncdf4
   oce
     > install.packages('oce', dependencies=TRUE, repos='http://cran.rstudio.com/')
       or
     > install_github('dankelley/oce', ref='develop')
   
   foreach
   doParallel


# PART II: TRACK POSTPROCESSING.

There are two postprocessing tools provided in this package, one for producing a plot with the track, and one for obtaining further data on the track parameters.

## Plot of calculated track

To plot the calculated track -SEE PART I-, you should go to the *Code* folder and introduce the following command in the console:


```console
user@machine:dir$ bash plotmedicanestrack.sh PATHTOFOLDER ADJUST COMPLETE CONNECT SLPEXPANDN RESOLUTION DTHRESHOLD DTTHRESHOLD
```

**PATHTOFOLDER** must be the path to the folder created by the PART I. Inside that folder, it should be present the *-track.RData file -output of findmedicanes.sh-, along with a folder called 'output' containing the vertically interpolated files -slp, uvmet10 and z- in netcdf format. PATHTOFOLDER is to be introduced with no quotation marks. **ADJUST** should be one of TRUE/FALSE, and determines if the output map extent should be adjusted to the track, or the complete domain should be plotted instead. **COMPLETE** should be one of TRUE/FALSE, and determines if the track should be complemented with the SLP minimum path. **CONNECT** refers to the possibility of connecting track points trough a continuous line. SLPEXPANDN, RESOLUTION, DTHRESHOLD or DTTHRESHOLD should also be introduced as numbers. **SLPEXPANDN** is the number of track points that should be taken to expand the track at its beginning and ending. **RESOLUTION** represents the grid horizontal resolution in kilometers -the same as in the part I-; **DTHRESHOLD** refers to the maximum allowed distance between two track points to be connected -in km-, and a value of DTHRESHOLD below 200 km is highly recommended for physical reasons if timestep is 1 hour, 200km*timestep for other timestep values; **DTTHRESHOLD** is the maximum allowed time interval -in timesteps- between two points to be connected. A value of 1 or 2 is recommended for this last parameter. For example, with a value of 1 only consecutive points in time are connected. RESOLUTION, DTHRESHOLD and DTTHRESHOLD are optional arguments. If not provided, the plot won't have connected track dots, and only sparse points will be shown.   

Output of this tool is a pdf called trackplot.pdf, located inside the provided folder. 

## Obtain further data along calculated track

Go to the *Code* folder and type in the console the following command:

```console
user@machine:dir$ bash getmedicanestrackdata.sh PATHTOFOLDER TYPE
```

**PATHTOFOLDER** should be the path to the folder created in the PART I. Inside that folder, it should be present the *-track.RData file -output of findmedicanes.sh-, along with a folder called 'output' containing the vertically interpolated files -slp, uvmet10 and z- in netcdf format. PATHTOFOLDER is to be introduced with no quotation marks. **TYPE** should be 'reduced' or 'complete' -the quotation marks are allowed but not needed'. 

Output of this tool is a csv called trackingdf.csv, located inside the provided folder.

The 'reduced' TYPE value gives the following track parametersS:

**Date**:                              date and time in the format YY-MM-DD hh:mm:ss
**Timestep**:                          corresponding timestep to the date in the simulation
**x**:                                 coordinate of the center position in the longitudinal dimension
**Lon**:                               longitudinal coordinate of the center position -in degrees-
**y**:                                 coordinate of the center position in the latitudinal dimension
**Lat**:                               latitudinal coordinate of the center position -in degrees-
**center.slp.hPa**:                    SLP value at the center position -in hPa-

The 'complete' TYPE value gives the following track parameters:

**Date**:                              date and time in the format YY-MM-DD hh:mm:ss
**Timestep**:                          corresponding timestep to the date in the simulation
**x**:                                 coordinate of the center position in the longitudinal dimension
**Lon**:                               longitudinal coordinate of the center position -in degrees-
**y**:                                 coordinate of the center position in the latitudinal dimension
**Lat**:                               latitudinal coordinate of the center position -in degrees-
**center.slp.hPa**:                    SLP value at the center position -in hPa-
**inner.radius.km**:                   Medicane inner radius calculated as the distance from the center to the eyewall -max wind surface-
**outer.radius.km**:                   Medicane outer radius calculated as the mean distance from the center to the zero vorticity closed line points
**B.m**:                               B Hart thermal symmetry parameter value at the center position -in meters-
**LTW**:                               LTW Hart lower thermal wind parameter value at the center position
**UTW**:                               UTW Hart upper thermal wind parameter value at the center position
**max.inZVradius.wind.kmh**:           maximum wind speed value inside a circle with the zero vorticity radius around the center position -in km.h{-1}-
**center.wind.kmh**:                   wind speed value at the center position -in km.h{-1}-
**min.inZVradius.wind.kmh**:           minimum wind speed value inside a circle with the zero vorticity radius around the center position -in km.h{-1}-
**min.inZVradius.slp.x.position**:     x position of the minimum SLP value inside a circle with the zero vorticity radius around the center position -in km.h{-1}-
**min.inZVradius.slp.Lon**:            longitude of the minimum SLP value inside a circle with the zero vorticity radius around the center position -in km.h{-1}-
**min.inZVradius.slp.y.position**:     y position of the minimum SLP value inside a circle with the zero vorticity radius around the center position -in km.h{-1}-
**min.inZVradius.slp.Lat**:            latitude of the minimum SLP value inside a circle with the zero vorticity radius around the center position -in km.h{-1}-
**min.inZVradius.slp.hPa**:            minimum SLP value inside a circle with the zero vorticity radius around the center position -in hPa-
**minSLP.center.distance.km**:         distance between the min.inZVradius.slp.hPa position and the center position -in km-
**Saffir.Simpson.scale.category**:     storm category according to the Saffir-Simpson scale using the max.100km.wind.kmh wind speed 



