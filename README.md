# Errata-based correction of marine geophysical track line data

Authors:
	Michael Hamilton (Chandler) and Paul Wessel
	School of Ocean and Earth Science and Technology
	University of Hawaii

Date: June 2012

Note: This document explains both the creation and usage of E77 corrections.  Users who simply want to apply published E77 corrections to NGDC data can skip sections C and D.

A)  E77 errata table versions must coincide with their respective MGD77 versions. As of June 29, 2007, NGDC replaced the source institution supplied  "MGD77 CREATE DATE" header field with NGDC's "Assimilation Date", updated whenever revised trackline data sets are submitted. This corrected a previous tendency for source institutions to resubmit revised datasets without changing the MGD77 CREATE DATE. Therefore, these E77 tables will only be compatible with MGD77 tracks downloaded after June 29, 2007.

B)  The E77 errata system is one facet of our along-track analysis approach for improving marine geophysical trackline data quality [1,2,3]. The along-track tools are distributed as a supplement to the Generic Mapping Tools (GMT). GMT installation instructions can be found at http://gmt.soest.hawaii.edu. Once installed, the mgd77 tools are easily configured. An MGD77_paths.txt file containing complete paths to NGDC trackline data directories must be present in the directory referred to by the MGD77_HOME environment variable, which also needs to be set by the user. The MGD77_paths.txt file should be arranged in order of ascending priority (e.g., paths to updated or newer files should precede older or outdated download directories). See sample below:

    % cat $MGD77_HOME/MGD77_paths.txt
    .
    /data/NGDC/mgd77_2011_01_13_FTP
    /data/NGDC/mgd77_2009_03_10_FTP
    /data/NGDC/2007-07-10_DVD
    /data/NGDC/MGD77+
    
    Once properly configured, trackline data may be easily accessed:
    e.g.,
    
    To view a cruise's MGD77 header metadata:
    
    gmt mgd77info 08010001 -Mr
    
    To extract columns of data:
    
    gmt mgd77list 08010001 -Flon,lat,depth

    See man pages for complete options and syntax.
    
    To set up the errata system, an E77 directory needs to be created in the $MGD77_HOME directory. The mgd77 tools will look in this directory automatically when attempting to apply E77 corrections.

C)  Creating errata tables

    The mgd77sniffer tool performs an extensive array of data quality checks such as bad navigation checking, geophysical measurements out of valid range, excessive data gradients, comparisons to global gravity and predicted bathymetry grids, comparisons of reported and recomputed potential field anomalies, among others. 
    
    E.g., In order to create a new errata E77 file using all possible comparisons, try:
    
    gmt mgd77sniffer <ngdc_id> -Rwest/east/south/north -De -Gdepth,topo1.grd \
    -gfaa,grav.18.1.img,0.1,1,80.738008628 -Gnav,dist_to_coast_1m_grd.i4
   
    Global gravity and topography grids were obtained from http://topex.ucsd.edu. The distance to coast grid may be obtained from ftp://ftp.soest.hawaii.edu/pwessel/gshhs/dist_to_GSHHS_v1.3_1m.nc
 
D)  Errata table review

    Results of these quality checks are output in the E77 errata format. E77 tables consist of three parts: (a) Cruise examination information (file version, reviewer comments, file generation command, etc), (b) the errata header which reports any detected systematic errors pertaining to entire surveys, and (c) errata pertaining to data records. '?' flags in the errata header section (i.e., after the "# Errata: Header" row but before the "# Errata: Data" row) need to be toggled to either 'Y' or 'N' by the errata table reviewer. Errata data flags report all errors encountered in individual data records. Errata data flags are not required to be toggled from '?' to 'Y' or 'N' and should only be changed when a reviewer has reviewed such data in detail. After carefully reviewing the errata table and determining which flags to apply, the reviewer then toggles the 'Errata table verification status' from 'N' to 'Y'. The errata table will not be applied in later processing if the verification status is not changed to 'Y'. Processing comments and the processor's name are also important for collaborative data review.
    
    Move reviewed E77 errata tables to the $MGD77_HOME E77 directory.

E)  Applying corrections

    1. Convert the raw trackline file (m: MGD77T, a: MGD77) to MGD77+ format (see [3] for format details):
        
    gmt mgd77convert <ngdc-id> -Fm -Tc

    2. Make sure that the path to the MGD77+ directory is specified in the MGD77_paths.txt file
    
    3. Apply E77 corrections

    gmt mgd77manage <ngdc-id> -Ae 
    
    4. Confirm that E77 corrections are applied
    
    gmt mgd77info <ngdc-id> -Me -Iam
    
F) Extracting corrected data
    
    gmt mgd77list <ngdc-id> -Flon,lat,depth -Iam

    See man pages for complete options and syntax.

References:

[3] Chandler, M.T. and P. Wessel (2012): Errata-based Correction of Marine Geophysical Trackline Data, Geochem Geophy Geosy., 13(Q10004), doi:10.1029/2012GC004294.

[2] Chandler, M.T., and P. Wessel (2008), Improving the quality of marine geophysical track line data: Along-track analysis, J. Geophys. Res., 113, B02102, doi:10.1029/2007JB005051.

[1] Wessel, P. and M.T. Chandler (2007), The mgd77 supplement to the Generic Mapping Tools, Comp. & Geosci., 33, 62-75.