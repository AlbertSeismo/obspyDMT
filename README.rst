==========================================================================================
obspyDMT: Parallel Retrieving, Processing and Management of Massive Seismological Datasets
==========================================================================================


Welcome!

obspyDMT_ (ObsPy Data Management Tool) is a command line tool for retrieving, processing and management of massive seismic data in a fully automatic way which could be run in serial or in parallel. 
Moreover, complementary processing and managing tools have been designed and introduced in addition to the obspyDMT options.

This tool is developed to mainly address the following tasks automatically: 

1. Retrieval of waveforms (MSEED or SAC), response files and metadata from IRIS_ and ORFEUS_ (via ArcLink_) archives. This could be done in *serial* or in *parallel* for single or large requests.
2. Supports event-based and continuous requests.
3. Extracting the information of all the events via user-defined options (time span, magnitude, depth and event location) from IRIS_ and EMSC_ (European Mediterranean Seismological Centre).
4. Updating the existing archives (waveforms, response files and metadata).
5. Processing the data in *serial* or in *parallel* (e.g. *Tapering, removing the trend of the time series, filtering and Instrument correction*).
6. Management of large seismic datasets.
7. Plotting tools (events and/or station locations, Ray coverage (event-station pair), epicentral-distance plots for all archived waveforms and seismicity maps).


This tutorial has been divided into the following sections: 

1.  `How to cite obspyDMT`_
2.  `Lets get started`_: install obspyDMT and check your local machine for required dependencies.
3.  `Quick tour`_: run a quick tour for obspyDMT.
4.  `Option types`_: there are two types of options for obspyDMT: *option-1* (with value) and *option-2* (without value)
5.  `event-info request`_: if you are looking for some events and you want to get info about them without downloading waveforms.
6.  `event-based request`_: retrieve the waveforms, response files and meta-data of all the requested stations for all the events found in the archive.
7.  `continuous request`_: retrieve the waveforms, response files and meta-data of all the requested stations for the specified time span.
8.  `Update`_: if you want to continue an interrupted request or complete your existing archive.
9.  `Geographical restriction`_: if you are interested in the events happened in a specific geographical coordinate and/or retrieving the data from the stations in a specific circular or rectangular bounding area.
10.  `Instrument correction`_: instrument correction for displacement, velocity and acceleration with full response file or Poles And Zeros *(PAZ)*.
11.  `Parallel retrieving and processing`_: send the request and/or process the data in parallel. This section introduces some options (*bulkdataselect* and *parallel retrieving and processing*) to speed-up the whole procedure.
12.  `Plot`_: for an existing folder, you could plot all the events and/or all the stations, ray path for event-station pairs and epicentral-distance/time for the waveforms using GMT-5 or basemap tools.
13. `Seismicity`_: plot the geographical and historical distribution of earthquake activities (seismicity).
14. `Folder structure`_: the way that obspyDMT organize your retrieved and processed data in the file-based mode.
15. `Available options`_: all options currently available in obspyDMT.

--------------------
How to cite obspyDMT
--------------------

If you use obspyDMT, please consider citing the code as:

