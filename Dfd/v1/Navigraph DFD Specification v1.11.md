
|Company:|Navigraph|
|---|---|
|Author:|Richard Stefan|
|Version:|1.11|
|Date:|February 19, 2020|


**Table of Content**

[[TOC]]

**Revisions**


|Version|Date|Changes|
|---|---|---|
|0.1|10/09/2016|basic dataset – first draft|
|0.2|15/09/2016|dataset restructuring|
|0.3|16/09/2016|SQL tables/indices added, new records Crusing, FIR/UIR, Restrictive Airspace, Controlled Aispace, Grid Mora, Airport MSA, Enroute Airways Restriction, Enroute Communication Records added|
|0.4|20/10/2017|table names standardized, database columns review/corrections|
|1.0|21/10/2017|New records Gates, GLS added – first version|
|1.01|23/10/2017|Added "boundary via" in Controlled Airspace table, sort information added in airspace-tables|
|1.02|15/11/2017|Inbound/Outbound Course and Distance values added to Enroute Airway Records|
|1.03|02/01/2018|Marker Identifier added to Marker Records, FIR/UIR Address added to FIR/UIR Records|
|1.04|07/02/2018|ICAO 3-letter codes (continental for USA/CAN) column added in the Airport Records|
|1.05|06/01/2018|Added "parsed at" in header table|
|1.06|24/06/2018|Multiple Code field added in MSA and Restricted Airspace table|
|1.07|28/06/2018|Time Code field added in the Restricted Airspace table|
|1.08|10/07/2018|Localizer Width added in the Localizer/Glideslope table|
|1.09|27/07/2018|Distance/Time flag added in the Procedure tables|
|1.10|16/08/2019|IATA/ATA Designator added in the Airport table|
|1.11|19/02/2020|Format and link error in the document fixed|


1. **Introduction**

This document will define the complete dataset specifications of the Digital Flight Data (DFD) format. It is heavily inspired by the ARINC 424 specifications, though tailored to Flight Simulator add-ons use. Whenever possible, we use the same naming conventions as the ARINC specification, to avoid confusion. Whenever possible, we will also clearly indicate from which sections and record columns the data has to be extracted from.

We used the standard ARINC424-18 specification, but it should forward compatible with 424-19/20. The DFD dataset is provided in a SQLite file. We can also prepare a customized DFD formatted dataset. The DFD can be parsed into any file format, including plain ASCII text files, and can include any record in this documentation, and also additional data. 

The **DFD** contains the primary records of the following record types (in parenthesis, the corresponding ARINC 424 section and subsection codes):

