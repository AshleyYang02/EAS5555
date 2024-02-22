# EAS5555 - SP24
GitHub Repository for EAS 5555 Numerical Techniques for Weather and Climate Modeling
We'll use markdown to write this file. Read more about Markdown here: https://www.markdownguide.org/basic-syntax/

# MATTHEW TUTORIAL

We are following the UCAR Hurricane Matthew tutorial: https://www2.mmm.ucar.edu/wrf/OnLineTutorial/
This gives us an introduction to running WRF on a local processor


## [0] Logging into Derecho

Type: ssh -y <username>@derecho.hpc.ucar.edu
Enter UCAR password and authenticate Duo push

## [1] Copy WRF and WPS Executables

Change directories to work directory by typing: cd /glade/work/wrfhelp/

> To check work, list servers by typing: ls

Change directories to pre-compiled code by typing: cd derecho_pre_compiled_code/

> List servers by typing: ls

Recursively copy the WRF version 4.1 directory to the home directory by typing: cp -rp wrfv4.1 ~/

> Weather Research and Forecasting (WRF) is the main modeling system for atmospheric research and numerical weather prediction

Recursively copy the WPS version 4.1 directory to the home directory by typing: cp -rp wpsv4.1 ~/

> WRF Preprocessing System (WPS) is a set of preprocessing tools/utilities used to prepare data for the WRF model (converts raw meteorological and geographical data to the required format)

## [2] Get Data

At the same level as the WRF and WPS directories, create a new directory named DATA by typing: mkdir DATA
Download the data relevant to the model

> https://www2.mmm.ucar.edu/wrf/TUTORIAL_DATA/matthew_1deg.tar.gz

Open a new terminal window to move the data from its local directory to Derecho by typing: scp /Users/<PC_Username>/downloads/<filename> <Derecho_username>@derecho.hpc.ucar.edu:/glade/u/home/<Derecho_username>/DATA

> Follow the prompts as needed
>
> List servers by typing: ls

Return to Linux terminal and unpack the tar file by typing: tar -xf matthew_1deg.tar.gz

> List servers by typing: ls

Change the directory to access the WPS directory

> Type: cd
>
> Type: cd wpsv4.1
> 
> List servers by typing: ls

Run g2print on one of the datafiles to become familiarized with the data by typing:

> ./util/g2print.exe ../DATA/matthew/<filename> >& g2print.log
> 
> ./util/g2print.exe ../DATA/matthew/fnl_20161006_00_00.grib2 >& g2print.log

## [3] Link Vtable

Link in the GFS Vtable by typing: ln -sf ungrib/Variable_Tables/Vtable.GFS Vtable

> This creates a symbolic link and provides details about the variables

To explore fields in the ungib/Variable_Tables/Vtable.GFS file, type: emacs Vtable

> To close the window, hit Ctrl + X, then Ctrl + C

## [4] Run link_grib.csh

Link in the GRIB data by typing: ./link_grib.csh ../DATA/matthew/fnl

> List servers by typing: ls
> 
> If successful, the directory should contain the files below:
>
>>     GRIBFILE.AAA -> ../DATA/matthew/fnl_20161006_00_00.grib2
>>
>>     GRIBFILE.AAB -> ../DATA/matthew/fnl_20161006_06_00.grib2
>>
>>     GRIBFILE.AAC -> ../DATA/matthew/fnl_20161006_12_00.grib2
>>
>>     GRIBFILE.AAD -> ../DATA/matthew/fnl_20161006_18_00.grib2
>>
>>     GRIBFILE.AAE -> ../DATA/matthew/fnl_20161007_00_00.grib2
>>
>>     GRIBFILE.AAF -> ../DATA/matthew/fnl_20161007_06_00.grib2
>>
>>     GRIBFILE.AAG -> ../DATA/matthew/fnl_20161007_12_00.grib2
>>
>>     GRIBFILE.AAH -> ../DATA/matthew/fnl_20161007_18_00.grib2
>>
>>     GRIBFILE.AAI -> ../DATA/matthew/fnl_20161008_00_00.grib2

## [5] Edit namelist.wps

Edit namelist.wps by typing: emacs namelist.wps

> Set the following:
>
>> max_dom = 1
>>
>> start_date = '2016-10-06_00:00:00',
>>
>> end_date = '2016-10-08_00:00:00',
>>
>> interval_seconds = 21600,
>>
>> prefix = 'FILE',
>>
> To save changes to the file, hit Ctrl + X, then Ctrl + C

## [6] Run ungrib.exe

Run ungrib by typing: ungrib.exe

> This creates the following intermediate files:
>
>> FILE:2016-10-06_00
>>
>> FILE:2016-10-06_06
>>
>> FILE:2016-10-06_12
>>
>> FILE:2016-10-06_18
>>
>> FILE:2016-10-07_00
>>
>> FILE:2016-10-07_06
>>
>> FILE:2016-10-07_12
>>
>> FILE:2016-10-07_18
>>
>> FILE:2016-10-08_00
>>
> Check that the log file ungrib.log has all the same fields as vtable by typing: emacs ungrib.log
>
> Get familiarized with intermediate files by typing: ./util/rd_intermediate.exe FILE:2016-10-06_00

## [7] Edit namelist.wps (again)

Edit namelist.wps by typing: emacs namelist.wps

> Make the following changes:
>
>> max_dom = 1
>> 
>> parent_id = 1,
>> 
>> parent_grid_ratio = 1,
>> 
>> i_parent_start = 1,
>> 
>> j_parent_start = 1,
>> 
>> e_we = 91,
>> 
>> e_sn = 100,
>> 
>> geog_data_res = 'default',
>> 
>> dx = 27000,
>> 
>> dy = 27000,
>> 
>> map_proj = 'mercator',
>> 
>> ref_lat = 28.00,
>> 
>> ref_lon = -75.00,
>> 
>> truelat1 = 30.0,
>> 
>> truelat2 = 60.0,
>> 
>> stand_lon = -75.0,
>> 
>> geog_data_path = 'Your WPS_GEOG data location'
>
> To save changes to the file, hit Ctrl + X, then Ctrl + C

