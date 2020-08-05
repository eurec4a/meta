## EUREC4A
This document suggests some standards for the treatment of EUREC4A metadata.  The goal is to evolve a metadata standard that will enable data analysis and perhaps better structure the use and provision of data more generally. The primary source of information for data files are the data files themselves, which implies that they contain their own meta-data, which may be in the form of links to other files or tables.  However, many people like to use filenames as a compressed form of meta data, and for this case we suggest some file naming conventions.

### Identifiers
The data repository, will be built around a controlled vocabulary consisting of a hierarchy of unique identifiers.  We also encourage the use of these identifiers in file meta-data and for building file names.  The suggested identifiers are as follows, not every identifier will apply to every instance of the data, in fact almost every dataset will miss one or more identifies:

  * **campaign:**  EUREC4A
  * **project:**  An optional identifier to allow groups to give additional specificity to a set of measurements collected with different platforms, for instance ATOMIC.
  * **platform:**  An identifier drawn from a controlled vocabulary specifying a platform from which the data was collected.  See [EUREC4A.yaml](EUREC4A.yaml) which maps `platform` to platform metadata.
  * **instrument:**  Specifies an instrument from which a measurement has been made.  Ideally similar instruments from different platforms will adopt similar identifiers. Level 2 data will often have a `platform` and `instrument`, but in some cases an instrument and platform may be synoymous.  
  * **product:**  An optional identifier to be used with the `instrument` to allow groups the flexibility to group measurements in different ways, i.e., SAFIRE_Aerosol might designate a bundle of aerosol instruments run by SAFIRE.
  * **variable:** A specific quantity. Usually this will be the output from a sensor, but it could also be a derived or composite variable.  Examples might be a voltage on a power supply, a temperature reading, a radar reflectivity, the latitude and longitude of a track, or maybe the divergence from a sounding circle. It should follow a standard vocabulary whenever possible, i.e., NetCDF Climate and Forecast (CF) Metadata Conventions.
  * **time:** For some data it may be useful to indicate the time of the measurement or the time-range of the data included in the file. This will adopt the form yyyymmddTHHMMSS-yyyymmddTHHMMSS, or e.g., yyyymmdd for a single instance. `time` should be specified
  specified in UTC at the minimum level of complexity required to distinguish one file from another, i.e. files covering a single day might be include yyyymmdd, while those covering an hour might be designated yyyymmddTHH, etc. 
 * **version:** The data versioning, in the form vN.M with N and M integers. Using version 0 for preliminary data is encouraged.

This concept admits virtual platforms, or projects, as there may be ambiguity between the two.  For instance JOANNE is a virtual platform for dropsonde data, and combines dropsonde data from different ships.

The EUREC4A database will use the `platform` as the first level of data granularity. Depending on the platform, additional levels of granularity (sub-directories) may be included and labeled by some (possibly empty) subset of `instrument`, `product` or `variable`.  It will not organize data by `project`, nor by `time`.

### File naming conventions

Filenames should be composed of a hierarchical application of the applicable subset of the controlled vocabularly, with ids separated by underscores.  Hyphens can be used for ids composed of compound words.  Hence the structure would be:

`<campaign>_<project>_<platform>_<instrument>_<variable>_<time>_<version>.nc`

Examples could be
  - EUREC4A_Meteor_Track_v0.2.nc
  - EUREC4A_ATR_Safire_Radiation_20200202_v2.2.nc  
  - EUREC4A_ATOMIC_SWIFT-12_T05_20200120-20200212_v1.0.nc
  - EUREC4A_JOANNE_Dropsonde-RD41_Level_3_v0.5.3

We envision every file beginning with the `campaign` and ends with the `version` and includes at least one further id to add content specificity. By this convention the version_id is everything between the last underscore and the last period.  Underscores are important as they delineate 'words'.

### File metadata

File metadata should include all the identifiers, i.e., `platform`: "ATR", `campaign`: "EUREC4A" and additionally contain contact information of the person or organization responsible for the file (its creator), and instructions as to how use of the data should be acknowledged. Often the contact will be the PI for the platform (or instrument) or the creator for the derived product.  We are working on a broader meta data concept for EUREC4A, but want to try it out on a subset of the data before busying people with adhering to it.  For now we just ask people to subscribe to the naming convention of the `platform`s which you can read as plain text here: [EUREC4A.yaml](EUREC4A.yaml).