* [Airports (PA)](#heading=h.3rdcrjn)

* [Enroute Airways (ER)](#heading=h.17dp8vu)

* [Enroute NDB Navaids (DB)](#heading=h.tyjcwt)

* [Enroute Waypoints (EA)](#heading=h.1t3h5sf)

* [Holdings (EP)](#heading=h.2s8eyo1)

* [IAP - Instrument Arrival Procedures (PF)](#heading=h.1ksv4uv)

* [Localizer Marker (PM)](#heading=h.35nkun2)

* [Localizer/Glideslopes (PI)](#heading=h.lnxbz9)

* [Runways (PG)](#heading=h.26in1rg)

* [SID – Standard Instrument Departure (PD)](#heading=h.1ksv4uv)

* [STAR – Standard Terminal Arrival Route (PE)](#heading=h.1ksv4uv)

* [Terminal NDB Navaids (PN)](#heading=h.3dy6vkm)

* [Terminal Waypoints (PC)](#heading=h.4d34og8)

* [VHF Navaids (D)](#heading=h.3znysh7)

* [Airport Communication (PV)](#heading=h.44sinio)

* [Airport MSA (PS)](#heading=h.3j2qqm3)

* [Controlled Airspace (UC)](#heading=h.4i7ojhp)

* [Cruising Tables (TC)](#heading=h.2xcytpi)

* [Enroute Airway Restriction (EU)](#heading=h.1y810tw)

* [Enroute Communication (EV)](#heading=h.2jxsxqh)

* [FIR/UIR (UF)](#heading=h.1ci93xb)

* [Gate (PB)](#heading=h.2bn6wsx)

* [GLS (PT)](#heading=h.qsh70q)

* [Grid Mora (AS)](#heading=h.z337ya)

* [Restrictive Airspace (UR)](#heading=h.3whwml4)

Filename: dfd_xxxx.3sdb

(xxxx is the cycle-number)

2. **Specification**

When provided in SQLite binary file format, the name of the tables has following syntax:

tbl_<tablename> All columns in the records are lowercase. The columns contain no spaces, the "_" (underline) character is used to separate words, if necessary.

When provided in ASCII text file format all fields in the records are separated with a | character (vertical bar or ASCII 124). All blanks will be trimmed at the end of each field (excluding special marked fields – see the footnotes)

    1. **Header line**

SQL Format:

CREATE TABLE [tbl_header] (

[version] TEXT(5) NOT NULL,

[arincversion] TEXT(6) NOT NULL,

[revision] TEXT(2) NOT NULL,

[record_set] TEXT(8) NOT NULL,

[current_airac] TEXT(4) NOT NULL,

[effective_fromto] TEXT(10) NOT NULL,

[previous_airac] TEXT(4) NOT NULL,

[previous_fromto] TEXT(10) NOT NULL,

[parsed_at] TEXT(22) NOT NULL

)

ASCII Format:

version|arincversion|record-set|current AIRAC cycle|Revision|effective from-to|previous AIRAC cycle|effective from-to|parsed at


|Field|Format|max. length|ARINC Ref|
|---|---|---|---|
|version|alphanumeric|5||
|arincversion|alphanumeric|6||
|record-set|alphanumeric|8||
|current AIRAC|alphanumeric|4||
|Revision|alphanumeric|2||
|effective from-to|alphanumeric|10||
|previous AIRAC|alphanumeric|4||
|effective from-to|alphanumeric|10||
|parsed at|alphanumeric|22||


Example:

* 0.3|424-18|extended|1610|2|1509121016|1609|1808140916|01/01/01 – 10:10:10UTC

Description:

* version : the current version of the specifications (0.3)

* arincversion : the current used ARINC version (424-18)

* record-set : indicates the data content - extended

* current AIRAC : the current AIRAC cycle (1610)

* revision : revision of the current AIRAC cycle

* effective from-to : the date, when the current AIRAC cycle starts/ends (Format DDMMDDMMYY– DD is the day, MM is the month, YY is the year) (1509121016)

* previous AIRAC : the current AIRAC cycle (1609)

* effective from-to : is the date, when the current AIRAC cycle starts/ends (Format DDMMDDMMYY– DD is the day, MM is the month, YY is the year) (1808140916)

* parsed at : parsing/creation date of the current AIRAC cycle (Format DD/MM/YY – HH:MM:SS in UTC)

    2. **VHF Navaids**

SQL Format:

CREATE TABLE [tbl_vhfnavaids] (

[area_code] TEXT(3),

[airport_identifier] TEXT(4),

[icao_code] TEXT(2) NOT NULL,

[vor_identifier] TEXT(4) NOT NULL,

[vor_name] TEXT(30),

[vor_frequency] REAL(5),

[navaid_class] TEXT(5,

[vor_latitude] REAL(9),

[vor_longitude] REAL(10),

[dme_ident] TEXT(4),

[dme_latitude] REAL(9),

[dme_longitude] REAL(10),

[dme_elevation] INT(5),

[ilsdme_bias] REAL(3),

[range] INT(3),

[station_declination] REAL(5)

)

CREATE UNIQUE INDEX [pk_key]

	ON [tbl_vhfnavaids]

([icao_code],[vor_identifier])

ASCII Format:

Area Code|Airport ICAO Identifier|ICAO Code|VOR Identifier|VOR Name|VOR Frequency|NAVAID class|VOR Latitude|VOR Longitude|DME Ident|DME Latitude|DME Longitude|DME elevation|ILS/DME bias|Range|Station Declination


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|Airport Identifier|alphanumeric|4|5.6|
|ICAO Code|alphanumeric|2|5.14|
|VOR Identifier|alphanumeric|4|5.33|
|VOR Name|alphanumeric|30|5.71|
|VOR Frequency|numeric|5|5.34|
|NAVAID class|alphanumeric|5|5.35|
|VOR Latitude|numeric|9|5.36|
|VOR Longitude|numeric|10|5.37|
|DME Ident|alphanumeric|4|5.38|
|DME Latitude|numeric|9|5.36|
|DME Longitude|numeric|10|5.37|
|DME Elevation|alphanumeric|5|5.40|
|ILS/DME bias|numeric|2|5.90|
|Range|alphanumeric|3|5.149|
|Station Declination|alphanumeric|5|5.66|


Example:

* EUR|LOWW|LO|WGM|WAGRAM|112.20|VDHW |48.32385556|16.49095278||48.32301667|16.49080833|574||130|4.1

* EUR|LOWW|LO|OEX|SCHWECHAT|109.55| IT N|||OEX|48.10885833|16.57153889|624||25|4.1

Description:

* Area Code : geographical area of the navaid

* Airport Identifier : four character ICAO location identifier

* ICAO Code : location indicator of the navaid

* VOR Identifier : navaid identifier

* VOR Name : navaid name

* VOR Frequency : navaid frequency in kHz

* NAVAID class : navaid type, range/power, additional information & collocation (see appendix 3.1)

* VOR Latitude : navaid latitude in degrees decimal floating point (N positive, S negative)

* VOR Longitude : navaid longitude in degrees decimal floating point (E positive, W negative)

* DME Ident : identification of a DME facility, a TACAN facility or the DME (or TACAN) component of a VORDME or VORTAC facility

* DME Latitude : DME latitude in degrees decimal floating point (N positive, S negative)

* DME Longitude : DME longitude in degrees decimal floating point (E positive, W negative)

* DME Elevation : DME elevation in feet AMSL

* ILS/DME bias : specify the DME offset

* Range : navaid usable range in nautical miles

* Station Declination : angular difference between true north and the zero degree radial of the navaid in degrees

    3. **Enroute NDB Navaids**

SQL Format:

CREATE TABLE [tbl_enroute_ndbnavaids] (

[area_code] TEXT(3),

[icao_code] TEXT(2) NOT NULL,

[ndb_identifier] TEXT(4) NOT NULL,

[ndb_name] TEXT(30),

[ndb_frequency] REAL(5),

[navaid_class] TEXT(5),

[ndb_latitude] REAL(9,

[ndb_longitude] REAL(10)

)

CREATE UNIQUE INDEX [pk_db]

	ON [tbl_ndbnavaids]

([icao_code],[ndb_identifier])

ASCII Format:

Area Code|ICAO Code|NDB Identifier|NDB Name|NDB Frequency|NDB class|NDB Latitude|NDB Longitude


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|NDB Identifier|alphanumeric|4|5.33|
|NDB Name|alphanumeric|30|5.71|
|NDB Frequency|numeric|5|5.34|
|NDB class|alphanumeric|5|5.35|
|NDB Latitude|numeric|9|5.36|
|NDB Longitude|numeric|10|5.37|


Example:

* EUR|LO|STE|STEINHOF|293.00|H MW |48.21061667|16.24713611

 

Description:

* Area Code : geographical area of the NDB

* ICAO Code : location indicator of the NDB

* NDB Identifier : NDB identifier

* NDB Name : NDB name

* NDB Frequency : NDB frequency in Mhz

* NDB class : NDB type, range/power, additional information & collocation (see appendix 3.1)

* NDB Latitude : NDB latitude in degrees decimal floating point (N positive, S negative)

* NDB Longitude : NDB longitude in degrees decimal floating point (E positive, W negative)

    4. **Terminal NDB Navaids**

SQL Format:

CREATE TABLE [tbl_terminal_ndbnavaids] (

[area_code] TEXT(3),

[airport_identifier] TEXT(4) NOT NULL,

[icao_code] TEXT(2) NOT NULL,

[ndb_identifier] TEXT(4) NOT NULL,

[ndb_name] TEXT(30),

[ndb_frequency] REAL(5),

[navaid_class] TEXT(5),

[ndb_latitude] REAL(9),

[ndb_longitude] REAL(10)

)

CREATE UNIQUE INDEX [pk_pn]

	ON [tbl_ndbnavaids]

([airport_identifier],[icao_code],[ndb_identifier])

ASCII Format:

Area Code|Airport Identifier|ICAO Code|NDB Identifier|NDB Name|NDB Frequency|NDB class|NDB Latitude|NDB Longitude


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|Airport Identifier|alphanumeric|4|5.6|
|ICAO Code|alphanumeric|2|5.14|
|NDB Identifier|alphanumeric|4|5.33|
|NDB Name|alphanumeric|30|5.71|
|NDB Frequency|numeric|5|5.34|
|NDB class|alphanumeric|5|5.35|
|NDB Latitude|numeric|9|5.36|
|NDB Longitude|numeric|10|5.37|


Example:

* EUR|ED|MNW|MUNICH|338.00|H MW |48.37426667|16.24713611

 

Description:

* Area Code : geographical area of the NDB

* Airport Identifier : four character ICAO location identifier

* ICAO Code : location indicator of the NDB

* NDB Identifier : NDB identifier

* NDB Name : NDB name

* NDB Frequency : NDB frequency in Mhz

* NDB class : NDB type, range/power, additional information & collocation (see appendix 3.1)

* NDB Latitude : NDB latitude in degrees decimal floating point (N positive, S negative)

* NDB Longitude : NDB longitude in degrees decimal floating point (E positive, W negative)

    5. **Enroute Waypoints**

SQL Format:

CREATE TABLE [tbl_enroute_waypoints] (

	[area_code] TEXT(3, 

	[icao_code] TEXT(2) NOT NULL, 

	[waypoint_identifier] TEXT(5) NOT NULL, 

	[waypoint_name] TEXT(25), 

	[waypoint_type] TEXT(3), 

	[waypoint_usage] TEXT(2), 

	[waypoint_latitude] REAL(9, 

	[waypoint_longitude] REAL(10)

)

CREATE UNIQUE INDEX [pk_ea]

	ON [tbl_enroute_waypoints]

([icao_code],

[waypoint_identifier])

ASCII Format:

Area Code|ICAO Code|Waypoint Identifier|Waypoint Name|Waypoint Type|Waypoint Usage|Waypoint Latitude|Waypoint Longitude


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Waypoint Identifier|alphanumeric|5|5.13|
|Waypoint Name|alphanumeric|25|5.43|
|Waypoint Typei|alphanumeric|3|5.42|
|Waypoint Usagei|alphanumeric|2|5.82|
|Waypoint Latitude|numeric|9|5.36|
|Waypoint Longitude|numeric|10|5.37|


Example:

* EUR|LO|ABETI|ABETI|WU|RB|47.67771389|17.01284167

 

Description:

* Area Code : geographical area of the waypoint

* ICAO Code : location indicator of the waypoint

* Waypoint Identifier : waypoint identifier

* Waypoint Name : waypoint name

* Waypoint Type : waypoint type (see appendix 3.3)

* Waypoint Usage : waypoint usage (see appendix 3.7)

* Waypoint Latitude : waypoint latitude in degrees decimal floating point (N positive, S negative)

* Waypoint Longitude : waypoint longitude in degrees decimal floating point (E positive, W negative)

    6. **Terminal Waypoints**

SQL Format:

CREATE TABLE [tbl_terminal_waypoints] (

	[area_code] TEXT(3), 

	[region_code] TEXT(4) NOT NULL, 

	[icao_code] TEXT(2) NOT NULL, 

	[waypoint_identifier] TEXT(5) NOT NULL, 

	[waypoint_name] TEXT(25), 

	[waypoint_type] TEXT(3), 

	[waypoint_latitude] REAL(9), 

	[waypoint_longitude] REAL(10)

)

CREATE UNIQUE INDEX [pk_pc]

	ON [tbl_terminal_waypoints]

([area_code],[region_code],[icao_code],[waypoint_identifier])

ASCII Format:

Area Code|Region Code|ICAO Code|Waypoint Identifier|Waypoint Name|Waypoint Type|Waypoint Latitude|Waypoint Longitude


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|Region Code|alphanumeric|4|5.41/2|
|ICAO Code|alphanumeric|2|5.14|
|Waypoint Identifier|alphanumeric|5|5.13|
|Waypoint Name|alphanumeric|25|5.43|
|Waypoint Typei|alphanumeric|3|5.42|
|Waypoint Latitude|numeric|9|5.36|
|Waypoint Longitude|numeric|10|5.37|


Example:

* EUR|LOWW|LO|FI29|OEX112009|IAF|48.05601389|16.73783056

 

Description:

* Area Code : geographical area of the waypoint

* Region Code : airport identification code for the terminal waypoint

* ICAO Code : location indicator of the waypoint

* Waypoint Identifier : waypoint identifier

* Waypoint Name : waypoint name

* Waypoint Type : waypoint type (see appendix 3.4)

* Waypoint Latitude : waypoint latitude in degrees decimal floating point (N positive, S negative)

* Waypoint Longitude : waypoint longitude in degrees decimal floating point (E positive, W negative)

    7. **Holdings**

SQL Format:

CREATE TABLE [tbl_holdings] (

	[area_code] TEXT(3), 

	[region_code] TEXT(4), 

	[icao_code] TEXT(2), 

	[fix_identifier] TEXT(5), 

	[holding_name] TEXT(25), 

	[fix_latitude] REAL(9), 

	[fix_longitude] REAL(10), 

	[duplicate_identifier] INT(2), 

	[inbound_holding_course]REAL(5), 

	[turn_direction] TEXT(1), 

	[leg_length] REAL(3), 

	[leg_time] REAL(2), 

	[minimum_altitude] INT(5), 

	[maximum_altitude] INT(5), 

	[holding_speed] INT(3)

)

ASCII Format:

Area Code|Region Code|ICAO Code|Fix Identifier|Holding Name|Fix Latitude|Fix Longitude|Duplicate Identifier|Inbound Holding Course|Turn Direction|Leg Length|Leg Time|Minimum Altitude|Maximum Altitude|Holding Speed


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|Region Code|alphanumeric|4|5.41/2|
|ICAO Code|alphanumeric|2|5.14|
|Fix Identifier|alphanumeric|5|5.13|
|Holding Name|alphanumeric|25|5.60|
|Fix Latitude|numeric|9|5.36|
|Fix Longitude|numeric|10|5.37|
|Duplicate Identifier|numeric|2|5.114|
|Inbound Holding Course|numeric|5|5.62|
|Turn Direction|alphanumeric|1|5.63|
|Leg Length|numeric|3|5.64|
|Leg Time|numeric|2|5.65|
|Minimum Altitude|numeric|5|5.30|
|Maximum Altitude|numeric|5|5.127|
|Holding Speed|numeric|3|5.175|


Example:

* EUR|LOWW|LO|WW814|WW814|48.24151111|16.16855556|50|113.0|R|0|1|5000||210

* EUR|ENRT|ED|LMA|LIMA|51.37091389|6.394925|20|240.0|R|0|1|4000|FL240|

 

Description:

* Area Code : geographical area of the waypoint

* Region Code : static text ENRT or airport identification code 

* ICAO Code : location indicator of the waypoint

* Fix Identifier : navaid or waypoint identifier

* Holding Name : holding name

* Fix Latitude : navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)

* Fix Longitude : navaid or waypoint longitude in degrees decimal floating point (E positive, W negative)

* Duplicate Identifier : used for more than one holding patterns for a single navaid or waypoint

* Inbound Holding Course : inbound magnetic course in degrees floating point

* Turn Direction : holding turn direction (see appendix 3.8)

* Leg Length : inbound leg length in nautical miles, decimal floating point

* Leg Time : inbound leg time in minutes, decimal floating point

* Minimum Altitude : contain altitudes in feet or flight level

* Maximum Altitude : contain altitudes in feet or flight level

* Holding Speed : holding speed limit in knots

    8. **Enroute Airways**

SQL Format:

CREATE TABLE [tbl_enroute_airways] (

	[area_code] TEXT(3), 

	[route_identifier] TEXT(5), 

	[seqno] INT(4), 

	[icao_code] TEXT(2), 

	[fix_identifier] TEXT(5), 

	[fix_latitude] REAL(9), 

	[fix_longitude] REAL(10), 

	[waypoint_description_code] TEXT(4), 

	[route_type] TEXT(1), 

	[flightlevel] TEXT(1), 

	[directional_restriction] TEXT(1), 

	[crusing_table_identifier] TEXT(2), 

	[minimum_altitude1] INT(5), 

	[minimum_altitude2] INT(5), 

	[maximum_altitude] INT(5),

	[outbound_course] REAL(5),

	[inbound_course] REAL(5),

	[inbound_distance] REAL(5)

)

ASCII Format:

Area Code|Route Identifier|Sequence Number|ICAO Code|Fix Identifier|Fix Latitude|Fix Longitude|Waypoint Description Code|Route Type|Flightlevel|Direction Restriction| Cruise Table Identifier|Minimum Altitude1|Minimum Altitude2|Maximum Altitude|Outbound Course|Inbound  Course|Distance


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|Route Identifier|alphanumeric|5|5.8|
|Sequence Number|numeric|4|5.12|
|ICAO Code|alphanumeric|2|5.14|
|Fix Identifier|alphanumeric|5|5.13|
|Fix Latitude|numeric|9|5.36|
|Fix Longitude|numeric|10|5.37|
|Waypoint Description Codei|alphanumeric|4|5.17|
|Route Type|alphanumeric|1|5.7|
|Flightlevel|alphanumeric|1|5.19|
|Directional Restriction|alphanumeric|1|5.115|
|Cruise Table Identifier|alphanumeric|2|5.134|
|Minimum Altitude1|numeric|5|5.30|
|Minimum Altitude2|numeric|5|5.30|
|Maximum Altitude|alphanumeric|5|5.127|
|Outbound Course|numeric|5|5.26|
|Inbound Course|numeric|5|5.28|
|Inbound Distance |numeric|5|5.27|


Example:

* EUR|M725|LO|UMBIL|47.35331944|15.60116944|T|R|L||9000|9000|24500|100.0|237.5|34.0

* EUR|UM725|LO|UMBIL|47.35331944|15.60116944|T|R|H|B|XX|24500|26000|66000|100.0|237.5|34.0

Description:

* Area Code : geographical area of the waypoint

* Route Identifier : enroute route identifier

* Sequence Number : sort order of each enroute airway, no duplicate sequences per airway are possible

* ICAO Code : location indicator of the waypoint

* Fix Identifier : navaid or waypoint identifier

* Fix Latitude : navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)

* Fix Longitude : navaid or waypoint longitude in degrees decimal floating point (E positive, W negative)

* Waypoint Description Code : provides information on the type of fix (see appendix 3.2)

* Route Type : indicated the route type (see appendix 3.9)

* Flightlevel : defines the airway structure (see appendix 3.13)

* Directional Restriction : indicate the flyable direction (see appendix 3.14)

* Cruise Table Identifier : indicate the cruising table (see appendix 3.5)

* Minimum Altitude1 : contain altitudes in feet

* Minimum Altitude2 : contain altitudes in feet

* Maximum Altitude : contain altitudes in feet

* Outbound Course : outbound magnetic course from the waypoint identified in the record's "Fix Identifier" field

* Inbound Course : inbound magnetic course to the waypoint identified in the record's "Fix Identifier" field

* Inbound Distance : contain segment distances/along track distances/excursion distances/DME distances in nautical miles

    9. **Airports**

SQL Format:

CREATE TABLE [tbl_airports] (

	[area_code] TEXT(3), 

	[icao_code] TEXT(2) NOT NULL, 

	[airport_identifier] TEXT(4) NOT NULL, 

	[airport_identifier_3letter] TEXT(3),

	[airport_name] TEXT(30), 

	[airport_ref_latitude] REAL(9), 

	[airport_ref_longitude] REAL(10), 

	[ifr_capability] TEXT(1), 

	[longest_runway_surface_code] TEXT(1), 

	[elevation] INT(5), 

	[transition_altitude] INT(5), 

	[speed_limit] INT(3), 

	[speed_limit_altitude] INT(5),

	[iata_ata_designator] TEXT(3)

)

CREATE UNIQUE INDEX [pk_pa]

	ON [tbl_airports]

([icao_code],[airport_identifier])

ASCII Format:

Area Code|ICAO Code|Airport Identifier|Airport Identifier 3-Letter|Airport Name|Airport Ref Latitude|Airport Ref Longitude|

IFR Capability|Longest Runway Surface Code|Elevation|

Transition Altitude|Speedlimit|Speedlimit Altitude|Iata Ata Designator


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Airport Identifier|alphanumeric|4|5.6|
|Airport Identifier 3-Letter Code|Alphanumeric|3|5.6|
|Airport Name|alphanumeric|30|5.71|
|Airport Ref Latitude|numeric|11|5.36|
|Airport Ref Longitude|numeric|12|5.37|
|IFR Capability|alphanumeric|1|5.108|
|Longest Runway Surface Code|alphanumeric|1|5.249|
|Elevation|alphanumeric|5|5.55|
|Transition Altitude|numeric|5|5.53|
|Speed Limit|numeric|3|5.72|
|Speed Limit Altitude|alphanumeric|5|5.73|
|IATA/ATA Designator|alphanumeric|3|5.107|


Example:

* EUR|LO|LOWW||SCHWECHAT|48.11027778|16.56972222|Y|H|600|10000|250|FL100|VIE

Description:

* Area Code : geographical area of the airport

* ICAO Code : location indicator of the airport

* Airport Identifier : four character ICAO location identifier

* Airport Identifier 3-Letter Code : three character ICAO continental location identifier for USA and CAN airports

* Airport Name : airport name

* Airport Ref Latitude : airport reference latitude in degrees decimal floating point (N positive, S negative)

* Airport Ref Longitude : airport reference longitude in degrees decimal floating point (E positive, W negative)

* IFR Capability : indicates if the airport has a published IAP (see appendix 3.15)

* Longest Runway Surface Code : define if there is a hard runway or not (see appendix 3.16)

* Elevation : elevation in feet above MSL

* Transition Altitude : transition altitude in feet

* Speed Limit : speed limit in knots

* Speed Limit Altitude : altitude below which speed limit may be imposed (feet or flight level)

* iata_ata_designator : IATA/ATA airport designator code

    10. **Runways**

SQL Format:

CREATE TABLE [tbl_runways] (

	[area_code] TEXT(3, 

	[icao_code] TEXT(2), 

	[airport_identifier] TEXT(4) NOT NULL, 

	[runway_identifier] TEXT(3) NOT NULL, 

	[runway_latitude] REAL(9), 

	[runway_longitude] REAL(10), 

	[runway_gradient] INT(5),

	[runway_magnetic_bearing] REAL(6), 

	[runway_true_bearing] REAL(7),

	[landing_threshold_elevation] INT(5), 

	[displaced_threshold_distance] INT(4), 

	[threshold_crossing_height] INT(2), 

	[runway_length] INT(5), 

	[runway_width] INT(3), 

	[llz_identifier] TEXT(4), 

	[llz_mls_gls_category] TEXT(1)

)

CREATE UNIQUE INDEX [pk_pg]

	ON [tbl_runways]

([airport_identifier],[runway_identifier])

ASCII Format:

Area Code|ICAO Code|Airport Identifier|Runway Identifier|Runway Latitude|Runway Longitude|Runway Gradient|Runway Magnetic Bearing|Runway True Bearing|Landing Threshold Elevation|Displaced Threshold Distance|Threshold Crossing Height|Runway Length|Runway Width|LLZ Identifier|LLZ Category/Class


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Airport Identifier|alphanumeric|4|5.6|
|Runway Identifier|alphanumeric|3|5.46|
|Runway Latitude|numeric|11|5.36|
|Runway Longitude|numeric|12|5.37|
|Runway Gradient|numeric|5|5.212|
|Runway Magnetic Bearing|numeric|6|5.58|
|Runway True Bearing|numeric|7|5.94|
|Landing Threshold Elevation|numeric|5|5.68|
|Displaced Threshold Distance|numeric|4|5.69|
|Threshold Crossing Height|numeric|2|5.67|
|Runway Length|numeric|5|5.57|
|Runway Width|numeric|3|5.109|
|LLZ Identifier|alphanumeric|4|5.44|
|ILS/MLS/GLS Category|alphanumeric|1|5.80|


Example:

* EUR|LO|LOWW|29|48.10904722|16.57563056|-0.218|292.0|296.019|600||52|11483|148|OEX|3

 

Description:

* Area Code : geographical area of the runway

* ICAO Code : location indicator of the runway

* Airport Identifier : four character ICAO location identifier

* Runway Identifier : runway identifier

* Runway Latitude :  runway latitude in degrees decimal floating point (N positive, S negative)

* Runway Gradient : overall gradient in percent (positive is upward, negative is downward)

* Runway Longitude : runway longitude in degrees decimal floating point (E positive, W negative)

* Runway Magnetic Bearing : magnetic bearing of the runway identifier

* Runway True Bearing : rue bearing of the runway identifier

* Landing Threshold Elevation : elevation of the landing threshold in feet

* Displaced Threshold Distance : distance from the extremity of a runway to a threshold in feet

* Threshold Crossing Height : height above the landing threshold on a normal glide path

* Runway Length : runway length in feet

* Runway Width : runway width in feet

* LLZ Identifier : ILS/MLS/GLS facility

* ILS/MLS/GLS Category : ILS/MLS/GLS performance categories (see appendix 3.17)

    11. **Localizer/Glideslopes**

SQL Format:

CREATE TABLE [tbl_localizers_glideslopes] (

	[area_code] TEXT(3), 

	[icao_code] TEXT(2), 

	[airport_identifier] TEXT(4) NOT NULL, 

	[runway_identifier] TEXT(3), 

	[llz_identifier] TEXT(4) NOT NULL, 

	[llz_latitude] REAL(9), 

	[llz_longitude] REAL(10), 

	[llz_frequency] REAL(6), 

	[llz_bearing] REAL(6), 

	[llz_width] REAL(6),

	[ils_mls_gls_category] TEXT(1), 

	[gs_latitude] REAL(9), 

	[gs_longitude] REAL(10), 

	[gs_angle] REAL(4), 

	[gs_elevation] INT(5), 

	[station_declination] REAL(5)

)

CREATE UNIQUE INDEX [pk_pi]

	ON [tbl_localizers_glideslopes]

([airport_identifier],[llz_identifier])

ASCII Format:

Area Code|ICAO Code|Airport Identifier|Runway Identifier|LLZ Identifier|LLZ Latitude|LLZ Longitude|LLZ Frequency|LLZ Bearing|LLZ Width|ILS Category|GS Latitude|GS Longitude|GS Angle|GS Elevation|Station Declination


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Airport Identifier|alphanumeric|4|5.6|
|Runway Identifier|alphanumeric|3|5.46|
|LLZ Identifier|alphanumeric|4|5.44|
|LLZ Latitude|numeric|9|5.36|
|LLZ Longitude|numeric|10|5.37|
|LLZ Frequency|numeric|6|5.45|
|LLZ Bearing|numeric|6|5.47|
|LLZ Width|numeric|6|5.51|
|ILS/MLS/GLS Category|alphanumeric|1|5.80|
|GS Latitude|numeric|9|5.36|
|GS Longitude|numeric|10|5.37|
|GS Angle|numeric|4|5.52|
|GS Elevation|numeric|5|5.74|
|Station Declination|numeric|5|5.66|


Example:

* EUR|LO|LOWW|29|OEX|48.124775|16.52732778|109.55|292.0|0.0|3|48.10894444|16.57161111|3.00|658|4.0

 

Description:

* Area Code : geographical area of the localizer

* ICAO Code : location indicator of the localizer

* Airport Identifier : four character ICAO location identifier

* Runway Identifier : runway identifier

* LLZ Identifier : identification code of the LLZ, MLS facility or GLS reference path

* LLZ Latitude : LLZ latitude in degrees decimal floating point (N positive, S negative)

* LLZ Longitude : LLZ longitude in degrees decimal floating point (E positive, W negative)

* LLZ Frequency : VHF frequency of the facility in MHz

* LLZ Bearing : magnetic bearing of the localizer course

* LLZ Width : specifies the localizer course width (in degrees) of the ILS facility

* ILS/MLS/GLS Category : ILS/MLS/GLS performance categories (see appendix 3.17)

* GS Latitude : GS latitude in degrees decimal floating point (N positive, S negative)

* GS Longitude : GS longitude in degrees decimal floating point (E positive, W negative)

* GS Angle : glide slope angle of an ILS facility/GLS approach in degrees

* GS Elevation : elevation of LLZ in feet

* Station Declination : angular difference between true north and the zero degree radial of the LLZ in degrees

    12. **Localizer Marker**

SQL Format:

CREATE TABLE [tbl_localizer_marker] (

	[area_code] TEXT(3), 

	[icao_code] TEXT(2), 

	[airport_identifier] TEXT(4) NOT NULL, 

	[runway_identifier] TEXT(3), 

	[llz_identifier] TEXT(4) NOT NULL, 

	[marker_identifier] TEXT(4) NOT NULL,

	[marker_type] TEXT(3), 

	[marker_latitude] REAL(9), 

	[marker_longitude] REAL(10)

)

CREATE UNIQUE INDEX [pk_pm]

	ON [tbl_localizer_marker]

([airport_identifier],[llz_identifier])

ASCII Format:

Area Code|ICAO Code|Airport Identifier|Runway Identifier|LLZ Identifier|Marker Type|Marker Latitude|Marker Longitude 


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Airport Identifier|alphanumeric|4|5.6|
|Runway Identifier|alphanumeric|3|5.46|
|LLZ Identifier|alphanumeric|4|5.44|
|Marker Identifier|Alphanumeric|5|-|
|Marker Type|numeric|3|5.99|
|Marker Latitude|numeric|9|5.36|
|Marker Longitude|numeric|10|5.37|


Example:

* EUR|ED|EDDT|08L|ITLE|OM08L| OM|52.54570278|13.15108333 

Description:

* Area Code : geographical area of the marker

* ICAO Code : location indicator of the marker

* Airport Identifier : four character ICAO location identifier

* Runway Identifier : runway identifier

* LLZ Identifier : identification code of the LLZ, MLS facility or GLS reference path

* Marker Identifier: marker identifier

* Marker Type : defines the type of marker (see appendix 3.18)

* Marker Latitude : marker latitude in degrees decimal floating point (N positive, S negative)

* Marker Longitude : marker longitude in degrees decimal floating point (E positive, W negative)

    13. **Terminal Procedures** 

SQL Format:

CREATE TABLE [tbl_sids/stars/iaps] (

	[area_code] TEXT(3), 

	[icao_code] TEXT(2), 

	[airport_identifier] TEXT(4), 

	[procedure_identifier] TEXT(6), 

	[route_type] TEXT(1), 

	[transition_identifier] TEXT(5), 

	[seqno] INT(3), 

	[waypoint_identifier] TEXT(5), 

	[waypoint_latitude] REAL(9), 

	[waypoint_longitude] REAL(10), 

	[waypoint_description_code] TEXT(4), 

	[turn_direction] TEXT(1), 

	[rnp] REAL(4), 

	[path_termination] TEXT(2), 

	[recommanded_navaid] TEXT(4), 

	[recommanded_navaid_latitude] REAL(9), 

	[recommanded_navaid_longitude] REAL(10), 

	[arc_radius] REAL(7), 

	[theta] REAL(5), 

	[rho] REAL(5), 

	[magnetic_course] REAL(5), 

	[route_distance_holding_distance_time] REAL(5), 

	[distance_time] TEXT(1),

	[altitude_description] TEXT(1), 

	[altitude1] INT(5), 

	[altitude2] INT(5), 

	[transition_altitude] INT(5), 

	[speed_limit_description] TEXT(1), 

	[speed_limit] INT(3), 

	[vertical angle] REAL(4), 

	[center_waypoint] TEXT(5), 

	[center_waypoint_latitude] REAL(9), 

	[center_waypoint_longitude] REAL(9)

)

ASCII Format:

Area Code|ICAO Code|Airport Identifier|Procedure Identifier|Route Type|Transition Identifier|Sequence Number|Waypoint Identifier|Waypoint Latitude|Waypoint Longitude|Waypoint Description Code|Turn Direction|RNP|Path and Termination|Recommanded Navaid|Recommanded Navaid Latitude|Recommanded Navaid Longitude|ARC Radius|Theta|Rho|Magnetic Course|Route Distance/Holding Distance or Time|Distance Time|Altitude Description|Altitude1|Altitude2|Transition Altitude|Speed Limit Description|Speed Limit|Vertical Angle|Center Waypoint|Center Waypoint Latitude|Center Wayoint Longitude


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Airport Identifier|alphanumeric|4|5.6|
|Procedure Identifier|alphanumeric|6|5.9/10|
|Route Type|alphanumeric|1|5.7|
|Transition Identifier|alphanumeric|5|5.11|
|Sequence Number|numeric|3|5.12|
|Waypoint Identifier|alphanumeric|5|5.13|
|Waypoint Latitude|numeric|9|5.36|
|Waypoint Longitude|numeric|10|5.37|
|Waypoint Description Code|alphanumeric|4|5.17|
|Turn Direction|alphanumeric|1|5.20|
|RNP|numeric|5|5.211|
|Path and Termination|alphanumeric|2|5.21|
|Recommended Navaid|alphanumeric|4|5.23|
|Recommended Navaid Latitude|numeric|9|5.36|
|Recommended Navaid Longitude|numeric|10|5.37|
|ARC Radius|numeric|7|5.204|
|Theta|numeric|5|5.24|
|Rho|numeric|5|5.25|
|Magnetic Course|numeric|5|5.26|
|Route Distance/Holding Distance or Time|numeric|5|5.27|
|Distance Time|alphanumeric|1|-|
|Altitude Description|alphanumeric|1|5.29|
|Altitude1|numeric|5|5.30|
|Altitude2|numeric|5|5.30|
|Transition Altitude|numeric|5|5.53|
|Speed Limit Description|alphanumeric|1|5.261|
|Speed Limit|numeric|3|5.72|
|Vertical Angle|numeric|4|5.70|
|Center Waypoint|alphanumeric|5|5.144|
|Center Waypoint Latitude|numeric|9|5.36|
|Center Waypoint Longitude|numeric|10|5.37|


Example:

* EUR|LO|LOWW|ADAM1A|5|RW11|10|WW100|48.08981667|16.63360278|EY|||CF|FMD|48.10511389|16.62926389||165.2|0.9|112.0|3.0||||10000|-|205||||||

Description:

* Area Code : geographical area of the marker

* ICAO Code : location indicator of the marker

* Airport Identifier : four character ICAO location identifier

* Procedure Identifier : name of the terminal procedure

* Route Type : element of the terminal procedure (see appendix 3.10/3.11/3.12)

* Transition Identifier : describes the type of transition to be made from the enroute environment into the terminal area and vice versa

* Sequence Number : sequence definition phase of the terminal procedure

* Waypoint Identifier : : navaid or waypoint identifier

* Waypoint Latitude : navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)

* Fix Longitude : navaid or waypoint longitude in degrees decimal floating point (E positive, W negative)

* Waypoint Description Code : the field provides information on the type of fix (see appendix 3.2)

* Turn Direction : turn direction (see appendix 3.8)

* RNP : statement of the Navigation Performance necessary for operation within a defined airspace in accordance with ICAO Annex 15 and/or State published rules

* Path and Termination : defines the path geometry for a single record of an terminal procedure (see appendix 3.21)

* Recommended Navaid : reference facility for the fix

* Recommended Navaid Latitude : recommended navaid latitude in degrees decimal floating point (N positive, S negative)

* Recommended Navaid Longitude : recommended navaid longitude in degrees decimal floating point (E positive, W negative)

* ARC Radius : used to define the radius of a precision turn

* Theta : defined as the magnetic bearing to the waypoint identified in the record's "Fix Identifier" field from the Navaid in the "Recommended Navaid" field

* Rho : defined as the geodesic distance in nautical miles to the waypoint identified in the record's "Fix Identifier" field from the NAVAID in the "Recommended Navaid" field

* Magnetic Course : outbound magnetic course from the waypoint identified in the record's "Fix Identifier" field

* Route Distance/Holding Distance or Time : contain segment distances/along track distances/excursion distances/DME distances

* Distance/Time : indicates, if the value in the "Route Distance/Holding" column references to a time value, or distance value (see appendix 3.36)

* Altitude Description : designate whether a waypoint should be crossed (see appendix 3.24)

* Altitude1 : contain altitudes in feet or flight level

* Altitude2 : contain altitudes in feet or flight level

* Transition Altitude : transition altitude in feet

* Speed Limit Description : designate whether the speed limit coded at a fix in a terminal procedure description is a mandatory, minimum or maximum speed (see appendix 3.22)

* Speed Limit : speed limit in knots

* Vertical Angle : defines the vertical navigation path prescribed for the procedure

* Center Waypoint : represents the MSA Center, that point (Navaid or Waypoint) on which the MSA is predicated

* Center Waypoint Latitude : center fix latitude in degrees decimal floating point (N positive, S negative)

* Center Waypoint Longitude : center fix longitude in degrees decimal floating point (E positive, W negative)

    14. **Airport Communication**

SQL Format:

CREATE TABLE [tbl_airport_communication] (

	[area_code] TEXT(3), 

	[icao_code] TEXT(2), 

	[airport_identifier] TEXT(4), 

	[communication_type] TEXT(3), 

	[communication_frequency]REAL(5), 

	[frequency_units] TEXT(1), 

	[service_indicator] TEXT(3), 

	[callsign] TEXT(25), 

	[latitude] REAL(9), 

	[longitude] REAL(10)

)

ASCII Format:

Area Code|ICAO Code|Airport Identifier|Communication Type|Communication Frequency|Frequency Units|Service Indicator|Callsign|Latitude|Longitude 


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Airport Identifier|alphanumeric|4|5.6|
|Communication Type|alphanumeric|3|5.101|
|Communication Frequency|numeric|5|5.103|
|Frequency Units|alphanumeric|1|5.104|
|Service Indicator|alphanumeric|3|5.106|
|Callsign|alphanumeric|25|5.105|
|Latitude|numeric|9|5.36|
|Longitude|numeric|10|5.37|


Example:

* EUR|LO|LOWW|APP|118.775|V||WIEN RADAR|48.11027778| 16.56972222

Description:

* Area Code : geographical area of the facility

* ICAO Code : location indicator of the facility

* Airport Identifier : four character ICAO location identifier

* Communication Type : specified the type of communication unit (see appendix 3.32)

* Communication Frequency : specifies a frequency for the facility identified in the communicaton type field

* Frequency Units : designate the frequency spectrum area for the frequency (see appendix 3.30)

* Service Indicator : define the use of the frequency for the specified communication type (see appendix 3.33)

* Callsign : name of the facility being called

* Latitude : latitude in degrees decimal floating point (N positive, S negative)

* Longitude : longitude in degrees decimal floating point (E positive, W negative)

    15. **Enroute Communication**

SQL Format:

CREATE TABLE [tbl_enroute_communication] (

[area_code] TEXT(3), 

[fir_rdo_ident] TEXT(4), 

[fir_uir_indicator] TEXT(1), 

[communication_type] TEXT(3),

[communication_frequency] REAL(5), 

[frequency_units] TEXT(1), 

[service_indicator] TEXT(3),

[remote_name] TEXT(25),

[callsign] TEXT(30), 

[latitude] REAL(9), 

[longitude] REAL(10)

)

ASCII Format:

Area Code|FIR/RDO Ident|FIR/UIR Indicator|Communication Type|Comm Frequency|Frequency Units|Service Indicator|Remote Name|CallSign|Latitude|Longitude


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|FIR/RDO Ident|alphanumeric|4|5.190|
|FIR/UIR Indicator|alphanumeric|1|5.117|
|Communication Type|alphanumeric|3|5.101|
|Communication Frequency|numeric|5|5.103|
|Frequency Units|alphanumeric|1|5.104|
|Service Indicator|alphanumeric|3|5.106|
|Remote Name|alphanumeric|25|5.189|
|Callsign|alphanumeric|30|5.105|
|Latitude|numeric|9|5.36|
|Longitude|numeric|10|5.37|


Example:

* EUR|LOVV|F|||130.475||||VIENNA RADAR|48.11027778| 16.56972222

Description:

* Area Code : geographical area of the facility

* FIR/RDO Ident : identifies the Flight Information Region or Upper Information Region

* FIR/UIR Indicator : contain the identifier of a FIR, UIR or combined FIR/UIR (see appendix 3.6)

* Communication Type : specified the type of communication unit (see appendix 3.32)

* Communication Frequency : specifies a frequency for the facility identified in the communicaton type field

* Frequency Units : designate the frequency spectrum area for the frequency (see appendix 3.30)

* Service Indicator : define the use of the frequency for the specified communication type (see appendix 3.33)

* Remote Name : name of unmanned air/ground facility

* Callsign : name of the facility being called

* Latitude : latitude in degrees decimal floating point (N positive, S negative)

* Longitude : longitude in degrees decimal floating point (E positive, W negative)

    16. **Grid Mora**

SQL Format:

CREATE TABLE [tbl_grid_mora] (

[starting_latitude] INT(3), 

[starting_longitude] INT(4), 

[mora01] TEXT(3), 

[mora02] TEXT(3), 

[mora03] TEXT(3), 

[mora04] TEXT(3), 

[mora05] TEXT(3), 

[mora06] TEXT(3), 

[mora07] TEXT(3), 

[mora08] TEXT(3), 

[mora09] TEXT(3), 

[mora10] TEXT(3), 

[mora11] TEXT(3), 

[mora12] TEXT(3), 

[mora13] TEXT(3), 

[mora14] TEXT(3), 

[mora15] TEXT(3), 

[mora16] TEXT(3), 

[mora17] TEXT(3), 

[mora18] TEXT(3), 

[mora19] TEXT(3), 

[mora20] TEXT(3), 

[mora21] TEXT(3), 

[mora22] TEXT(3), 

[mora23] TEXT(3), 

[mora24] TEXT(3), 

[mora25] TEXT(3), 

[mora26] TEXT(3), 

[mora27] TEXT(3), 

[mora28] TEXT(3), 

[mora29] TEXT(3), 

[mora30] TEXT(3)

)

ASCII Format:

Start Latitude|Start Longitude|Mora01|Mora02|Mora03|Mora04|Mora05|Mora06|Mora07|Mora08|Moa09|Mora10|Mora11|Mora12|Mora13|Mora14|Mora15|Mora16|Mora17|Mora18|Mora19|Mora20|Mora21|Mora22|Mora23|Mora24|Mora25|Mora26|Mora27|Mora28|Mora29|Mora30


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Starting Latitude|numeric|3|5.36|
|Starting Longitude|numeric|4|5.37|
|Mora01 – Mora30|numeric|5|5.143|


Example:

* N48|E000|035|035|035|035|035|035|064|064|055|046|041|037|050|073|057|050|042|046|076|091|088|054|073|078|088|065|029|026|025|027

Description:

* Starting Latitude : latitude in degrees decimal floating point (N positive, S negative)

* Starting Longitude : longitude in degrees decimal floating point (E positive, W negative)

* Mora01 – Mora30: terrain and obstruction clearance within the section outlined by latitude and longitude blocks in feet

    17. **Airport MSA**

SQL Format:

CREATE TABLE [tbl_airport_msa] (

	[area_code] TEXT(3), 

	[icao_code] TEXT(2),

	[airport_identifier] TEXT(4),

	[msa_center] TEXT(5),

	[msa_center_latitude] REAL(9),

	[msa_center_longitude] REAL(10),

	[magnetic_true_indicator] TEXT(1),

	[multiple_code] TEXT(1),

	[radius_limit] INT(2),

	[sector_bearing_1] INT(3),

	[sector_altitude_1] INT(3),

	[sector_bearing_2] INT(3),

	[sector_altitude_2] INT(3),

[sector_bearing_3] INT(3),

	[sector_altitude_3] INT(3),

	[sector_bearing_4] INT(3),

	[sector_altitude_4] INT(3),

	[sector_bearing_5] INT(3),

	[sector_altitude_5] INT(3)

)

ASCII Format:

Area Code|ICAO Code|Airport Identifier|MSA Center|MSA Center Latitude|MSA Center Longitude|Magnetic/True/Indicator|Multiple Code|Radius Limit|Sector Bearing 1|Sector Altitude 1|Sector Bearing 2|Sector Altitude 2|Sector Bearing 3|Sector Altitude 3|Sector Bearing 4|Sector Altitude 4|Sector Bearing 5|Sector Altitude 5


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Airport Identifier|alphanumeric|4|5.6|
|MSA Center|alphanumeric|5|5.144|
|MSA Center Latitude|numeric|9|5.36|
|MSA Center Longitude|numeric|10|5.37|
|Magnetic/True Indicator|alphanumeric|1|5.165|
|Multiple Code|alphanumeric|1|5.130|
|Radius Limit|numeric|2|5.145|
|Sector Bearing 1-5|numeric|3|5.146|
|Sector Altitude 1-5|numeric|3|5.147|


Example:

* EUR|LO|LOWW|BRK|48.06292222|16.71674722|270|2700|25|360|4200|10|||||||||||||||

Description:

* Area Code : geographical area

* ICAO Code : location indicator of the airport

* Airport Identifier : four character ICAO location identifier

* MSA Center : MSA center fix 

* MSA Center Latitude : MSA Center fix latitude in degrees decimal floating point (N positive, S negative)

* MSA Center Longitude : MSA Center fix longitude in degrees decimal floating point (E positive, W negative)

* Multiple Code : indicate Restrictive Airspace having the same designator but subdivided or differently divided by lateral and/or vertical detail

* Sector Bearing 1-7 : sector bearing in degrees

* Sector Altitude 1-7 : sector altitude in feet

* Sector Radius 1-7 : radius from MSA Center fix in nautical miles

    18. **Enroute Airways Restriction**

SQL Format:

CREATE TABLE [tbl_enroute_airway_restriction] (

	[area_code] TEXT(3), 

	[route_identifier] TEXT(5),

	[restriction_identifier]  INT(3),

	[restriction_type] TEXT(2),

	[start_waypoint_identifier] TEXT(5),

	[start_waypoint_latitude] REAL(9),

	[start_waypoint_longitude] REAL(10),

	[end_waypoint_identifier] TEXT(5),

	[end_waypoint_latitude] REAL(9),

	[end_waypoint_longitude] REAL(10),

	[start_date] TEXT(7),

	[end_date] TEXT(7),

	[units_of_altitude] TEXT(1),

	[restriction_altitude1] INT(3),

	[block_indicator1] TEXT(1),

	[restriction_altitude2] INT(3),

	[block_indicator2] TEXT(1),

	[restriction_altitude3] INT(3),

	[block_indicator3] TEXT(1),

	[restriction_altitude4] INT(3),

	[block_indicator4] TEXT(1),

	[restriction_altitude5] INT(3),

	[block_indicator5] TEXT(1),

	[restriction_altitude6] INT(3),

	[block_indicator6] TEXT(1),

	[restriction_altitude7] INT(3),

	[block_indicator7] TEXT(1)

)

ASCII Format:

Area Code|Route Identifier|Restriction Identifier|Restriction Type|Start Waypoint Identifier|Start Waypoint Latitude|Start Waypoint Longitude|End Waypoint Identifier|End Waypoint Latitude|End Waypoint Longitude|Start Date|End Date|Units of Altitude|Restriction Altitude1|Block Indicator1|Restricted Altitude2|Block Indicator2|Restricted Altitude3|Block Indicator3|Restricted Altitude4|Block Indicator4|Restricted Altitude5|Block Indicator5|Restricted Altitude6|Block Indicator6|Restricted Altitude7|Block Indicator7


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|Route Identifier|alphanumeric|5|5.8|
|Restriction Identifier|numeric|3|5.154|
|Restriction Type|alphanumeric|2|5.201|
|Start Waypoint Identifier|alphanumeric|5|5.13|
|Start Waypoint Latitude|numeric|9|5.36|
|Start Waypoint Longitude|numeric|10|5.37|
|End Waypoint Identifier|alphanumeric|5|5.13|
|End Waypoint Latitude|numeric|9|5.36|
|End Waypoint Longitude|numeric|10|5.37|
|Start Date|alphanumeric|7|5.157|
|End Date|alphanumeric|7|5.157|
|Units of Altitude|alphanumeric|1|5.160|
|Restriction Altitude1-7|numeric|3|5.161|
|Block Indicator1-7|alphanumeric|1|5.203|


Example:

* EUR|M984|033|NR|STO|48.41713611|16.01859444|MIKOV|48.78474444|16.62100278||||||||||||||||||

Description:

* Area Code : geographical area

* Route Identifier : enroute route identifier

* Restriction Identifier : assign a unique identifier

* Restriction Type : define the type of the restriction (see appendix 3.27)

* Start Waypoint Identifier : start fix

* Start Waypoint Latitude : start fix latitude in degrees decimal floating point (N positive, S negative)

* Start Waypoint Longitude : start fix longitude in degrees decimal floating point (E positive, W negative)

* End Waypoint Identifier : end fix 

* End Waypoint Latitude : end fix latitude in degrees decimal floating point (N positive, S negative)

* End Waypoint Longitude : end fix longitude in degrees decimal floating point (E positive, W negative)

* Start Date: specific the effective date which does not corresponding with the AIRAC date

* End Date: specific the effective date which does not corresponding with the AIRAC date

* Units of Altitude : indicate the units of measurement for the restriction altitudes (see appendix 3.28)

* Restriction Altitude1-7 : specify the altitude profile for a specific restriction

* Block Indicator1-7 : specify that the altitudes that follow in the restriction record (see appendix 3.29)

    19. **Controlled Airspace**

SQL Format:

CREATE TABLE [tbl_controlled_airspace] (

	[area_code] TEXT(3), 

	[icao_code] TEXT(2), 

	[airspace_center] TEXT(5), 

	[controlled_airspace_name] TEXT(30), 

	[airspace_type] TEXT(1), 

	[airspace_classification] TEXT(1), 

	[multiple_code] TEXT(1),

	[time_code] TEXT(1),

	[seqno] INT(3), 

	[flightlevel] TEXT(1),

	[boundary_via] TEXT(2),

	[latitude] REAL(9), 

	[longitude] REAL(10),

	[arc_origin_latitude] REAL(9),

	[arc_origin_longitude] REAL(10),

	[arc_distance] REAL(5),

	[arc_bearing]REAL(5),

	[unit_indicator_lower_limit] TEXT(1),

	[lower_limit] TEXT(5),

	[unit_indicator_upper_limit] TEXT(1),

	[upper_limit] TEXT(5)

)

Sort Order:

[rowid]

ASCII Format:

Area Code|Area Code|Airspace Center|Controlled Airspace Name|Airspace Type|Airspace Classification|Multiple Code|Time Code|Sequence Number|Flightlevel|Boundary Via|Latitude|Longitude|Arc Origin Latitude|Arc Origin Longitude|Arc Distance|Arc Bearing|Unit Indicator Lower Limit|Lower Limit|Unit Indicator Upper Limit|Upper Limit


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Airspace Center|alphanumeric|5|5.214|
|Controlled Airspace Name|alphanumeric|30|5.216|
|Airspace Type|alphanumeric|1|5.213|
|Airspace Classification|alphanumeric|1|5.215|
|Multiple Code|alphanumeric|1|5.130|
|Time Code|alphanumeric|1|5.131|
|Sequence Number|numeric|4|5.12|
|Flightlevel|alphanumeric|1|5.19|
|Boundary Via|alphanumeric|2|5.118|
|Latitude|numeric|9|5.36|
|Longitude|numeric|10|5.37|
|Arc Origin Latitude|numeric|9|5.36|
|Arc Origin Longitude|numeric|10|5.37|
|Arc Distance|numeric|5|5.119|
|Arc Bearing|numeric|5|5.120|
|Unit Indicator Lower Limit|alphanumeric|1|5.133|
|Lower Limit|alphanumeric|5|5.121|
|Unit Indicator Upper Limit|alphanumeric|1|5.133|
|Upper Limit|alphanumeric|5|5.121|


Example:

* EUR|LOWW|VIENNA CTR|M|D|10|L||48.30611111|16.60305556||||||GND|M|2500

Description:

* Area Code : geographical area

* ICAO Code : location indicator of the airspace center

* Airspace Center : define the navigation element upon which the controlled airspace being defined is predicated, but not necessarily centered

* Controlled Airspace Name : the name of the controlled airspace when assigned

* Airspace Type : indicate the type of controlled airspace (see appendix 3.23)

* Airspace Classification : indicating the published classification of the controlled airspace, when assigned

* Multiple Code : indicate Restrictive Airspace having the same designator but subdivided or differently divided by lateral and/or vertical detail

* Time Code : Active Time (see appendix 3.35 )

* Airspace Type : indicated restrictive Airspace Areas or MSA Centers having the same designator but subdivided or differently divided by lateral and/or vertical detail

* Sequence Number : sort order of each airspace, no duplicate sequences per airspace are possible

* Flightlevel : defines the airway structure (see appendix 3.13)

* Boundary Via : defines the path of the boundary from the position identified in the record to the next defined position (see appendix 3.25)

* Latitude : latitude in degrees decimal floating point (N positive, S negative)

* Longitude : longitude in degrees decimal floating point (E positive, W negative)

* Arc Origin Latitude : arc origin latitude in degrees decimal floating point (N positive, S negative)

* Arc Origin Longitude : arc origin longitude in degrees decimal floating point (E positive, W negative)

* Arc Distance : define the distance in nautical miles from the "Arc Origin" position

* Arc Bearing : contains the true bearing from the "Arc Origin" position to the beginning of the arc

* Unit Indicator Lower Limit : specified as "above mean sea level" (MSL) or "above ground level" (AGL)

* Lower Limit : contain the lower limits

* Unit Indicator Upper Limit : specified as "above mean sea level" (MSL) or "above ground level" (AGL)

* Upper Limit : contain the upper limits

    20. **Cruising Tables**

SQL Format:

CREATE TABLE [tbl_cruising_tables] (

	[cruise_table_identifier] TEXT(2), 

	[seqno] INT(3), 

	[course_from] REAL(5), 

	[course_to] REAL(5), 

	[mag_true] TEXT(1), 

	[cruise_level_from1] INT(5), 

	[vertical_separation1]INT(5), 

	[cruise_level_to1] INT(5), 

	[cruise_level_from2] INT(5), 

	[vertical_separation2] INT(5), 

	[cruise_level_to2] INT(5), 

	[cruise_level_from3] INT(5), 

	[vertical_separation3] INT(5), 

	[cruise_level_to3] INT(5), 

	[cruise_level_from4] INT(5), 

	[vertical_separation4] INT(5), 

	[cruise_level_to4] INT(5)

)

ASCII Format:

Cruise Table Identifier|Sequence Number|Course From|Course To|Mag/True|Cruise Level From 1|Vertical Separation 1|Cruise Level To 1|Cruise Level From 2|Vertical Separation 2|Cruise Level To 2|Cruise Level From 3|Vertical Separation 3|Cruise Level To 3|Cruise Level From 4|Vertical Separation 4|Cruise Level To 4


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Cruise Table Identifier|alphanumeric|2|5.134|
|Sequence Number|numeric|1|5.12|
|Course From|numeric|5|5.135|
|Course To|numeric|5|5.135|
|Mag/True|alphanumeric|1|5.165|
|Cruise Level From 1|numeric|5|5.136|
|Vertical Separation 1|numeric|5|5.136|
|Cruise Level To 1|numeric|5|5.136|
|Cruise Level From 2|numeric|5|5.136|
|Vertical Separation 2|numeric|5|5.136|
|Cruise Level To 3|numeric|5|5.136|
|Cruise Level From 4|numeric|5|5.136|
|Vertical Separation 4|numeric|5|5.136|
|Cruise Level To 4|numeric|5|5.136|


Example:

* A4|1|360.0|179.0|M|200|2000|27000|27000|4000|31000|31000|1000|32000|32000|3000|

Description:

* Cruise Table Identifier : indicate the cruising table (see appendix 3.5)

* Sequence Number : sort order of each cruise table, no duplicate sequences per cruise table are possible

* Course From : indicate the lowest course for which a block of cruising levels are prescribed

* Course To : indicate the highest course for which a block of cruising levels is prescribed

* Mag/True : course from/to in magnetic or true degrees

* Cruise Level From 1-4: indicate the lowest cruising level prescribed for use within the Course From/To fields

* Vertical Separation 1-4 : indicate the minimum separation prescribed to be maintained between the cruising levels

* Cruise Level To 1-4 : indicate the highest cruising level prescribed for use within the Course From/To fields

    21. **FIR/UIR**

SQL Format:

CREATE TABLE [tbl_fir_uir] (

	[area_code] TEXT(3), 

	[fir_uir_identifier] TEXT(4), 

	[fir_uir_address] TEXT(4),

	[fir_uir_name] TEXT(25), 

	[fir_uir_indicator] TEXT(1), 

	[seqno] INT(3), 

	[boundary_via] TEXT(2), 

	[adjacent_fir_identifier] TEXT(4), 

	[adjacent_uir_identifier] TEXT(4), 

	[reporting_units_speed] INT(1), 

	[reporting_units_altitude] INT(1), 

	[fir_uir_latitude] REAL(9), 

	[fir_uir_longitude] REAL(10), 

	[arc_origin_latitude] REAL(9),

	[arc_origin_longitude] REAL(10),

	[arc_distance] REAL(5),

	[arc_bearing] REAL(5),

	[fir_upper_limit] TEXT(5),

	[uir_lower_limit] TEXT(5),

	[uir_upper_limit] TEXT(5),

	[cruise_table_idenfier] TEXT(2)

)

Sort Order:

[rowid]

ASCII Format:

Area Code|FIR/UIR Identifier|FIR/UIR Address|FIR/UIR Name|FIR/UIR Indicator|Sequence Number|Boundary Via|Adjacent FIR Identifier|Adjacent UIR Identifier|Reporting Units Speed|Reporting Units Altitude| FIR/UIR Latitude|FIR/UIR Longitude|Arc Origin Latitude|Arc Origin Longitude|Arc Distance|Arc Bearing|FIR Upper Limit|UIR Lower Limit|UIR Upper Limit|Cruise Table Identifier


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|FIR/UIR Identifier|alphanumeric|4|5.116|
|FIR/UIR Address|Alphanumeric|4|5.151|
|FIR/UIR Name|alphanumeric|25|5.125|
|FIR/UIR Indicator|alphanumeric|1|5.117|
|Sequence Number|numeric|4|5.12|
|Boundary Via|alphanumeric|2|5.118|
|Adjacent FIR Identifier|alphanumeric|4|5.116|
|Adjacent UIR Identifier|alphanumeric|4|5.116|
|Reporting Units Speed|numeric|1|5.122|
|Reporting Units Altitude|numeric|1|5.123|
|FIR/UIR Latitude|numeric|9|5.36|
|FIR/UIR Longitude|numeric|10|5.37|
|Arc Origin Latitude|numeric|9|5.36|
|Arc Origin Longitude|numeric|10|5.37|
|Arc Distance|numeric|5|5.119|
|Arc Bearing|numeric|5|5.120|
|FIR Upper Limit|alphanumeric|5|5.121|
|UIR Lower Limit|alphanumeric|5|5.121|
|UIR Upper Limit|alphanumeric|5|5.121|
|Cruise Table Identifier|alphanumeric|2|5.134|


Example:

* EUR|LOVV|ZRZX|VIENNA|F|10|G|LKAA||1|1|49.000000|15.00388889|||||UNLTD|||XX

Description:

* Area Code : geographical area

* FIR/UIR Identifier : identifies the Flight Information Region and Upper Information Region of airspace

* FIR/UIR Address : communication address of the FIR/UIR to supplement the FIR/UIR ident (see appendix 3.34)

* FIR/UIR Name : the name of the controlled airspace when assigned

* FIR/UIR Indicator : indicate the type of controlled airspace (see appendix 3.6)

* Sequence Number : sort order of each airspace, no duplicate sequences per airspace are possible

* Boundary Via : defines the path of the boundary from the position identified in the record to the next defined position (see appendix 3.25)

* Adjacent FIR Identifier : identifies the Flight Information Region and Upper Information Region of airspace

* Adjacent UIR Identifier : identifies the Flight Information Region and Upper Information Region of airspace

* Reporting Units Speed : indicate the units of measurement concerning True Air Speed used in the specific FIR/UIR (see appendix 3.19)

* Reporting Units Altitude : indicate the units of measurement concerning the altitude used in the specific FIR/UIR (see appendix 3.20)

* FIR/UIR Latitude : FIR/UIR latitude in degrees decimal floating point (N positive, S negative)

* FIR/UIR Longitude : FIR/UIR longitude in degrees decimal floating point (E positive, W negative)

* Arc Origin Latitude : arc origin latitude in degrees decimal floating point (N positive, S negative)

* Arc Origin Longitude : arc origin longitude in degrees decimal floating point (E positive, W negative)

* Arc Distance : define the distance in nautical miles from the "Arc Origin" position

* Arc Bearing : contains the true bearing from the "Arc Origin" position to the beginning of the arc

* FIR Upper Limit : contain the FIR upper limits

* UIR Lower Limit : contain the UIR lower limits

* UIR Upper Limit : contain the UIR upper limits

* Cruise Table Identifier : indicate the cruising table (see appendix 3.5)

    22. **Restrictive Airspace**

SQL Format:

CREATE TABLE [tbl_restrictive_airspace] (

	[area_code] TEXT(3), 

	[icao_code] TEXT(2), 

	[restrictive_airspace_designation] TEXT(10), 

	[restrictive_airspace_name] TEXT(30), 

	[restrictive_type] TEXT(1), 

	[multiple_code] TEXT(1), 

	[seqno] INT(3), 

	[boundary_via] TEXT(2), 

	[flightlevel] TEXT(1), 

	[latitude] REAL(9), 

	[longitude] REAL(10), 

	[arc_origin_latitude] REAL(9), 

	[arc_origin_longitude] REAL(10),

	[arc_distance] REAL(5),

	[arc_bearing] REAL(5),

	[unit_indicator_lower_limit] TEXT(1),

	[lower_limit] TEXT(5),

	[unit_indicator_upper_limit] TEXT(1),

	[upper_limit] TEXT(5)

)

Sort Order:

[rowid]

ASCII Format:

Area Code|ICAO Code|Restrictive Airspace Designation|Restrictive Airspace Name|Restrictive Type|Multiple Code|Sequence Number|Boundary Via|Flightlevel|Latitude|Longitude|Arc Origin Latitude|Arc Origin Longitude|Arc Distance|Arc Bearing|Unit Indicator Lower Limit|Lower Limit|Unit Indicator Upper Limit|

Upper Limit


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|ICAO Code|alphanumeric|2|5.14|
|Restrictive Airspace Designation|alphanumeric|10|5.129|
|Restrictive Airspace Name|alphanumeric|30|5.126|
|Restrictive Type|alphanumeric|1|5.128|
|Multiple Code|alphanumeric|1|5.130|
|Sequence Number|numeric|4|5.12|
|Boundary Via|alphanumeric|2|5.118|
|Flightlevel|alphanumeric|1|5.19|
|Latitude|numeric|9|5.36|
|Longitude|numeric|10|5.37|
|Arc Origin Latitude|numeric|9|5.36|
|Arc Origin Longitude|numeric|10|5.37|
|Arc Distance|numeric|5|5.119|
|Arc Bearing|numeric|5|5.120|
|Unit Indicator Lower Limit|alphanumeric|1|5.133|
|Lower Limit|alphanumeric|5|5.121|
|Unit Indicator Upper Limit|alphanumeric|1|5.133|
|Upper Limit|alphanumeric|5|5.121|


Example:

* EUR|LO|21|LIZUM|D||10|G|L|47.225000|11.61666667||||||GND|M|21000

Description:

* Area Code : geographical area of the airspace

* ICAO Code : location indicator of the airspace

* Restrictive Airspace Designation : contains the number or name that uniquely identifies the restrictive airspace

* Restrictive Airspace Name : name of the restrictive airspace when assigned

* Restrictive Type : indicate the type of Airspace in which the flight of aircraft is prohibited or restricted (see appendix 3.26)

* Multiple Code : indicate Restrictive Airspace having the same designator but subdivided or differently divided by lateral and/or vertical detail

* Sequence Number : sort order of each airspace, no duplicate sequences per airspace are possible

* Boundary Via : defines the path of the boundary from the position identified in the record to the next defined position (see appendix 3.25)

* Flightlevel : defines the airway structure (see appendix 3.13)

* Latitude : latitude in degrees decimal floating point (N positive, S negative)

* Longitude : longitude in degrees decimal floating point (E positive, W negative)

* Arc Origin Latitude : arc origin latitude in degrees decimal floating point (N positive, S negative)

* Arc Origin Longitude : arc origin longitude in degrees decimal floating point (E positive, W negative)

* Arc Distance : define the distance in nautical miles from the "Arc Origin" position

* Arc Bearing : contains the true bearing from the "Arc Origin" position to the beginning of the arc

* FIR Upper Limit : contain the lower and upper limits of the FIR

* UIR Lower Limit : contain the lower and lower limits of the UIR

* UIR Upper Limit : contain the lower and upper limits of the UIR

    23. **Gate**

SQL Format:

CREATE TABLE [tbl_gate] (

	[area_code] TEXT(3), 

	[airport_identifier] TEXT(4),

	[icao_code] TEXT(2),

	[gate_identifier] TEXT(5),

	[gate_latitude] REAL(9),

	[gate_longitude] REAL(10),

	[name] TEXT(25)

)

ASCII Format:

Area Code|Airport Identifier|ICAO Code|Gate Identifier|Gate Latitude|Gate Longitude|Gate Name


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|Airport ICAO Identifier|alphanumeric|4|5.6|
|ICAO Code|alphanumeric|2|5.14|
|Gate Identifier|alphanumeric|5|5.56|
|Gate Latitude|numeric|9|5.36|
|Gate Longitude|numeric|10|5.37|
|Name|alphanumeric|5|5.60|


Example:

* EUR|LOWW|LO|57| 48.11837222| 16.56030833|57

Description:

* Area Code : geographical area of the gate

* Airport Identifier : four character ICAO location identifier

* ICAO Code : location indicator of the gate

* Gate Identifier : Airport Gate identifier

* Gate Latitude : latitude in degrees decimal floating point (N positive, S negative)

* Gate Longitude : longitude in degrees decimal floating point (E positive, W negative)

* Name : name of the gate

    24. **GLS**

SQL Format:

CREATE TABLE [tbl_gls] (

	[area_code] TEXT(3), 

	[airport_identifier] TEXT(4),

	[icao_code] TEXT(2),

	[gls_ref_path_identifier] TEXT(4),

	[gls_category] TEXT(1),

	[gls_channel] INT(5),

	[runway_identifier] TEXT(5),

	[gls_approach_bearing] REAL(5),

	[station_latitude] REAL(9),

	[station_longitude] REAL(10),

	[gls_station_ident] TEXT(4),

	[gls_approach_slope] REAL(4),

	[magnetic_variation] REAL(6),

	[station_elevation] INT(5),

	[station_type] TEXT(3)

)

ASCII Format:

Area Code|Airport Identifier|ICAO Code|GLS Ref Path Identifier|GLS Category|GLS Channel|Runway Identifier|GLS Approach Bearing|Station Latitude|Station Longitude|GLS Station Ident|GLS Approach Slope|Magnetic Variation|Station Elevation|Station Type


|Field|Format|max.length|ARINC Ref|
|---|---|---|---|
|Area Code|alphanumeric|3|5.3|
|Airport ICAO Identifier|alphanumeric|4|5.6|
|ICAO Code|alphanumeric|2|5.14|
|GLS Ref Path Identifier|alphanumeric|4|5.44|
|GLS Category|alphanumeric|1|5.80|
|GLS Channel|numeric|5|5.244|
|Runway Identifier|alphanumeric|5|5.46|
|GLS Approach Bearing|numeric|5|5.47|
|Station Latitude|numeric|9|5.36|
|Station Longitude|numeric|10|5.37|
|GLS Station Ident|alphanumeric|4|5.243|
|GLS Approach Slope|numeric|4|5.52|
|Magnetic Variation|numeric|6|5.39|
|Station Elevation|numeric|5|5.74|
|Station Type|alphanumeric|3|5.247|


Example:

* EUR|EDDF|ED|G07A|1|21159|RW07L|67.0|50.02711111|8.52151667|EDDF|3.0|2.0|376|L

Description:

* Area Code : geographical area of the gate

* Airport Identifier : four character ICAO location identifier

* ICAO Code : location indicator of the gate

* GLS Ref Path Identifier : Identification code of the GLS Reference Path

* GLS Category : ILS/MLS/GLS performance categories (see appendix 3.17)

* GLS Channel : identifies the channel that will be decoded to identify the augmentation system used. 20001 – 39999 for GBAS, 40000 – 99999 for SBAS

* Runway Identifier : runway identifier

* GLS Approach Bearing : magnetic bearing of the GLS course

* Station Latitude : latitude in degrees decimal floating point (N positive, S negative)

* Station Longitude : longitude in degrees decimal floating point (E positive, W negative)

* GLS Station Ident : identification code for retrieval of such a transmitter (not a transmitted identifier)

* GLS Approach Slope : glide slope angle of an GLS approach in degrees

* Magnetic Variation : specifies the angular difference between true north and magnetic north at the location

* Station Elevation : elevation of GLS ground station in feet

* Station Type : identifies the type of the different ground station (see appendix 3.31)

3. **Appendixes**

    25. **Navaid Class**


||1|2|3|4|5|
|---|---|---|---|---|---|
|Facility|NavaidType 1|NavaidType 2||||
|VOR|V|||||
|DME||D||||
|TACAN||T||||
|MIL TACAN||M||||
|ILS/DME||I||||
|ILS/TACAN||I||||
|MLS/DME N||N||||
|MLS/DME P||P||||
||||Range Power|||
|Terminal|||T|||
|Low Altitude|||L|||
|High Altitude|||H|||
|Undefined|||U|||
|ILS/TACAN|||C|||
|||||Additional Information||
|Bias ILS/DME or ILS/TACAN||||D||
|Automatic Transcribed Weather Broadcast||||A||
|Scheduled Weather Broadcast||||B||
|No voice on Frequency||||W||
|Voice on Frequency||||blank||
||||||Collocation|
|Collocated Navaid|||||blank|
|Non-Collocated Navaid|||||N|


    26. **Waypoint Description Code**


||1|2|3|4|
|---|---|---|---|---|
|Airport as Waypoint|A||||
|Essential Waypoint|E||||
|Off Airway Waypoint|F||||
|Runway as Waypoint|G||||
|Heliport as Waypoint|H||||
|NDB navaid as Waypoint|N||||
|Phantom Waypoint|P||||
|Non-Essential Waypoint|R||||
|Transition Essential Waypoint|T||||
|VHF Navaid as Waypoint|V||||
|end of SID/STAR/IAP route Type||B|||
|end of enroute Airway or terminal procedure||E|||
|uncharted airway intersection||U|||
|Fly-Over Waypoint||Y|||
|unnamed Stepdown Fix After final Approach Fix|||A||
|unnamed Stepdown Fix before Final Approach Fix|||B||
|ATC Compulsory Waypoint|||C||
|Oceanic Gateway Waypoint|||G||
|First leg of Missed Approach Procedure|||M||
|Path Point Fix|||P||
|Named Stepdown Fix|||S||
|Initial Approach Fix||||A|
|Intermediate Approach Fix||||B|
|Initial Approach Fix with Holding||||C|
|Initial Approach with Final Approach Course Fix||||D|
|Final End Point Fix||||E|
|Published Final Approach Fix or Database Final Approach Fix||||F|
|Holding Fix||||H|
|Final Approach Course Fix||||I|
|Published Missed Approach Point Fix||||M|


    27. **Waypoint Type for Enroute Waypoints (EA)**


|Waypoint Type|1|2|3|
|---|---|---|---|
|Combined named intersection and RNAV|C|||
|unnamed, charted intersection|I|||
|NDB navaid as waypoint|N|||
|named intersection|R|||
|uncharted Airway intersection|U|||
|VFR Waypoint|V|||
|RNAV Waypoint|W|||
|Final Approach Fix||A||
|Initial and Final Approach Fix||B||
|Final Approach Course Fix||C||
|Intermediate Approach Fix||D||
|Off-Route intersection in the FAA National Ref System||E||
|Off-Route Intersection||F||
|Initial Approach Fix||I||
|Final Approach Course Fix at Initial Approach Fix||K||
|Final Approach Course Fix at Intermediate Approach Fix||L||
|Missed Approach Fix||M||
|Initial Approach Fix and Missed Approach Fix||N||
|Oceanic Entry/Exit Waypoint||O||
|Pitch and Catch Point in the FAA High Altitude Redesign||P||
|AACAA and SUA Waypoints in the FAA High Altitude Redesign||S||
|FIR/UIR or Controlled Airspace Intersection||U||
|Latitude/Longitude Intersection Full Degree of Latitude||V||
|Latitude/Longitude Intersection, Half Degree Latitude||W||
|SID|||D|
|STAR|||E|
|Approach|||F|
|Multiple|||Z|


    28. **Waypoint Type for Terminal Waypoints (PC)**


|Waypoint Type|1|2|3|
|---|---|---|---|
|ARC Center Fix Waypoint|A|||
|Combined Named Intersection and RNAV Waypoint|C|||
|Unnamed, Charted Intersection|I|||
|Middle Marker as Waypoint|M|||
|Terminal NDB Navaid as Waypoint|N|||
|Outer Marker as Waypoint|O|||
|Named Intersection|R|||
|VFR Waypoint|V|||
|RNAV Waypoint|W|||
|Final Approach Fix||A||
|Initial Approach Fix and Final Approach Fix||B||
|Final Approach Course Fix||C||
|Intermediate Approach Fix||D||
|Initial Approach Fix||I||
|Final Approach Course Fix at Initial Approach Fix||K||
|Final Approach Course Fix at Intermediate Approach Fix||L||
|Missed Approach Fix ||M||
|Initial Approach Fix and Missed Approach Fix||N||
|Unnamed Stepdown Fix||P||
|Named Stepdown Fix||S||
|FIR/UIR or Controlled Airspace Intersection||U||
|SID|||D|
|STAR|||E|
|Approach|||F|
|Multiple|||Z|


    29. **Cruise Table Identifier**


|Field|Description|
|---|---|
|AA|ICAO standard cruise table|
|AO|Exception to ICAO cruise table|
|BB-ZZ|Modified cruise table|
|BO-ZO|Exception to modified cruise table|


    30. **FIR/UIR Indicator**


|Field|Type|
|---|---|
|F|FIR|
|U|UIR|
|B|Combined FIR/UIR|


    31. **Waypoint Usage**


|Usage|1|2|
|---|---|---|
|High and Low Altitude||B|
|High Altitude only||H|
|Low Altitude only||L|
|Terminal Use Only||blank|
|RNAV|R||


    32. **Turn Direction**


|Turn|Description|
|---|---|
|L|Left turn|
|R|Right turn|


    33. **Route Type for Enroute Airways**


|Airway Type|Field Content|
|---|---|
|Control|C|
|Direct Route|D|
|Helicopter Route|H|
|Offical Designated Airways expect RNAV Airways|O|
|RNAV Airways|R|
|Undesignated ATS Route|S|


    34. **Route Type for SIDs (PD)**


|SID Route Type|Field Content|
|---|---|
|Engine Out SID|0|
|SID Runway Transition|1|
|SID or SID Common Route|2|
|SID Enroute Transition|3|
|RNAV SID Runway Transition|4|
|RNAV SID or SID Common Route|5|
|RNAV SID Enroute Transition|6|
|FMS SID Runway Transition|F|
|FMS SID or SID Common Route|M|
|FMS SID Enroute Transition|S|
|Vector SID Runway Transition|T|
|Vector SID Enroute Transition|V|


    35. **Route Type for STARs (PE)**


|STAR Route Type|Field Content|
|---|---|
|STAR Enroute Transition|1|
|STAR or STAR Common Route|2|
|STAR Runway Transition|3|
|RNAV STAR Enroute Transition|4|
|RNAV STAR or STAR Common Route|5|
|RNAV STAR Runway Transition|6|
|Profile Descent Enroute Transition|7|
|Profile Descent Common Route|8|
|Profile Descent Runway Transition|9|
|FMS STAR Enroute Transition|F|
|FMS STAR or STAR Common Route|M|
|FMS STAR Runway Transition|S|


    36. **Route Type of IAPs (PF)**


|IAP Route Type|Field Content|
|---|---|
|Approach Transition|A|
|Localizer/Back course Approach|B|
|VORDME Approach|D|
|FMS Approach|F|
|IGS Approach|G|
|ILS Approach|I|
|GLS Approach|J|
|LOC only Approach|L|
|MLS Approach|M|
|NDB Approach|N|
|GPS Approach|P|
|NDB DME Approach|Q|
|RNAV Approach|R|
|VOR Approach using VORDME/VORTAC|S|
|TACAN Approach|T|
|SDF Approach|U|
|VOR Approach|V|
|MLS Type A Approach|W|
|LDA Approach|X|
|MLS Type B and C Approach|Y|
|Missed Approach|Z|


    37. **Level**


|Level|Description|
|---|---|
|B|All Altitudes|
|H|High Level Airways|
|L|Low Level Airways|


    38. **Directional Restriction**


|Direction|Description|
|---|---|
|F|One way in direction route is coded (Forward)|
|B|One way in opposite direction route is coded (backwards)|
|blank|no restrictions on direction|


    39. **IFR Capability**


|Direction|Description|
|---|---|
|Y|Instrument Approach Procedure published|
|N|no Instrument Approach Procedure published|


    40. **Longest Runway Surface**


|Code|Description|
|---|---|
|H|Hard Surface (asphalt or concrete)|
|S|Soft Surface (gravel, grass or soil)|
|W|Water Runway|
|U|undefined|


    41. **ILS/MLS/GLS Category**


|Definition|Category/Classification|
|---|---|
|ILS Localizer Only, no glideslope|0|
|ILS Localizer/MLS/GLS Category I|1|
|ILS Localizer/MLS/GLS Category II|2|
|ILS Localizer/MLS/GLS Category III|3|
|IGS Facility|I|
|LDA Facility with Glideslope|L|
|LDA Facility no Glideslope|A|
|SDF Facility with Glideslope|S|
|SDF Facility no Glideslope|F|


    42. **Marker Type**


|Field|Type of Facility|
|---|---|
|-IM|Inner Marker|
|-MM|Middle Marker|
|-OM|Outer Marker|
|-BM|Back Marker|
|L--|Locator at Marker|


- blank

    43. **Reporting Units Speed**


|Field|Reporting Units|
|---|---|
|0|not specified|
|1|TAS in Knots|
|2|TAS in Mach|
|3|TAS in Kilometer/hour|


    44. **Reporting Units Altitude**


|Field|Reporting Units|
|---|---|
|0|not specified|
|1|Altitude in Flightlevel|
|2|Altitude in Meters|
|3|Altitude in Feet|


    45. **Path and Termination**


|Path & Termination|Description|
|---|---|
|IF|Initial Fix|
|TF|Track to a Fix|
|CF|Course to a Fix|
|DF|Direct to a Fix|
|FA|Fix to an Altitude|
|FC|Track from a Fix for a Distance|
|FD|Track from a Fix to a DME Distance|
|FM|From a Fix to a Manual termination|
|CA|Course to an Altitude|
|CD|Course to a DME Distance|
|CI|Course to an Intercept|
|CR|Course to a Radial termination|
|RF|Constant Radius Arc|
|AF|Arc to Fix|
|VA|Heading to an Altitude|
|VD|Heading to a DME Distance termination|
|VI|Heading to an Intercept|
|VM|Heading to a Manual termination|
|VR|Heading to a Radial termination|
|PI|045/180 Procedure turn|
|HA, HF, HM|Holding in lieu of Procedure Turn|


    46. **Speed Limit Description**


|Speed Limit |Description|
|---|---|
|@ or blank|Mandatory Speed, cross fix at speed specified in the Speed Limit field|
|+|Minimum Speed, cross fix at or above speed specified in Speed Limit field|
|-|Maximum Speed, cross fix at or below speed specified in Speed Limit field|


    47. **Airspace Type**


|Field|Description|
|---|---|
|A|Class C Airspace (USA)|
|C|Control Area, ICAO Designation (CTA)|
|K|Control Area, ICAO Designation (CTA)|
|M|Terminal Control Area, ICAO Designation (TMA or TCA)|
|Q|Military Control Zone (MCTR)|
|R|Radar Zone or Radar Area (USA)|
|T|Class B Airspace (USA)|
|W|Terminal Control Area (TCA)|
|X|Terminal Area (TMA)|
|Y|Terminal Radar Service Area (TRSA)|
|Z|Class D Airspace, ICAO Designation (CTR)|


    48. **Altitude Description**


|Field|Description|
|---|---|
|+|at or above altitude specified in Altitude1 field|
|-|at or below altitude specified in Altitude1 field|
|@ or blank|at altitude specified in Altitude1 field|
|B|at or above to at or below altitudes in Altitude1 field and Altitude2 field|
|C|at or above altitude specified in Altitude2 field|
|G|Glide Slope altitude (MSL) specified in Altitude2 field and at altitude specified in Altitude1 field|
|H|Glide Slope altitude (MSL) specified in Altitude2 field and at or above altitude specified in Altitude1 field|
|I|Glide Slope Intercept Altitude specified in Altitude2 field and at altitude specified in Altitude1 field|
|J|Glide Slope Intercept Altitude specified in Altitude2 field and at or above altitude specified in Altitude1 field|
|V|at altitude on the coded vertical angle in the Altitude2 field and at or above altitude specified in Altitude1 field|
|X|at altitude on the coded vertical angle in Altitude2 field and at altitude specified in Altitude1 field|
|Y|at altitude on the coded vertical angle in Altitude2 field and at or below altitude specified in the Altitude1 field|


    49. **Boundary Via**


|Field|Description|
|---|---|
|C-|Circle|
|G-|Great Circle|
|H-|Rhumb Line|
|L-|Counter Clockwise ARC|
|R-|Clockwise ARC|
|-E|End of description, return to origin point|


- blank

    50. **Restrictive Airspace Type**


|Field|Type|
|---|---|
|A|Alert|
|C|Caution|
|D|Danger|
|M|Military Operations Area|
|P|Prohabited|
|R|Restricted|
|T|Training|
|W|Warning|
|U|Unknown|


    51. **Restriction Type**


|Field|Description|
|---|---|
|AE|Altitude exclusion|
|TC|Cruising Table Replacement|
|SC|Seasonal Restriction|
|NR|Note Restriction|


    52. **Units of Altitude**


|Field|Description|
|---|---|
|F|Restriction Altitudes are expressed in hundreds of feet|
|K|Restriction Altitudes are expressed in metric Flightlevel|
|L|Restriction Altitudes are expressed in feet Flightlevel|
|M|Restriction Altitudes are expressed in tens of meters|


    53. **Block Indicator**


|Field|Description|
|---|---|
|B|indicates an altitude block|
|I|indicates an individual altitudes block|


    54. **Frequency Units** 


|Field|Description|
|---|---|
|H|High Frequency (3.000 kHz – 30.000 kHz)|
|V|Very High Frequency (30.000 kHz – 200 MHz)|
|U|Ultra High Frequency (200 MHz – 3.000 MHz)|
|C|Communication Channel for 8.33 kHz spacing|


    55. **Station Type**


|Field|Description|
|---|---|
|L--|LAAS/GLS ground station|
|C--|SCAT-1 station|


- blank

    56. **Communication Type** 


|||Communication|||
|---|---|---|---|---|
|Field|Description|Airport|Enroute|Both|
|ACC|Area Control Center|||X|
|ACP|Airlift Command Post|X|||
|AIR|Air to Air|X|||
|APP|Approach Control|X|||
|ARR|Arrival Control|X|||
|ASO|Automatic Surface Oserving System (ASOS)|X|||
|ATI|Automatic Terminal Information Services (ATIS)|X|||
|AWI |Airport Weather Information Broadcast (AWIB)|X|||
|AWO|Automatic Weather Observing Service (AWOS)|||X|
|AWS|Aerodrome Weather Information Service (AWIS)|X|||
|CLD|Clearance Delivery|X|||
|CPT|Clearance, Pre-Taxi|X|||
|CTA|Control Area (Terminal)|X|||
|CTL|Control|||X|
|DEP|Departure Control|X|||
|DIR|Director (Approach Control Radar)|X|||
|EFS|Enroute Flight Advisory Service (EFAS)||X||
|EMR|Emergency|||X|
|FSS|Flight Service Station|||X|
|GCO|Ground Comm Outlet|X|||
|GND|Ground Control|X|||
|GET |Gate Control|X|||
|HEL|Helicopter Frequency|X|||
|INF|Information|||X|
|MIL|Military Frequency|||X|
|MUL|Multicom|||X|
|OPS|Operations|X|||
|PAL|Pilot Activated Lighting|X|||
|RDO|Radio|||X|
|RDR|Radar|||X|
|RFS|Remote Flight Service Station (RFSS)|||X|
|RMP|Ramp/Taxi Control|X|||
|RSA|Airport Radar Service Area (ARSA)|X|||
|TCA|Terminal Control Area (TCA)|X|||
|TMA|Terminal Control Area (TMA)|X|||
|TML|Terminal|X|||
|TRS|Terminal Radar Service Area (TRSA)|X|||
|TWE|Transcriber Weather Broadcast (TWEB)||X||
|TWR|Tower, Air Traffic Control|X|||
|UAC|Upper Area Control||X||
|UNI|Unicom|X|||
|VOL|Volmet||X||


    57. **Service Indicator**


|Field|Description|
|---|---|
|A--|Airport Advisory Service (AAS)|
|C--|Community Aerodrome Radio Station (CARS)|
|D--|Departure Service|
|F--|Flight Information Service (FIS)|
|I--|Initial Contact (IC)|
|L--|Arrival Service|
|P--|Pre-Departure Clearance (Data Link Service)|
|S--|Aerodrof Flight Information Service (AFIS)|
|T|Terminal Area Control|
|-A-|Aerodrome Traffic Frequency (ATF)|
|-C-|Common Traffic Advisory Frequency (CTAF)|
|-M-|Mandatory Frequency (MF)|
|-R-|Air/Air|
|-S-|Secondary Frequency|
|--A|Air/Ground|
|--D|VHF Direction Finding Service (VDF)|
|--G|Remote Communications Air to Ground (RCAG)|
|--L|Language other than English|
|--M|Military Use Frequency|
|--P|Pilot Controlled Light (PCL)|
|--R|Remote Communications Outlet (RCO)|


- blank

    58. **FIR/UIR Address**


|Field|Description|
|---|---|
|ZQZX|related to an IFR Flight|
|ZFZX|related to an VFR Flight|
|ZOZX|related to an Oceanic FIR/UIR|
|ZRZX|related to all other FIR/UIR|


    59. **Time Codes**


|Field|Description|
|---|---|
|C|active continuously, including holidays|
|H|active continuously, excluding holidays|
|N|active none continuously, refer to continuation records|
|(blank)|active times announced by Notams|


    60. **Time Distance**


|Field|Description|
|---|---|
|T|Time in minute(s)|
|D|Distance in nautical miles|