::
    Kasra Hosseini (2013), obspyDMT (Version 0.5.0) [software] [https://github.com/kasra-hosseini/obspyDMT]

We have also published a paper in SRL (Seismological Research Letters) for obspyDMT's predecessor that we kindly ask you to cite in case that you found obspyDMT useful for your research:

::

    C. Scheingraber, K. Hosseini, R. Barsch, and K. Sigloch (2013), ObsPyLoad - a tool for fully automated retrieval of seismological waveform data, Seismological Research Letters, 84(3), 525-531, DOI:10.1785/0220120103.

-----------------
Lets get started
-----------------

Once a working Python and ObsPy_ environment is installed, there are two possible ways to have obspyDMT:
 
1. Manually from the source code:

::

    clone the obspyDMT git repository (or fork obspyDMT in GitHub and clone your fork):

::
    
    $ git clone https://github.com/kasra-hosseini/obspyDMT.git /path/to/my/obspyDMT
    $ cd /path/to/my/obspyDMT
    $ python setup.py install

Alternatively:

::
    
    $ git clone https://github.com/kasra-hosseini/obspyDMT.git /path/to/my/obspyDMT
    $ cd /path/to/my/obspyDMT
    $ pip install -v -e .

2. Prepackaged Modules from Python Package Index (PyPI):

::
    
    $ easy_install -N obspyDMT

or using *pip*:

::
    
    $ pip install obspyDMT
 
In case that none of these worked for you, the source code could be downloaded directly from either PyPI_ or GitHub_ websites and you can either work with the source code or install it:

::
    
    $ cd /path/to/my/obspyDMT
    $ python setup.py install

obspyDMT can be used from a system shell without explicitly calling the *Python* interpreter. It contains various options for customizing the request. Each option has a reasonable default value and the user can change them to adjust obspyDMT options to a specific request. The following command gives all the available options with their default values:

::

    $ obspyDMT --help

To check the dependencies required for running the code properly:

::

    $ obspyDMT --check

**ATTENTION:** if obspyDMT is installed on your machine, it could be easily run from everywhere. However, if you want to use the source code instead:

::

    $ cd /path/to/my/obspyDMT.py
    $ ./obspyDMT.py --check

*--check* flag checks for the basic requirements to run the code. However, some special options in obspyDMT are built upon other packages:

**Parallelization:** There are some options in obspyDMT in which the job will be distributed on several processes (refer to `Parallel retrieving and processing`_). Here, pprocess_ is used and should be installed beforehand.

In all the following examples, we assume that obspyDMT is already installed.

----------
Quick tour
----------

To run a quick tour for obspyDMT:

::

    $ obspyDMT --tour

*DMT-Tour-Data* directory will be created in the current path and the retrieved/processed data will be organized there. (Please refer to `Folder structure`_ section for more information)

The retrieved raw counts could be plotted:

::

    $ obspyDMT --plot_epi 'DMT-Tour-Data'

.. image:: figures/tourepiraw.png
   :scale: 60%
   :align: center

for plotting the corrected waveforms:

::

    $ obspyDMT --plot_epi 'DMT-Tour-Data' --plot_type corrected

.. image:: figures/tourepicorrected.png
   :scale: 60%
   :align: center

obspyDMT plots the ray coverage (ray path between each event-station pair) by:

::

    $ obspyDMT --plot_ray 'DMT-Tour-Data'
   
.. image:: figures/tourray.png
   :scale: 75%
   :align: center
    
------------
Option types
------------

There are two types of options in obspyDMT: option-1 (with value) and option-2 (without value). In the first type, user should provide value/s which will be stored and be used in the program as input. However, by adding type-2 options, which does not require any value, one feature will be activated or deactivated (e.g. if you enter '--check', refer to `Lets get started`_ section, the program will check all the dependencies required for running the code properly).

The general form to enter the input (i.e. change the default values) is as follow:

::

    $ obspyDMT --option-1 'value' --option-2

To show all the available options with short descriptions:

::

    $ obspyDMT --help 

.. or refer to the `Available options`_ section in this tutorial in which the options marked with '*' are the first option type (option-1), and the options marked with '**' are the second type (option-2).

The options specified by *--option=OPTION* are type-1 (with value) and *--option* are type-2 (without value).

**ONE GOOD THING:** the order of options is commutative!

------------------
event-info request
------------------

In this type of request, obspyDMT will search for all the available events based on the options specified by the user, print the results and create an event catalogue.

The following lines show how to send an `event-info request`_ with obspyDMT and present some examples.

The general way to define an `event-info request`_ is:

::

    $ obspyDMT --event_info --option-1 'value' --option-2

The *--event_info* flag forces the code to just retrieve the event information and create an event catalog.
For details on *option-1* and *option-2* please refer to `Option types`_ section.

**Example 1:** run with the default values:

::

    $ obspyDMT --event_info

When the job starts, a folder will be created with the address specified for *--datapath* flag (by default: *obspyDMT-data* in the current directory). To access the event information for this example, go to */path/specified/in/datapath/2013-01-27_2013-02-01_5.5_9.9/EVENT* [the folder names will change based on your request] and check the *EVENT-CATALOG* text file (Please refer to `Folder structure`_ section for more information)

**Example 2:** by adding flags to the above command, one can change the default values and add/remove functionalities of the code. As an example, the following command shows how to get the info of all the events with magnitude more than Mw 7.0 occured after 2011-03-01 and before 2012-03-01:

::
    
    $ obspyDMT --event_info --min_mag '7.0' --min_date '2011-03-01' --max_date '2012-03-01'

-------------------
event-based request
-------------------

In this type of request, the following steps will be done automatically:

1. Search for all available events based on the options specified by the user.
2. Check the availability of the requested stations for each event.
3. Start to retrieve the waveforms and/or response files for each event and for all available stations. (default: waveforms, response files and metadata will be retrieved.)
4. Instrument correction to all saved waveforms based on the specified options.

Retrieving and processing could be done in **serial** or in **parallel**.

The following lines show how to send an `event-based request`_ with obspyDMT and present short examples.

The general way to define an `event-based request`_ is:

::

    $ obspyDMT --option-1 'value' --option-2

For details on *option-1* and *option-2* please refer to `Option types`_ section.

**Example 1:** to test the code with the defualt values run:

::

    $ obspyDMT --test '20'

if you take away the option *--test '20'*, the default values could result in a huge amount of requests. This option set the code to send *20* requests to IRIS and ArcLink which is suitable for testing.

When the job starts, a folder will be created with the address specified for *--datapath* flag (by default: *obspyDMT-data* in the current directory). [refer to `Folder structure`_ section]

**Example 2:** by adding flags to the above command, one can change the default values and add/remove functionalities of the code. As an example, the following commands show how to get all the waveforms, response files and metadata of *BHZ* channels available in *TA* network with station names start with *Z* for the great Tohoku-oki earthquake of magnitude Mw 9.0:

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --identity 'TA.Z*.*.BHZ'

or instead of using *identity* option:

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --net 'TA' --sta 'Z*' --cha 'BHZ'

In the case that you know from which data provider you want to retrieve the data, it is better to exclude the non-relevant one. For instance, in this example since we know that *TA* network is within IRIS, it makes more sense to exclude ArcLink by:

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --identity 'TA.Z*.*.BHZ' --arc 'N'

**Example 3:** By default, obspyDMT saves the waveforms in *SAC* format. In this case, it will fill out the station location (stla and stlo), station elevation (stel), station depth (stdp), event location (evla and evlo), event depth (evdp) and event magnitude (mag) in the SAC headers. However, if the desired format is *MSEED*: (for downloading the same event and station identity as *Example 2*)

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --identity 'TA.Z*.*.BHZ' --arc 'N' --mseed

**Example 4:** for downloading just the raw waveforms without response file and instrument correction:

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --identity 'TA.Z*.*.BHZ' --arc 'N' --mseed --response 'N' --ic_no

**Example 5:** the default values for the preset (how close the time series (waveform) will be cropped before the origin time of the event) and the offset (how close the time series (waveform) will be cropped after the origin time of the event) are 0 and 1800 seconds. You could change them by adding the following flags:

::

    $ obspyDMT --preset time_before --offset time_after --option-1 value --option-2 

------------------
continuous request
------------------

In this type of request, the following steps will be done automatically:

1. Get the time span from input and in case of large time spans, divide it into small intervals.
2. Check the availability of the requested stations for each interval.
3. Start to retrieve the waveforms and/or response files for each interval and for all the available stations. (default: waveforms, response files and metadata will be retrieved.)
4. Instrument correction to all saved waveforms based on the specified options.
5. Merging the retrieved waveforms for all time intervals to get the original input time span and save the final product.

The following lines show how to send a `continuous request`_ with obspyDMT and present short examples.

The general way to define a `continuous request`_ is:

::

    $ obspyDMT --continuous --option-1 value --option-2

For details on *option-1* and *option-2* please refer to `Option types`_ section.

**Example 1:** to test the code with the defualt values run:

::

    $ obspyDMT --continuous --test '20'

if you take away the option *--test '20'*, the default values could result in a huge amount of requests. This option set the code to send *20* requests to IRIS and ArcLink which is suitable for testing.

When the job starts, a folder will be created with the address specified for *--datapath* flag (by default: *obspyDMT-data* in the current directory). [refer to `Folder structure`_ section]

**Example 2:** by adding flags to the above command, one can change the default values and add/remove functionalities of the code. As an example, the following command lines show how to get all the waveforms, response files and metadata of the *BHZ* channels available in *TA* network with station names start with *Z* for the specified time span:

::

    $ obspyDMT --continuous --identity 'TA.Z*.*.BHZ' --min_date '2011-01-01' --max_date '2011-01-03'

or instead of using *identity* option:

::

    $ obspyDMT --continuous --net 'TA' --sta 'Z*' --cha 'BHZ' --min_date '2011-01-01' --max_date '2011-01-03'

In the case that you know from which data provider you want to retrieve the data, it is better to exclude the non-relevant one. For instance, in this example since we know that *TA* network is within IRIS, it makes more sense to exclude ArcLink by:

::

    $ obspyDMT --continuous --identity 'TA.Z*.*.BHZ' --min_date '2011-01-01' --max_date '2011-01-03' --arc 'N' 

**Example 3:** By default, obspyDMT saves the waveforms in *SAC* format. In this case, it will fill out the station location (stla and stlo), station elevation (stel), station depth (stdp), event location (evla and evlo), event depth (evdp) and event magnitude (mag) in the SAC headers. However, if the desired format is *MSEED*: (for downloading the same event and station identity as *Example 2*)

::

    $ obspyDMT --continuous --identity 'TA.Z*.*.BHZ' --min_date '2011-01-01' --max_date '2011-01-03' --arc 'N' --mseed

**Example 4:** for downloading just the raw waveforms without response file and instrument correction:

::

    $ obspyDMT --continuous --identity 'TA.Z*.*.BHZ' --min_date '2011-01-01' --max_date '2011-01-03' --arc 'N' --mseed --response 'N' --ic_no

------
Update
------

If you want to continue an interrupted request or complete your existing archive, you could use the updating option. The general ways to update an existing folder (located in *address*) for IRIS stations, ArcLink stations or both are:

::

    $ obspyDMT --iris_update 'address' --option-1 value --option-2
    $ obspyDMT --arc_update 'address' --option-1 value --option-2
    $ obspyDMT --update_all 'address' --option-1 value --option-2

Please note that all the commands presented in this section could be applied to `continuous request`_ by just adding *--continuous* flag to the command line (refer to the `continuous request`_ section).

**Example 1:** first, lets retrieve all the waveforms, response files and metadata of *BHZ* channels available in *TA* network with station names start with *Z* for the great Tohoku-oki earthquake of magnitude Mw 9.0:

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --identity 'TA.Z*.*.BHZ' --arc 'N'

now, we want to update the folder for *BHE* channels:

::

    $ obspyDMT --update_all './obspyDMT-data' --identity 'TA.Z*.*.BHE'

------------------------
Geographical restriction
------------------------

If you are interested in the events happened in a specific geographical coordinate and/or retrieving the data from the stations in a specific circular or rectangular bounding area, you are in the right section! Here, we have two examples:

**Example 1:** to extract the info of all the events occured in 2010 in a rectangular area (*lon1=44.38E* *lon2=63.41E* *lat1=24.21N* *lat2=40.01N*) with magnitude more than 3.0 and maximum depth of 80 km:

::

    $ obspyDMT --event_info --min_mag '3.0' --max_depth '-80.0' --min_date '2010-01-01' --max_date '2011-01-01' --event_rect '44.38/63.41/24.21/40.01'

**Example 2:** to retrieve all the waveforms, response files and metadata of *BHZ* channels available in a specified rectangular bounding area (*lon1=125.0W* *lon2=70.0W* *lat1=25N* *lat2=45N*) for the great Tohoku-oki earthquake of magnitude Mw 9.0, the command line will be:

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --cha 'BHZ' --station_rect '-125.0/-70.0/25.0/45.0'

---------------------
Instrument correction
---------------------

When obspyDMT retrieves waveforms and their response files, by default it tapers the waveforms, removes the trends of time series, filters and corrects them to the desired physical scales (Displacement, Velocity or Acceleration). The default correction unit is Displacement and to change it into Velocity or Acceleration:

::

    $ obspyDMT --corr_unit 'VEL' --option-1 'value' --option-2
    $ obspyDMT --corr_unit 'ACC' --option-1 'value' --option-2

where *option-1* and *option-2* are the flags defined by the user (see `Option types`_ section).

You could deactive the instrument correction functionallity by:

::

    $ obspyDMT --ic_no --option-1 value --option-2

Please note that all the commands presented in this section could be applied to `continuous request`_ by just adding *--continuous* flag to the command line (refer to `continuous request`_ section).

Before applying the instrument correction, a bandpass filter will be applied to the data with default values: *(0.008, 0.012, 3.0, 4.0)*. If you want to apply another band pass filter:

::

    $ obspyDMT --pre_filt '(f1,f2,f3,f4)' --option-1 value --option-2

where *(f1,f2,f3,f4)* are the four corner frequencies of a cosine taper, one between f2 and f3 and tapers to zero for f1 < f < f2 and f3 < f < f4.

If you do not need the pre filter:

::

    $ obspyDMT --pre_filt 'None' --option-1 value --option-2

In case that you want to apply instrument correction to an existing folder:

::

    $ obspyDMT --ic_all 'address' --corr_unit unit

here *address* is the path where your not-corrected waveforms are stored.
as mentioned above, *unit* is the unit that you want to correct the waveforms to. It could be *DIS* (default), *VEL* or *ACC*.

To make it clearer, let's take a look at an example with following steps:

**Step 1:** to retrieve all the waveforms, response files and metadata of *BHZ* channels available in *TA* network with station names start with *Z* for the great Tohoku-oki earthquake of magnitude Mw 9.0: (please note that instrument correction will be applied to the retrieved waveforms by default)

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --identity 'TA.Z*.*.BHZ' --arc 'N'

**Step 2:** now to correct the raw waveforms for velocity:

::

    $ obspyDMT --ic_all '/path/specified/in/datapath' --corr_unit 'VEL'

----------------------------------
Parallel retrieving and processing
----------------------------------

For each download request, obspyDMT uses ObsPy_ clients to establish connection to the data-centers, sends the request, downloads the data and disconnect. Some modifications could be applied to enhance the whole procedure:

**bulkdataselect**

bulkdataselect_ is a method provided by IRIS which gives access to multiple channels of *MSEED* data for specified time ranges, i.e. instead of sending the requests one by one, a list of requests can be sent.

obspyDMT incorporates this option and it can be activated:

::

    $ obspyDMT --iris_bulk --option-1 'value' --option-2

**Parallel retrieving and processing**

Moreover, obspyDMT can send the requests in parallel which makes the whole procedure much more efficient. In this case, the request (event-based or continuous) will be divided into the number of requested processes. Each process sends the request to the data providers, retrieves and organizes the data. The general syntax for this option is:

::

    $ obspyDMT --req_parallel --req_np 10 --option-1 'value' --option-2

*--req_parallel* means that the request should be sent in parallel and *--req_np 10* specifies the number of requested processes which is *10* here.

**ATTENTION**: *bulkdataselect* and parallel options can be combined as well:

::

    $ obspyDMT --iris_bulk --req_parallel --req_np 10 --option-1 'value' --option-2

In this case, obspyDMT checks the number of requested channels and if the number is more than *1000*, obspyDMT will automatically divide it into smaller groups and send them in parallel.

obspyDMT can run the processing unit in parallel as well. In this mode, it divides the job into the number of requested processes and each of them performs the instrument correction or any other defined processes and stores the results. Syntax to activate this option is:

::

    $ obspyDMT --ic_parallel --ic_np 10 --option-1 'value' --option-2

*--ic_parallel* means that the processing should be done in parallel and *ic_np 10* specifies the number of requested processes which is *10* here.

----
Plot
----

For an existing folder, you could plot all the events and/or all the stations, ray path for event-station pairs and epicentral-distance/time for the waveforms.

The general syntax for plotting tools is: 

::

    $ obspyDMT --plot_option 'address'

that *--plot_option* could be *--plot_ev* for events, *--plot_sta* for stations, *--plot_se* for stations and events, *--plot_ray* for ray path between each event-station pairs and *--plot_epi* for epicentral-distance/time. 

All the examples showed in this section are based on the folder created by the following request:

::

    $ obspyDMT --min_mag '8.9' --min_date '2011-03-01' --identity 'TA.Z*.*.BHZ' --arc 'N'

**Example 1:** let's plot both stations and events available in the folder:

::

    $ obspyDMT --plot_se './obspyDMT-data'

.. image:: figures/plotse.png
   :scale: 75%
   :align: center

the default format is *png*, but assume that we want *pdf* for our figures, then:

::

    $ obspyDMT --plot_se './obspyDMT-data' --plot_format 'pdf'

**Example 2:** in this example, we want to plot the ray path for event-station pairs but save the result in *$HOME/Desktop*:

::

    $ obspyDMT --plot_ray './obspyDMT-data' --plot_format 'pdf' --plot_save '$HOME/Desktop'

.. image:: figures/plotray.png
   :scale: 75%
   :align: center

**Example 3:** obspyDMT supports GMT plots as well. For this reason, GMT5_ should be installed on your machine. In this example, we want to plot the ray path for event-station pairs (similat to *Example 2*) by using GMT5_:

::

    $ obspyDMT --plot_ray_gmt './obspyDMT-data'

.. image:: figures/plotraygmt.png
   :scale: 50%
   :align: center

----------
Seismicity
----------

Geographical and historical distribution of earthquake activities (seismicity) can be plotted using *--seismicity* option in obspyDMT. In this mode, the software finds the events according to the input parameters and generates an image in which the events are categorized based on depth and magnitude.

**Example:** the command line to create *Japan* seismicity map from all the events available in IRIS_ archive with magnitude more than 3.0 is as follow:

::

    $ obspyDMT --datapath 'Seismicity' --seismicity --event_catalog IRIS --min_mag 3.0 --min_date 2000-01-01 --max_date 2013-01-01 --event_rect 120.0/155.0/25.0/55.0

*--datapath* is the address where the event catalog will be created, *--seismicity* enables the seismicity mode and *--event_catalog* changes the searching catalog (*EMSC* by default) to *IRIS*, *--min_mag*, *--min_date*, *--max_date* and *--event_rect* are event search parameters.

.. image:: figures/seismicity.png
   :scale: 50%
   :align: center

----------------
Folder structure
----------------

obspyDMT organizes the retrieved and processed data in a homogeneous way. Basically, when you want to run the code, you could specify a top-level folder path in which all the data will be organized:

::

    $ obspyDMT --datapath '/path/to/my/desired/address'

obspyDMT will create the folder (*/path/to/my/desired/address*) then start to create folders and files during retrieving and processing as it is shown in the following figure: 

.. image:: figures/Folderstruct.png
   :scale: 80%
   :align: center

-----------------
Available options
-----------------

All the options currently available in obspyDMT could be seen by:

::

    $ obspyDMT --help

The options specified by *--option=OPTION* are type-1 (with value) and *--option* are type-2 (without value).
Please refer to `Option types`_ section for more info about type 1 and type 2

.. Here, you could also find some of the options available in obspyDMT with a short description.
.. Options marked by (*) or (**) are:
.. 
.. (*): *option-1* (with value)
.. 
.. (**): *option-2* (without value)
.. 
.. Please refer to `Option types`_ section for more info about type 1 and type 2
.. 
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | options               | description           |   | options               | description           |
.. +=======================+=======================+===+=======================+=======================+
.. | --help                | show all the available|   | --test                | test the program for  |
.. |                       | flags with a short    |   |                       | the desired number of |
.. |                       | description for each  |   |                       | requests, eg:         |
.. |                       | and exit (**)         |   |                       | *--test 10* will test |
.. |                       |                       |   |                       | the program for 10    |
.. |                       |                       |   |                       | requests.             |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --version             | show the obspyDMT     |   | --iris_update         | update the specified  |
.. |                       | version and exit (**) |   |                       | folder for IRIS,      |
.. |                       |                       |   |                       | syntax:               |
.. |                       |                       |   |                       | --iris_update         |
.. |                       |                       |   |                       | address_of_the        |
.. |                       |                       |   |                       | _target_folder.       |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --check               | check all the         |   | --arc_update          | update the specified  |
.. |                       | dependencies and      |   |                       | folder for ArcLink,   |
.. |                       | their installed       |   |                       | syntax:               |
.. |                       | versions on the       |   |                       | --arc_update          |
.. |                       | local machine         |   |                       | address_of_the        |
.. |                       | and exit (**)         |   |                       | _target_folder.       |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --type                | type of the input     |   | --update_all          | update the specified  |
.. |                       | (*command* or *file*) |   |                       | folder for both IRIS  |
.. |                       | to be read            |   |                       | and ArcLink,          |
.. |                       | by obspyDMT. Please   |   |                       | syntax: --update_all  |
.. |                       | note that for         |   |                       | address_of_the        |
.. |                       | *--type 'file'* an    |   |                       | _target_folder.       |
.. |                       | external file         |   |                       | [Default: *N*] (*)    |
.. |                       | (*INPUT.cfg*) should  |   |                       |                       |
.. |                       | exist in the same     |   |                       |                       |
.. |                       | directory as          |   |                       |                       |
.. |                       | obspyDMT.py           |   |                       |                       |
.. |                       | [Default: command] (*)|   |                       |                       |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --reset               | if the datapath is    |   | --iris_ic             | apply instrument      |
.. |                       | found deleting it     |   |                       | correction to the     |
.. |                       | before running        |   |                       | specified folder for  |
.. |                       | obspyDMT. (**)        |   |                       | the downloaded        |
.. |                       |                       |   |                       | waveforms from        |
.. |                       |                       |   |                       | IRIS, syntax:         |
.. |                       |                       |   |                       | --iris_ic address_of  |
.. |                       |                       |   |                       | _the_target_folder.   |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --datapath            | the path where        |   | --arc_ic              | apply instrument      |
.. |                       | obspyDMT will store   |   |                       | correction to the     |
.. |                       | the data [Default:    |   |                       | specified folder for  |
.. |                       | *./obspyDMT-data*] (*)|   |                       | the downloaded        |
.. |                       |                       |   |                       | waveforms from        |
.. |                       |                       |   |                       | ArcLink, syntax:      |
.. |                       |                       |   |                       | --arc_ic address_of   |
.. |                       |                       |   |                       | _the_target_folder.   |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --min_date            | start time, syntax:   |   | --iris_ic_auto        | apply instrument      |
.. |                       | Y-M-D-H-M-S (eg:      |   |                       | correction            |
.. |                       | *2010-01-01-00-00-00*)|   |                       | automatically after   |
.. |                       | or just Y-M-D         |   |                       | downloading the       |
.. |                       | [Default: 10 days ago]|   |                       | waveforms from IRIS.  |
.. |                       | (*)                   |   |                       | [Default: *Y*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --max_date            | end time, syntax:     |   | --arc_ic_auto         | apply instrument      |
.. |                       | Y-M-D-H-M-S (eg:      |   |                       | correction            |
.. |                       | *2011-01-01-00-00-00*)|   |                       | automatically after   |
.. |                       | or just Y-M-D         |   |                       | downloading the       |
.. |                       | [Default: 5 days ago] |   |                       | waveforms from        |
.. |                       | (*)                   |   |                       | ArcLink.              |
.. |                       |                       |   |                       | [Default: *Y*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --min_mag             | minimum magnitude.    |   | --ic_all              | apply instrument      |
.. |                       | [Default: 5.5]        |   |                       | correction to the     |
.. |                       | (*)                   |   |                       | specified folder      |
.. |                       |                       |   |                       | for all the waveforms |
.. |                       |                       |   |                       | (IRIS and ArcLink),   |
.. |                       |                       |   |                       | syntax: --ic_all      |
.. |                       |                       |   |                       | address_of_the        |
.. |                       |                       |   |                       | _target_folder.       |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --max_mag             | maximum magnitude.    |   | --ic_no               | do not apply          |
.. |                       | [Default: 9.9]        |   |                       | instrument correction |
.. |                       | (*)                   |   |                       | automatically.        |
.. |                       |                       |   |                       | This is equivalent    |
.. |                       |                       |   |                       | to: *--iris_ic_auto N |
.. |                       |                       |   |                       | --arc_ic_auto N* (**) |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --min_depth           | minimum depth.        |   | --pre_filt            | apply a bandpass      |
.. |                       | [Default: +10.0       |   |                       | filter to the data    |                                          
.. |                       | (above the surface!)] |   |                       | trace before          |               
.. |                       | (*)                   |   |                       | deconvolution         |
.. |                       |                       |   |                       | (*None* if you do not |
.. |                       |                       |   |                       | need pre_filter),     | 
.. |                       |                       |   |                       | syntax:               |
.. |                       |                       |   |                       | *(f1,f2,f3,f4)* which |
.. |                       |                       |   |                       | are the four corner   |
.. |                       |                       |   |                       | frequencies of a      |
.. |                       |                       |   |                       | cosine taper, one     |
.. |                       |                       |   |                       | between f2 and f3     |
.. |                       |                       |   |                       | and tapers to zero    |
.. |                       |                       |   |                       | for f1 < f < f2 and   |
.. |                       |                       |   |                       | f3 < f < f4.          |
.. |                       |                       |   |                       | [Default:             |
.. |                       |                       |   |                       | *(0.008, 0.012, 3.0,  |
.. |                       |                       |   |                       | 4.0)*] (*)            |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --max_depth           | maximum depth.        |   | --corr_unit           | correct the raw       |
.. |                       | [Default: -6000.0]    |   |                       | waveforms for DIS (m),| 
.. |                       | (*)                   |   |                       | VEL (m/s) or          |
.. |                       |                       |   |                       | ACC (m/s^2).          |
.. |                       |                       |   |                       | [Default: DIS] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --event_rect          | search for all the    |   | --zip_w               | compress the          |
.. |                       | events within the     |   |                       | raw-waveform files    |                                            
.. |                       | defined rectangle,    |   |                       | after applying        |                                         
.. |                       | GMT syntax:           |   |                       | instrument correction.|                                         
.. |                       | <lonmin>/<lonmax>/    |   |                       | (**)                  |                            
.. |                       | <latmin>/<latmax>     |   |                       |                       |                            
.. |                       | [Default:             |   |                       |                       |                    
.. |                       | -180.0/+180.0         |   |                       |                       |                       
.. |                       | /-90.0/+90.0] (*)     |   |                       |                       |   
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --max_result          | maximum number of     |   | --zip_r               | compress the response |
.. |                       | events to be          |   |                       | files after applying  |                                         
.. |                       | requested.            |   |                       | instrument correction.|                                        
.. |                       | [Default: 2500] (*)   |   |                       | (**)                  |   
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --get_events          | event-based request   |   | --iris_merge          | merge the IRIS        |
.. |                       | (please refer to      |   |                       | waveforms in the      |                                         
.. |                       | the tutorial).        |   |                       | specified folder,     |                                        
.. |                       | [Default: *Y*] (*)    |   |                       | syntax: --iris_merge  |                
.. |                       |                       |   |                       | address_of_the        |
.. |                       |                       |   |                       | _target_folder.       |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --continuous          | continuous request    |   | --arc_merge           | merge the ArcLink     |
.. |                       | (please refer to the  |   |                       | waveforms in the      |                                             
.. |                       | tutorial). (**)       |   |                       | specified folder,     |         
.. |                       |                       |   |                       | syntax: --arc_merge   |
.. |                       |                       |   |                       | address_of_the        |
.. |                       |                       |   |                       | _target_folder.       |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --interval            | time interval for     |   | --iris_merge_auto     | merge automatically   |
.. |                       | dividing the          |   |                       | after downloading     |                                      
.. |                       | continuous request.   |   |                       | the waveforms from    |                                             
.. |                       | [Default: 86400 sec   |   |                       | IRIS.                 |                                 
.. |                       | (1 day)] (*)          |   |                       | [Default: *Y*] (*)    |           
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --iris_bulk           | using the IRIS        |   | --arc_merge_auto      | merge automatically   |
.. |                       | bulkdataselect        |   |                       | after downloading     |                                        
.. |                       | Web service.          |   |                       | the waveforms         |                                  
.. |                       | Since this method     |   |                       | from ArcLink.         |                                       
.. |                       | returns multiple      |   |                       | [Default: *Y*] (*)    |                                        
.. |                       | channels of time      |   |                       |                       |                           
.. |                       | series data for       |   |                       |                       |                          
.. |                       | specified time ranges |   |                       |                       |                                
.. |                       | in one request,       |   |                       |                       |                          
.. |                       | it speeds up the      |   |                       |                       |                           
.. |                       | waveform retrieving   |   |                       |                       |                              
.. |                       | approximately by      |   |                       |                       |                           
.. |                       | a factor of two.      |   |                       |                       |                           
.. |                       | [RECOMMENDED] (**)    |   |                       |                       | 
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --waveform            | retrieve the waveform.|   | --merge_all           | merge all waveforms   |
.. |                       | [Default: *Y*] (*)    |   |                       | (IRIS and ArcLink) in |
.. |                       |                       |   |                       | the specified folder, |
.. |                       |                       |   |                       | syntax: --merge_all   |
.. |                       |                       |   |                       | address_of_the        |
.. |                       |                       |   |                       | _target_folder.       |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --response            | retrieve the response |   | --merge_no            | do not merge          |
.. |                       | file. [Default: *Y*]  |   |                       | automatically. This is| 
.. |                       | (*)                   |   |                       | equivalent to:        |
.. |                       |                       |   |                       | *--iris_merge_auto N  |
.. |                       |                       |   |                       | --arc_merge_auto N*   |
.. |                       |                       |   |                       | (**)                  |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --iris                | send request          |   | --merge_type          | merge *raw* or        |
.. |                       | (waveform/response)   |   |                       | *corrected* waveforms.|                                                  
.. |                       | to IRIS.              |   |                       | [Default: *raw*]      |                                  
.. |                       | [Default: *Y*] (*)    |   |                       | (*)                   | 
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --arc                 | send request          |   | --plot_iris           | plot waveforms        |
.. |                       | (waveform/response)   |   |                       | downloaded from IRIS. |                                                 
.. |                       | to ArcLink.           |   |                       | (*)                   |                      
.. |                       | [Default: *Y*] (*)    |   |                       |                       | 
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --SAC                 | SAC format for saving |   | --plot_arc            | plot waveforms        |
.. |                       | the waveforms. Station|   |                       | downloaded from       |                                              
.. |                       | location (stla and    |   |                       | ArcLink. (*)          |                                    
.. |                       | stlo), station        |   |                       |                       |                         
.. |                       | elevation (stel),     |   |                       |                       |                            
.. |                       | station depth (stdp), |   |                       |                       |                                
.. |                       | event location (evla  |   |                       |                       |                               
.. |                       | and evlo), event depth|   |                       |                       |                                 
.. |                       | (evdp) and event      |   |                       |                       |                           
.. |                       | magnitude (mag) will  |   |                       |                       |                               
.. |                       | be stored in the SAC  |   |                       |                       |                               
.. |                       | headers.              |   |                       |                       |                   
.. |                       | [Default: MSEED] (**) |   |                       |                       | 
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --time_iris           | generate a data-time  |   | --plot_all            | plot all waveforms    |
.. |                       | file for an IRIS      |   |                       | (IRIS and ArcLink).   |                                            
.. |                       | request. This file    |   |                       | [Default: *Y*] (*)    |                                          
.. |                       | shows the required    |   |                       |                       |                             
.. |                       | time for each request |   |                       |                       |                                
.. |                       | and the stored data   |   |                       |                       |                              
.. |                       | in the folder. (**)   |   |                       |                       |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --time_arc            | generate a data-time  |   | --plot_type           | plot *raw* or         |
.. |                       | file for an ArcLink   |   |                       | *corrected* waveforms.|                                                  
.. |                       | request. This file    |   |                       | [Default: *raw*] (*)  |                                                
.. |                       | shows the required    |   |                       |                       |                             
.. |                       | time for each request |   |                       |                       |                                
.. |                       | and the stored data   |   |                       |                       |                              
.. |                       | in the folder. (**)   |   |                       |                       |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --preset              | time parameter in     |   | --plot_ev             | plot all the events   |
.. |                       | seconds which         |   |                       | found in the specified|                                            
.. |                       | determines how close  |   |                       | folder, syntax:       |                                            
.. |                       | the time series data  |   |                       | --plot_ev address_of  |                                                 
.. |                       | (waveform) will be    |   |                       | _the_target_folder.   |                                             
.. |                       | cropped before the    |   |                       | [Default: *N*] (*)    |
.. |                       | origin time of the    |   |                       |                       |
.. |                       | event.                |   |                       |                       |
.. |                       | [Default: 0.0 seconds.|   |                       |                       |
.. |                       | ] (*)                 |   |                       |                       |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --offset              | time parameter in     |   | --plot_sta            | plot all the stations |
.. |                       | seconds which         |   |                       | found in the specified|                                            
.. |                       | determines how close  |   |                       | folder, syntax:       |                                            
.. |                       | the time series data  |   |                       | --plot_sta address_of |                                                  
.. |                       | (waveform) will be    |   |                       | _the_target_folder.   |                                             
.. |                       | cropped after the     |   |                       | [Default: *N*] (*)    |                                         
.. |                       | origin time of the    |   |                       |                       |                             
.. |                       | event.                |   |                       |                       |                 
.. |                       | [Default:             |   |                       |                       |                   
.. |                       | 1800.0 seconds.] (*)  |   |                       |                       |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --identity            | identity code         |   | --plot_se             | plot both all the     |
.. |                       | restriction, syntax:  |   |                       | stations and all the  |                                                 
.. |                       | net.sta.loc.cha       |   |                       | events found in the   |                                           
.. |                       | (eg: TA.*.*.BHZ to    |   |                       | specified folder,     |                                            
.. |                       | search for all BHZ    |   |                       | syntax: --plot_se     |                                            
.. |                       | channels in           |   |                       | address_of_the_target |                                         
.. |                       | TA network).          |   |                       | _folder.              |                                         
.. |                       | [Default: *.*.*.*] (*)|   |                       | [Default: *N*] (*)    | 
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --net                 | network code.         |   | --plot_ray            | plot the ray coverage |
.. |                       | [Default: '*'] (*)    |   |                       | for all the           |
.. |                       |                       |   |                       | station-event pairs   |
.. |                       |                       |   |                       | found in the specified| 
.. |                       |                       |   |                       | folder, syntax:       |
.. |                       |                       |   |                       | --plot_ray address    |
.. |                       |                       |   |                       | _of_the_target_folder.|
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --sta                 | station code.         |   | --plot_epi            | plot *epicentral      |
.. |                       | [Default: '*'] (*)    |   |                       | distance-time* for all| 
.. |                       |                       |   |                       | the waveforms found in| 
.. |                       |                       |   |                       | the specified folder, |
.. |                       |                       |   |                       | syntax: --plot_epi    |
.. |                       |                       |   |                       | address_of_the_target |
.. |                       |                       |   |                       | _folder.              |
.. |                       |                       |   |                       | [Default: *N*] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --loc                 | location code.        |   | --min_epi             | plot *epicentral      |
.. |                       | [Default: '*'] (*)    |   |                       | distance-time*        |
.. |                       |                       |   |                       | (refer to             |
.. |                       |                       |   |                       | *--plot_epi*) for all |
.. |                       |                       |   |                       | the waveforms with    |
.. |                       |                       |   |                       | epicentral-distance >=| 
.. |                       |                       |   |                       | min_epi.              |
.. |                       |                       |   |                       | [Default: 0.0] (*)    |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --cha                 | channel code.         |   | --max_epi             | plot *epicentral      |
.. |                       | [Default: '*'] (*)    |   |                       | distance-time*        |
.. |                       |                       |   |                       | (refer to             |
.. |                       |                       |   |                       | *--plot_epi*) for all |
.. |                       |                       |   |                       | the waveforms with    |
.. |                       |                       |   |                       | epicentral-distance <=| 
.. |                       |                       |   |                       | max_epi.              |
.. |                       |                       |   |                       | [Default: 180.0] (*)  |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --station_rect        | search for all the    |   | --plot_save           | the path where        |
.. |                       | stations within the   |   |                       | obspyDMT will store   |                                               
.. |                       | defined rectangle,    |   |                       | the plots             |                                    
.. |                       | GMT syntax:           |   |                       | [Default: '.'         |                                 
.. |                       | <lonmin>/<lonmax>/    |   |                       | (the same directory   |                                             
.. |                       | <latmin>/<latmax>.    |   |                       | as obspyDMT.py)] (*)  |                                                
.. |                       | May not be used       |   |                       |                       |                          
.. |                       | together with circular|   |                       |                       |                                 
.. |                       | bounding box station  |   |                       |                       |                               
.. |                       | restrictions          |   |                       |                       |                       
.. |                       | (station_circle)      |   |                       |                       |                           
.. |                       | [Default:             |   |                       |                       |                    
.. |                       | -180.0/+180.0/        |   |                       |                       |                        
.. |                       | -90.0/+90.0] (*)      |   |                       |                       |
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --station_circle      | search for all the    |   | --plot_format         | format of the plots   |
.. |                       | stations within the   |   |                       | saved on the local    |                                              
.. |                       | defined circle,       |   |                       | machine               |                               
.. |                       | syntax:               |   |                       | [Default: *png*] (*)  |                                     
.. |                       | <lon>/<lat>/          |   |                       |                       |                       
.. |                       | <rmin>/<rmax>.        |   |                       |                       |                          
.. |                       | May not be used       |   |                       |                       |                           
.. |                       | together with         |   |                       |                       |                         
.. |                       | rectangular bounding  |   |                       |                       |                                
.. |                       | box station           |   |                       |                       |                       
.. |                       | restrictions          |   |                       |                       |                        
.. |                       | (station_rect). (*)   |   |                       |                       |    
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+
.. | --email               | send an email to the  |   |                       |                       |          
.. |                       | specified             |   |                       |                       |          
.. |                       | email-address after   |   |                       |                       |          
.. |                       | completing the job,   |   |                       |                       |          
.. |                       | syntax:               |   |                       |                       |          
.. |                       | --email email_address.|   |                       |                       |          
.. |                       | [Default: *N*] (*)    |   |                       |                       |          
.. +-----------------------+-----------------------+---+-----------------------+-----------------------+

.. obspyDMT: http://obspy.org/browser/obspy/trunk/apps/obspyDMT/obspyDMT.py
.. _obspyDMT: https://github.com/kasra-hosseini/obspyDMT
.. _ObsPy: https://github.com/obspy/obspy/wiki
.. _IRIS: http://www.iris.edu/ws/
.. _bulkdataselect: http://www.iris.edu/ws/bulkdataselect/
.. _ORFEUS: http://www.orfeus-eu.org/
.. _EMSC: http://www.emsc-csem.org/
.. _ArcLink: http://www.webdc.eu/arclink/
.. _http://pypi.python.org/pypi/obspyDMT: http://pypi.python.org/pypi/obspyDMT
.. _PyPI: http://pypi.python.org/pypi/obspyDMT
.. _GitHub: https://github.com/kasra-hosseini/obspyDMT
.. _pprocess: https://pypi.python.org/pypi/pprocess
.. _GMT5: http://gmt.soest.hawaii.edu/