## [8] Run geogrid.exe

Run geogrid by typing: ./geogrid.exe

> If successful, the following message should appear:
>
>>     !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
>>
>>     ! Successful completion of geogrid.         !
>>
>>     !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

## [9] Run metgrid.exe

Run metgrid.exe to interpolate the input data on the model (WRF) domain by typing: /metgrid.exe

> If successful, the following message should appear:
>
>>     !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
>>
>>     ! Successful completion of metgrid.         !
>>
>>     !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
>> 
> The following files should have been created in the WPS directory:
> 
>> met_em.d01.2016-10-06_00:00:00.nc
>> 
>> met_em.d01.2016-10-06_06:00:00.nc
>>
>> met_em.d01.2016-10-06_12:00:00.nc
>>
>> met_em.d01.2016-10-06_18:00:00.nc
>>
>> met_em.d01.2016-10-07_00:00:00.nc
>>
>> met_em.d01.2016-10-07_06:00:00.nc
>>
>> met_em.d01.2016-10-07_12:00:00.nc
>>
>> met_em.d01.2016-10-07_18:00:00.nc
>>
>> met_em.d01.2016-10-08_00:00:00.nc

## [10] Prepare to run wrf.exe

Go to the test/em_real directory

> Return to the work directory by typing: ..
> 
> Change directories to the wrf directory by typing: cd wrfv4.1
> 
> Change directories to the test/em_real/ directory by typing: cd test/em_real
> 
> List servers as needed by typing: ls

Link in the files created with metgrid.exe in the WPS directory to the WRF directory by typing: ln -sf ../../../wpsv4.1/met_em.d01.2016-10* .

Edit namelist.input by typing: emacs namelist.input

> Make the following changes:
> 
>> run_days = 0,
>> 
>> run_hours = 48,
>>
>>  run_minutes = 0,
>>
>> run_seconds = 0,
>>
>> start_year = 2016,
>>
>> start_month = 10,
>>
>> start_day = 06,
>>
>> start_hour = 00,
>>
>> end_year = 2016,
>>
>> end_month = 10,
>>
>> end_day = 08,
>>
>> end_hour = 00,
>>
>> interval_seconds = 21600
>>
>> input_from_file = .true.,
>>
>> history_interval = 180,
>>
>> frames_per_outfile = 1,
>>
>> restart = .false.,
>>
>> restart_interval = 1440,
>>
>> time_step = 150,
>>
>> max_dom = 1,
>>
>> e_we = 91,
>>
>> e_sn = 100,
>>
>> e_vert = 45,
>>
>> num_metgrid_levels = 32
>>
>> dx = 27000,
>>
>>  dy = 27000,
>> 
> To save changes to the file, hit Ctrl + X, then Ctrl + C

Run real.exe by typing: ./real.exe

> Check that the following two files have been created:

>> wrfinput_d01
>> 
>> wrfbdy_d01
>> 
After running, check that the program ran correctly by typing the linux command: cat rsl.error.0000 

> If successful, the following should appear at the bottom of the output:
> 
>> ...
>>
>> 
>>  Assume Noah LSM input
>>
>> d01 2016-10-08_00:00:00 forcing artificial silty clay loam at    9 points, out of   8910
>>
>> d01 2016-10-08_00:00:00 Timing for processing          0 s.
>>
>> d01 2016-10-08_00:00:00 Timing for output          0 s.
>>
>> d01 2016-10-08_00:00:00 Timing for loop #    9 =          0 s.
>>
>> d01 2016-10-08_00:00:00 real_em: SUCCESS COMPLETE REAL_EM INIT
>>

## Run wrf.exe

Run wrf.exe by typing: ./wrf.exe

> If successful, the following should appear:
> 
>> wrfout_d01_2016-10-06_00:00:00
>> 
>> wrfout_d01_2016-10-06_03:00:00
>> 
>> wrfout_d01_2016-10-06_06:00:00
>>
>> wrfout_d01_2016-10-06_09:00:00
>>
>> wrfout_d01_2016-10-06_12:00:00
>>
>> wrfout_d01_2016-10-06_15:00:00
>>
>> wrfout_d01_2016-10-06_18:00:00
>>
>> wrfout_d01_2016-10-06_21:00:00
>>
>> wrfout_d01_2016-10-07_00:00:00
>>
>> wrfout_d01_2016-10-07_03:00:00
>>
>> wrfout_d01_2016-10-07_06:00:00
>>
>> wrfout_d01_2016-10-07_09:00:00
>> 
>> wrfout_d01_2016-10-07_12:00:00
>>
>> wrfout_d01_2016-10-07_15:00:00
>>
>> wrfout_d01_2016-10-07_18:00:00
>>
>> wrfout_d01_2016-10-07_21:00:00
>>
>> wrfout_d01_2016-10-08_00:00:00
>>
>> wrfrst_d01_2016-10-07_00:00:00
>>
>> wrfrst_d01_2016-10-08_00:00:00

check the contents of the wrfout file

> Use the ncdump utility:
>
>> ncdump -h wrfout_d01_2016-10-06_00:00:00
>> 
>> ncdump -v Times wrfout_d01_2016-10-06_00:00:00 (to see which forecast times are in the file)
>> 
>> Note: run_hours was set to 48, so there should only be 48 hours of output
>
> Use the ncview utility.
>

Display the model output using graphical tools