### Coordinates and variables

We suggest a controlled vocabulary for position information, i.e., time (seconds since 2020-01-01, lat (degree_north), lon (degree_east), alt (m).  Note the short three letter names for position variables, i.e., lat, not latitude. Again coordination among instrument scientists so that similar instruments across different platforms have the same name would be desirable.  If a file is associated with a platform then the position information will be carried by that platform's track file and need not be provided separately for that instrument.

#### Time

Leap seconds can cause ambiguities in the definition of the time variable. While the error may seem to be small, for measurements on fast platforms as present during EUREC4A, a different handling of leap seconds between dataset creator and dataset user can for example cause position errors of several kilometers. At the time of writing, the number of leap seconds since the common unix time epoch (1970-01-01) and now is 27. There has been no leap second since 2017-01-01 and currently there is no schedule for an upcoming leap second and 2020 will be a year without leap seconds.

In order to prevent errors from leap second ambiguities, time variables should be given with no leap seconds between the reference date and variable date. For EUREC4A, a good default will be **seconds since 2020-01-01**. If a different resolution is required, other time intervals such as milliseconds, microseconds or nanoseconds are possible as well.

### Flagging

For flagging data we recommend the use of the 2013 UNESCO Intergovernmental Oceanographic Commission Conventions: Recommendation for a Quality Flag Scheme for the Exchange of Oceanographic and Marine Meteorological Data. (IOC Manuals and Guides, 54, Vol. 3.) 12 pp. (English.)(IOC/2013/MG/54-3)

Primary-level flag short name   Definition
```
  1 Good  Passed documented required QC tests
  2 Not evaluated, not available or unknown Used for data when no QC test performed or the information on quality is not available
  3 Questionable/suspect Failed non‐critical documented metric or subjective test(s)
  4 Bad  Failed critical documented QC test(s) or as assigned by the data provider
  9 Missing data Used as place holder when data are missing
```

### Example from sounding data ###

Here is an example of the meta data from the level 2 sounding files created by Hauke Schulz.  Note also the file naming conventions.

```
netcdf EUREC4A_RonBrown_Vaisala-RS_L2_v2.2.0.nc {
dimensions:
	sounding = UNLIMITED ; // (329 currently)
	alt = 3100 ;
	nv = 2 ;
	str_dim = 1000 ;
variables:
	short alt(alt) ;
		alt:long_name = "geopotential height" ;
		alt:standard_name = "geopotential_height" ;
		alt:units = "m" ;
		alt:axis = "Z" ;
		alt:positive = "up" ;
		alt:bounds = "alt_bnds" ;
...
netcdf EUREC4A_RonBrown_soundings {
dimensions:
	sounding = UNLIMITED ; // (329 currently)
	alt = 3100 ;
	nv = 2 ;
	str_dim = 1000 ;
variables:
	short alt(alt) ;
		alt:long_name = "geopotential height" ;
		alt:standard_name = "geopotential_height" ;
		alt:units = "m" ;
		alt:axis = "Z" ;
		alt:positive = "up" ;
		alt:bounds = "alt_bnds" ;
...
/ global attributes:
		:title = "EUREC4A level 2 sounding data" ;
		:platform = "RonBrown" ;
		:instrument = "Radiosonde RS41-SGP by Vaisala" ;
		:doi = "10.25326/62" ;
		:created_with = "batch_interpolate_soundings.py with its last modifications on Wed Jun 24 00:42:03 2020" ;
		:created_on = "Wed Jun 24 19:46:17 2020" ;
		:featureType = "trajectory" ;
		:Conventions = "CF-1.7" ;
		:campaign = "EUREC4A" ;
		:references = "Stephan et al. (2020): Ship- and island-based atmospheric soundings from the 2020 EUREC4A field campaign, ESSD" ;
		:acknowledgement = "The MPI-M is listed as the institute of first contact. Investigators, Institutions and Agencies who contributed to this dataset are numerous and are acknowledged in the reference publication" ;
		:instrument = "Vaisala-RS" ;
		:version = "v2.2.0" ;
}```
