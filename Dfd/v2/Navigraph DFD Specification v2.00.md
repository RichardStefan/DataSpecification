---
title: DFD Data Format Specification
description: The full specification for the DFD (Digital Flight Data) format, inspired by the ARINC 424 specifications.
type: Reference
order: 2
---

<Badge>Version 2.00</Badge>

## Revision History

| Version | Date       | Changes         |
|---------|------------|-----------------|
| 2.00    | 17/10/2024 | initial release |


# **Introduction**

This document will define the complete dataset specifications of the Digital Flight Data (DFD) format. It is heavily inspired by the ARINC424 specifications, though tailored to add-ons\' use. Whenever possible, I use the same naming conventions as the ARINC specification to avoid confusion. I will also clearly indicate from which sections and record columns the data must be extracted.

We used the standard ARINC424-18 specification, but it should be compatible with 424-19/20. The dataset can be provided in three different formats:

1.  ASCII text-file
2.  SQLite format
3.  in individual file formats (must be requested)

The DFD contains the primary records of the following record types (in parenthesis, the corresponding ARINC 424 section and subsection codes):

- [Airports (PA)](#airports)
- [Enroute Airways (ER)](#enroute-airways)
- [Enroute NDB Navaids (DB)](#enroute-ndb-navaids)
- [Enroute Waypoints (EA)](#enroute-waypoints)
- [Holdings (EP)](#holdings)
- [IAP - Instrument Arrival Procedures (PF)](#terminal-procedures-sidstar)
- [Localizer Marker (PM)](#localizer-marker)
- [Localizer/Glideslopes (PI)](#localizersglideslopes)
- [Runways (PG)](#runways)
- [SID -- Standard Instrument Departure (PD)](#terminal-procedures-sidstar)
- [STAR -- Standard Terminal Arrival Route (PE)](#terminal-procedures-sidstar)
- [Terminal NDB Navaids (PN)](#terminal-ndb-navaids)
- [Terminal Waypoints (PC)](#terminal-waypoints)
- [VHF Navaids (D)](#vhf-navaids)
- [Airport Communication (PV)](#airport-communication)
- [Airport MSA (PS)](#airport-msa)
- [Controlled Airspace (UC)](#controlled-airspace)
- [Cruising Tables (TC)](#cruising-tables)
- [Enroute Airway Restriction (EU)](#enroute-airways-restriction)
- [Enroute Communication (EV)](#enroute-communication)
- [FIR/UIR (UF)](#firuir)
- [Gate (PB)](#gate)
- [GLS (PT)](#gls)
- [Grid Mora (AS)](#grid-mora)
- [Restrictive Airspace (UR)](#restrictive-airspace)
- [Path Point (PP)](#path-point)

## Specification SQLite format

Filename: `ng_jeppesen_fwdfd_xxxx.3sdb` (xxxx is the cycle-number)

## Specification ASCII text-file format

All fields in the records are separated with a \| character (vertical bar or ASCII 124). All blanks will be trimmed at the end of each field (excluding special marked fields -- footnote)

## Header / Metadata {#header-metadata}

SQLite Table:
`tbl_hdr_header`

ASCII Format:
`creator\|cycle\|data_provider\|dataset_version\|dataset\|effective_fromto\|parsed_at\|revision`

| *Field*          | *Format*     | *max. length* | *ARINC Ref* |
|------------------|--------------|---------------|-------------|
| creator          | alphanumeric | 16            |             |
| cycle            | alphanumeric | 4             |             |
| data_provider    | alphanumeric | 16            |             |
| dataset_version  | alphanumeric | 16            |             |
| dataset          | alphanumeric | 20            |             |
| effective_fromto | alphanumeric | 10            |             |
| parsed_at        | alphanumeric | 22            |             |
| revision         | alphanumeric | 3             |             |

Description:

- creator: company name (ex. Navigraph)
- cycle: valid AIRAC cycle (ex. 2410)
- data_provider: data provider (ex. Jeppesen)
- dataset_version: dataset specification version (Format: 2.0.xx.xxxx)
- dataset: name of the dataset (Format: NG_xxx...)
- effective_fromto: the date when the current AIRAC cycle starts/ends (Format DDMMDDMMYY-- DD is the day, MM is the month, YY is the year) (ex. 0310301024)
- parsed_at: parsing/creation date of this AIRAC cycle (Format YYYY-MM-DD -- HH:MM:SS in UTC)
- revision: revision of this AIRAC cycle (Format: xxx)

## VHF Navaids

SQLite Table:
`tbl_d_vhfnavaids`

ASCII Format:
`airport_identifier\|area_code\|continent\|country\|datum_code\|dme_elevation\|dme_ident\|dme_latitude\|dme_longitude\|icao_code\|ilsdme_bias\|magnetic_variation\|navaid_class\|navaid_frequency\|navaid_identifier\|navaid_latitude\|navaid_longitude\|navaid_name\|range\|station_declination`

| *Field*             | *Format*     | *max.length* | *ARINC Ref* |
|---------------------|--------------|--------------|-------------|
| airport_identifier  | alphanumeric | 4            | 5.6         |
| area_code           | alphanumeric | 3            | 5.3         |
| continent           | alphanumeric | 40           | \-          |
| country             | alphanumeric | 40           | \-          |
| datum_code          | alphanumeric | 3            | 5.197       |
| dme_elevation       | numeric      | \-           | 5.40        |
| dme_ident           | alphanumeric | 4            | 5.38        |
| dme_latitude        | numeric      | \-           | 5.36        |
| dme_longitude       | numeric      | \-           | 5.37        |
| icao_code           | alphanumeric | 4            | 5.14        |
| ilsdme_bias         | numeric      | \-           | 5.90        |
| magnetic_variation  | numeric      | \-           | 5.39        |
| navaid_class        | alphanumeric | 5            | 5.35        |
| navaid_frequency    | numeric      | \-           | 5.34        |
| navaid_identifier   | alphanumeric | 4            | 5.33        |
| navaid_latitude     | numeric      | \-           | 5.36        |
| navaid_longitude    | numeric      | \-           | 5.37        |
| navaid_name         | alphanumeric | 30           | 5.71        |
| range               | numeric      | \-           | \-          |
| station_declination | numeric      | \-           | 5.66        |

Description:

- airport_identifier: four-character ICAO location identifier
- area_code: geographical area of the navaid
- continent: the continent in which the navaid is located
- country: the country in which the navaid is located
- datum_code: 3-letter code of the "local horizontal reference datum"
- dme_elevation: DME elevation in feet AMSL
- dme_ident: identification of a DME facility, a TACAN facility or the DME (or TACAN) component of a VORDME or VORTAC facility
- dme_latitude: DME latitude in degrees decimal floating point (N positive, S negative)
- dme_longitude: DME longitude in degrees decimal floating point (E positive, W negative)
- icao_code: 2-letter location indicator of the navaid
- ilsdme_bias: specify the DME offset
- magnetic_variation: magnetic north at the location
- navaid_class: navaid type, range/power, additional information & collocation (see appendix 3.1)
- navaid_frequency: navaid frequency in kHz
- navaid_identifier: navaid identifier
- navaid_latitude: navaid latitude in degrees decimal floating point (N positive, S negative)
- navaid_longitude: navaid longitude in degrees decimal floating point (E positive, W negative)
- navaid_name: navaid name
- range: distance in NM at which the signal can be received
- station_declination: the angular difference between true north and the zero degrees radial of the navaid in degrees

## Enroute NDB Navaids

SQLite Table:
`tbl_db_enroute_ndbnavaids`

ASCII Format:
`area_code\|continent\|country\|datum_code\|icao_code\|magnetic_variation\|navaid_class\|navaid_frequency\|navaid_identifier\|navaid_latitude\|navaid_longitude\|navaid_name\|range`

| *Field*            | *Format*     | *max.length* | *ARINC Ref* |
|--------------------|--------------|--------------|-------------|
| area_code          | alphanumeric | 3            | 5.3         |
| continent          | alphanumeric | 40           | \-          |
| country            | alphanumeric | 40           | \-          |
| datum_code         | alphanumeric | 3            | 5.197       |
| icao_code          | alphanumeric | 4            | 5.14        |
| magnetic_variation | numeric      | \-           | 5.39        |
| navaid_class       | alphanumeric | 5            | 5.35        |
| navaid_frequency   | numeric      | \-           | 5.34        |
| navaid_identifier  | alphanumeric | 4            | 5.33        |
| navaid_latitude    | numeric      | \-           | 5.36        |
| navaid_longitude   | numeric      | \-           | 5.37        |
| navaid_name        | alphanumeric | 30           | 5.71        |
| range              | numeric      | \-           | \-          |

Description:

- area_code: geographical area of the ndb
- continent: the continent in which the ndb is located
- country: the country in which the ndb is located
- datum_code: 3-letter code of the "local horizontal reference datum"
- icao_code: 2-letter location indicator of the ndb
- magnetic_variation: magnetic north at the location
- navaid_class: ndb type, range/power, additional information & collocation (see appendix 3.2)
- navaid_frequency: ndb frequency in MHz
- navaid_identifier: ndb identifier
- navaid_latitude: ndb latitude in degrees decimal floating point (N positive, S negative)
- navaid_longitude: ndb longitude in degrees decimal floating point (E positive, W negative)
- navaid_name: ndb name
- range: distance in NM at which the signal can be received

## Terminal NDB Navaids

SQLite Table:
`tbl_pn_terminal_ndbnavaids`

ASCII Format:
`airport_identifier\|area_code\|continent\|country\|datum_code\|icao_code\|magnetic_variation\|navaid_class\|navaid_frequency\|navaid_identifier\|navaid_latitude\|navaid_longitude\|navaid_name\|range`

| *Field*            | *Format*     | *max.length* | *ARINC Ref* |
|--------------------|--------------|--------------|-------------|
| airport_identifier | alphanumeric | 4            | 5.6         |
| area_code          | alphanumeric | 3            | 5.3         |
| continent          | alphanumeric | 40           | \-          |
| country            | alphanumeric | 40           | \-          |
| datum_code         | alphanumeric | 3            | 5.197       |
| icao_code          | alphanumeric | 4            | 5.14        |
| magnetic_variation | numeric      | \-           | 5.39        |
| navaid_class       | alphanumeric | 5            | 5.35        |
| navaid_frequency   | numeric      | \-           | 5.34        |
| navaid_identifier  | alphanumeric | 4            | 5.33        |
| navaid_latitude    | numeric      | \-           | 5.36        |
| navaid_longitude   | numeric      | \-           | 5.37        |
| navaid_name        | alphanumeric | 30           | 5.71        |
| range              | numeric      | \-           | \-          |

Description:

- airport_identifier: four-character ICAO location identifier
- area_code: geographical area of the ndb
- continent: the continent in which the ndb is located
- country: the country in which the ndb is located
- datum_code: 3-letter code of the "local horizontal reference datum"
- icao_code: 2-letter location indicator of the ndb
- magnetic_variation: magnetic north at the location
- navaid_class: ndb type, range/power, additional information & collocation (see appendix 3.1)
- navaid_frequency: ndb frequency in MHz
- navaid_identifier: ndb identifier
- navaid_latitude: ndb latitude in degrees decimal floating point (N positive, S negative)
- navaid_longitude: ndb longitude in degrees decimal floating point (E positive, W negative)
- navaid_name: ndb name
- range: distance in NM at which the signal can be received

## Enroute Waypoints

SQLite Table:
`tbl_ea_enroute_waypoints`

ASCII Format:
`area_code\|continent\|country\|datum_code\|icao_code\|magnetic_variation\|waypoint_identifier\|waypoint_latitude\|waypoint_longitude\|waypoint_name\|waypoint_type\|waypoint_usage`

| *Field*             | *Format*     | *max.length* | *ARINC Ref* |
|---------------------|--------------|--------------|-------------|
| area_code           | alphanumeric | 3            | 5.3         |
| continent           | alphanumeric | 40           | \-          |
| country             | alphanumeric | 40           | \-          |
| datum_code          | alphanumeric | 3            | 5.197       |
| icao_code           | alphanumeric | 4            | 5.14        |
| magnetic_variation  | numeric      | \-           | 5.39        |
| waypoint_identifier | alphanumeric | 5            | 5.13        |
| waypoint_latitude   | numeric      | \-           | 5.36        |
| waypoint_longitude  | numeric      | \-           | 5.37        |
| waypoint_name       | alphanumeric | 25           | 5.43        |
| waypoint_type       | alphanumeric | 3            | 5.42        |
| waypoint_usage      | alphanumeric | 2            | 5.82        |

Description:

- area_code: geographical area of the waypoint
- continent: continent in which the waypoint is located
- country: the country in which the waypoint is located
- datum_code: 3-letter code of the "local horizontal reference datum"
- icao_code: 2-letter location indicator of the waypoint
- magnetic_variation: magnetic north at the location
- waypoint_identifier: waypoint identifier
- waypoint_latitude: waypoint latitude in degrees decimal floating point (N positive, S negative)
- waypoint_longitude: waypoint longitude in degrees decimal floating point (E positive, W negative)
- waypoint_name: waypoint name
- waypoint_type: waypoint type (see appendix 3.4)
- waypoint_usage: waypoint usage (see appendix 3.8)

## Terminal Waypoints

SQLite Table:
`tbl_pc_terminal_waypoints`

ASCII Format:
`area_code\|continent\|country\|datum_code\|icao_code\|magnetic_variation\|region_code\|waypoint_identifier\|waypoint_latitude\|waypoint_longitude\|waypoint_name\|waypoint_type`

| *Field*             | *Format*     | *max.length* | *ARINC Ref* |
|---------------------|--------------|--------------|-------------|
| area_code           | alphanumeric | 3            | 5.3         |
| continent           | alphanumeric | 40           | \-          |
| country             | alphanumeric | 40           | \-          |
| datum_code          | alphanumeric | 3            | 5.197       |
| icao_code           | alphanumeric | 4            | 5.14        |
| magnetic_variation  | numeric      | \-           | 5.39        |
| region_code         | alphanumeric | 4            | 5.41        |
| waypoint_identifier | alphanumeric | 5            | 5.13        |
| waypoint_latitude   | numeric      | \-           | 5.36        |
| waypoint_longitude  | numeric      | \-           | 5.37        |
| waypoint_name       | alphanumeric | 25           | 5.43        |
| waypoint_type       | alphanumeric | 3            | 5.42        |

Description:

- area_code: geographical area of the terminal waypoint
- continent: continent in which the terminal waypoint is located
- country: the country in which the terminal waypoint is located
- datum_code: 3-letter code of the "local horizontal reference datum"
- icao_code: 2-letter location indicator of the terminal waypoint
- magnetic_variation: magnetic north at the location
- region_code: airport identification code for the terminal waypoint
- waypoint_identifier: terminal waypoint identifier
- waypoint_latitude: terminal waypoint latitude in degrees decimal floating point (N positive, S negative)
- waypoint_longitude: terminal waypoint longitude in degrees decimal floating point (E positive, W negative)
- waypoint_name: terminal waypoint name
- waypoint_type: terminal waypoint type (see appendix 3.5)


## Holdings

SQLite Table:
`tbl_ep_holdings`

ASCII Format:
`area_code\|duplicate_identifier\|holding_name\|holding_speed\|icao_code\|inbound_holding_course\|leg_length\|leg_time\|maximum_altitude\|minimum_altitude\|region_code\|turn_direction\|waypoint_identifier\|waypoint_latitude\|waypoint_longitude\|waypoint_ref_table`

| *Field*                | *Format*     | *max.length* | *ARINC Ref* |
|------------------------|--------------|--------------|-------------|
| area_code              | alphanumeric | 3            | 5.3         |
| duplicate_identifier   | numeric      | \-           | 5.114       |
| holding_name           | alphanumeric | 25           | 5.60        |
| holding_speed          | numeric      | \-           | 5.175       |
| icao_code              | alphanumeric | 2            | 5.14        |
| inbound_holding_course | numeric      | \-           | 5.62        |
| leg_length             | numeric      | \-           | 5.64        |
| leg_time               | numeric      | \-           | 5.65        |
| maximum_altitude       | numeric      | \-           | 5.127       |
| minimum_altitude       | numeric      | \-           | 5.30        |
| region_code            | alphanumeric | 4            | 5.41        |
| turn_direction         | alphanumeric | 1            | 5.63        |
| waypoint_identifier    | alphanumeric | 5            | 5.13        |
| waypoint_latitude      | numeric      | \-           | 5.36        |
| waypoint_longitude     | numeric      | \-           | 5.37        |
| waypoint_ref_table     | alphanumeric | 2            | \-          |

Description:

- area_code: geographical area of the waypoint
- duplicate_identifier: used for more than one holding pattern for a single navaid or waypoint
- holding_name: holding name
- holding_speed: holding speed limit in knots
- icao_code: location indicator of the waypoint
- inbound_holding_course: inbound magnetic course in degrees floating point
- leg_length: inbound leg length in nautical miles, decimal floating point
- leg_time: inbound leg time in minutes, decimal floating point
- maximum_altitude: contain altitudes in feet or flight level
- minimum_altitude: contain altitudes in feet or flight level
- region_code: static text ENRT or airport identification code
- turn_direction: holding turn direction (see appendix 3.9)
- waypoint_identifier: navaid or waypoint identifier
- waypoint_latitude: navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)
- waypoint_longitude: navaid or waypoint longitude in degrees decimal floating point (E positive, W negative)
- waypont_ref_table: reference table for more information about the specific waypoint

## Enroute Airways

SQLite Table:
`tbl_er_enroute_airways`

ASCII Format:
`area_code\|crusing_table_identifier\|direction_restriction\|flightlevel\|icao_code\|inbound_course\|inbound_distance\|maximum_altitude\|minimum_altitude1\|minimum_altitude2\|outbound_course\|route_identifier_postfix\|route_identifier\|route_type\|seqno\|waypoint_description_code\|waypoint_identifier\|waypoint_latitude\|waypoint_longitude\|waypoint_ref_table`

| *Field*                   | *Format*     | *max.length* | *ARINC Ref* |
|---------------------------|--------------|--------------|-------------|
| area_code                 | alphanumeric | 3            | 5.3         |
| crusing_table_identifier  | alphanumeric | 2            | 5.134       |
| direction_restriction     | alphanumeric | 1            | 5.115       |
| flightlevel               | alphanumeric | 1            | 5.19        |
| icao_code                 | alphanumeric | 2            | 5.14        |
| inbound_course            | numeric      | \-           | 5.28        |
| inbound_distance          | numeric      | \-           | 5.27        |
| maximum_altitude          | numeric      | \-           | 5.127       |
| minimum_altitude1         | numeric      | \-           | 5.30        |
| minimum_altitude2         | numeric      | \-           | 5.30        |
| outbound_course           | numeric      | \-           | 5.26        |
| route_identifier_postfix  | alphanumeric | 1            | 5.8         |
| route_identifier          | alphanumeric | 6            | 5.8         |
| route_type                | alphanumeric | 1            | 5.7         |
| seqno                     | numeric      | \-           | 5.12        |
| waypoint_description_code | alphanumeric | 4            | 5.17        |
| waypoint_identifier       | alphanumeric | 5            | 5.13        |
| waypoint_latitude         | numeric      | \-           | 5.36        |
| waypoint_longitude        | numeric      | \-           | 5.37        |
| waypoint_ref_table        | alphanumeric | 2            | \-          |

Description:

- area_code: geographical area of the waypoint
- crusing_table_identifier: indicate the cruising table (see appendix 3.6)
- direction_restriction: indicate the flyable direction (see appendix 3.15)
- flightlevel: defines the airway structure (see appendix 3.14)
- icao_code: location indicator of the waypoint
- inbound_course: inbound magnetic course to the waypoint identified
- inbound_distance: contain segment distances/along track distances/excursion distances/DME distances in nautical miles
- maximum_altitude: contain altitudes in feet
- minimum_altitude1: contain altitudes in feet
- minimum_altitude2: contain altitudes in feet
- outbound_course: outbound magnetic course from the waypoint identified
- route_identifier_postfix: postfix character for the route_identifier
- route_identifier: enroute route identifier
- route_type: indicated the route type (see appendix 3.103.10)
- seqno: sort order of each enroute airway, no duplicate sequences per airway are possible
- waypoint_description_code: provides information on the type of fix (see appendix 3.3)
- waypoint_identifier: navaid or waypoint identifier
- waypoint_latitude: navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)
- waypoint_longitude: navaid or waypoint longitude in degrees decimal floating point (E positive, W negative)
- waypoint_ref_table: reference table for more information about the specific waypoint

## Airports

SQLite Table:
`tbl_pa_airports`

ASCII Format:
`airport_identifier\|airport_name\|airport_ref_latitude\|airport_ref_longitude\|airport_type\|area_code\|ata_iata_code\|city\|continent\|country_3letter\|country\|elevation\|fuel\|icao_code\|ifr_capability\|longest_runway_surface_code\|magnetic_variation\|speed_limit_altitude\|speed_limit\|state_2letter\|state\|time_zone\|transition_altitude\|transition_level`

| *Field*                     | *Format*     | *max.length* | *ARINC Ref* |
|-----------------------------|--------------|--------------|-------------|
| airport_identifier          | alphanumeric | 4            | 5.6         |
| airport_name                | alphanumeric | 30           | 5.71        |
| airport_ref_latitude        | numeric      | \-           | 5.36        |
| airport_ref_longitude       | numeric      | \-           | 5.37        |
| airport_type                | alphanumeric | 1            | 5.177       |
| area_code                   | alphanumeric | 3            | 5.3         |
| ata_iata_code               | alphanumeric | 3            | 5.107       |
| city                        | alphanumeric | 24           | \-          |
| continent                   | alphanumeric | 40           | \-          |
| country_3letter             | alphanumeric | 3            | \-          |
| country                     | alphanumeric | 40           | \-          |
| elevation                   | numeric      | \-           | 5.55        |
| fuel                        | alphanumeric | 14           | JS5.5       |
| icao_code                   | alphanumeric | 2            | 5.14        |
| ifr_capability              | alphanumeric | 1            | 5.108       |
| longest_runway_surface_code | alphanumeric | 1            | 5.54        |
| magnetic_variation          | numeric      | \-           | 5.39        |
| speed_limit_altitude        | alphanumeric | 5            | 5.73        |
| speed_limit                 | numeric      | \-           | 5.72        |
| state_2letter               | alphanumeric | 2            | \-          |
| state                       | alphanumeric | 50           | \-          |
| time_zone                   | alphanumeric | 3            | 5.178       |
| transition_altitude         | numeric      | \-           | 5.53        |
| transition_level            | numeric      | \-           | 5.53        |

Description:

- airport_identifier: four character ICAO location identifier
- airport_name: airport name
- airport_ref_latitude: airport reference latitude in degrees decimal floating point (N positive, S negative)
- airport_ref_longitude: airport reference longitude in degrees decimal floating point (E positive, W negative)
- airport_type: defines the type of airport (see appendix 3.39)
- area_code: geographical area of the airport
- ata_iata_code: IATA/ATA airport designator code
- city: the city where the airport is located
- continent: the continent where the airport is located
- country_3letter: 3-letter country code of the airport
- country: the country where the airport is located
- elevation: elevation in feet above MSL
- fuel: available fuel specification (see appendix 3.40)
- icao_code: location indicator of the airport
- ifr_capability: indicates if the airport has a published IAP (see appendix 3.16)
- longest_runway_surface_code: define if there is a hard runway or not (see appendix 3.17)
- magnetic_variation: magnetic north at the location
- speed_limit_altitude: altitude below which speed limit may be imposed (feet or flight level)
- speed_limit: speed limit in knots
- state_2letter: 2-letter state code
- state: the state where the airport is located
- time_zone: time zone where the airport is located (see appendix 3.41)
- transition_altitude: transition altitude in feet
- transition_level: transition level in feet

## Runways

SQLite Table:
`tbl_pg_runways`

ASCII Format:
`airport_identifier\|area_code\|displaced_threshold_distance\|icao_code\|landing_threshold_elevation\|llz_identifier\|llz_mls_gls_category\|part_time_lights\|runway_gradient\|runway_identifier\|runway_latitude\|runway_length\|runway_lights\|runway_longitude\|runway_magnetic_bearing\|runway_true_bearing\|runway_width\|surface_code\|threshold_crossing_height\|traffic_pattern\|altitude_pattern_altitude`

| *Field*                      | *Format*     | *max.length* | *ARINC Ref* |
|------------------------------|--------------|--------------|-------------|
| airport_identifier           | alphanumeric | 4            | 5.3         |
| area_code                    | alphanumeric | 3            | 5.14        |
| displaced_threshold_distance | numeric      | \-           | 5.6         |
| icao_code                    | alphanumeric | 2            | 5.46        |
| landing_threshold_elevation  | numeric      | \-           | 5.36        |
| llz_identifier               | alphanumeric | 4            | 5.37        |
| llz_mls_gls_category         | alphanumeric | 1            | 5.212       |
| part_time_lights             | alphanumeric | 1            | 5.58        |
| runway_gradient              | numeric      | \-           | 5.94        |
| runway_identifier            | alphanumeric | 3            | 5.68        |
| runway_latitude              | numeric      | \-           | 5.69        |
| runway_length                | numeric      | \-           | 5.67        |
| runway_lights                | alphanumeric | 1            | 5.57        |
| runway_longitude             | numeric      | \-           | 5.109       |
| runway_magnetic_bearing      | numeric      | \-           | 5.44        |
| runway_true_bearing          | numeric      | \-           | 5.80        |
| runway_width                 | numeric      | \-           |             |
| surface_code                 | alphanumeric | 3            | JS5.19      |
| threshold_crossing_height    | numeric      | \-           |             |
| traffic_pattern              | alphanumeric | 1            | JS5.27      |
| traffic_pattern_altitude     | numeric      | \-           | JS5.9       |

Description:

- airport_identifier: four character ICAO location identifier
- area_code: geographical area of the runway
- displaced_threshold_distance: distance from the extremity of a runway to a threshold in feet
- icao_code: location indicator of the runway
- landing_theshold_elevation: elevation of the landing threshold in feet
- llz_identifier: ILS/MLS/GLS facility
- llz_mls_gls_category: ILS/MLS/GLS performance categories (see appendix 3.18)
- part_time_lights: part time/full time runway lights (see appendix 3.42)
- runway_gradient: overall gradient in percent (positive is upward, negative is downward)
- runway_identifier: runway identifier
- runway_latitude: runway latitude in degrees decimal floating point (N positive, S negative)
- runway_length: runway length in feet
- runway_lights: runway lights existing (see appendix 3.43)
- runway_longitude: runway longitude in degrees decimal floating point (E positive, W negative)
- runway_magnetic_bearing: magnetic bearing of the runway identifier
- runway_true_bearing: true bearing of the runway identifier
- runway_width: runway width in feet
- surface_code: runway surface code
- threshold_crossing_height: height above the landing threshold on normal glide path
- traffic_pattern: left/right traffic pattern (see appendix 3.44)
- traffic_pattern_altitude: indicate the height above the ground on a traffic pattern

## Localizers/Glideslopes

SQLite Table:
`tbl_pi_localizers_glideslopes`

ASCII Format:
`airport_identifier\|area_code\|gs_angle\|gs_elevation\|gs_latitude\|gs_longitude\|icao_code\|ils_mls_gls_category\|llz_bearing\|llz_frequency\|llz_identifier\|llz_latitude\|llz_longitude\|llz_truebearing\|llz_width\|runway_identifier\|station_declination`

| *Field*              | *Format*     | *max.length* | *ARINC Ref* |
|----------------------|--------------|--------------|-------------|
| airport_identifier   | alphanumeric | 4            | 5.3         |
| area_code            | alphanumeric | 3            | 5.14        |
| gs_angle             | numeric      | \-           | 5.52        |
| gs_elevation         | numeric      | \-           | 5.74        |
| gs_latitude          | numeric      | \-           | 5.36        |
| gs_longitude         | numeric      | \-           | 5.37        |
| icao_code            | alphanumeric | 2            | 5.14        |
| ils_mls_gls_category | alphanumeric | 1            | 5.80        |
| llz_bearing          | numeric      | \-           | 5.47        |
| llz_frequency        | numeric      | \-           | 5.45        |
| llz_identifier       | alphanumeric | 4            | 5.44        |
| llz_latitude         | numeric      | \-           | 5.36        |
| llz_longitude        | numeric      | \-           | 5.37        |
| llz_truebearing      | numeric      | \-           | 5.94        |
| llz_width            | numeric      | \-           | 5.51        |
| runway_identifier    | alphanumeric | 3            | 5.46        |
| station_declination  | numeric      | \-           | 5.66        |

Description:

- airport_identifier: four character ICAO location identifier
- area_code: geographical area of the localizer
- gs_angle: glide slope angle of an ILS facility/GLS approach in degrees
- gs_elevation: elevation of LLZ in feet
- gs_latitude: GS latitude in degrees decimal floating point (N positive, S negative)
- gs_longitude: GS longitude in degrees decimal floating point (E positive, W negative)
- icao_code: location indicator of the localizer
- ils_mls_gls_category: ILS/MLS/GLS performance categories (see appendix 3.18)
- llz_bearing: magnetic bearing of the localizer course
- llz_frequency: VHF frequency of the facility in MHz
- llz_identifier: identification code of the LLZ, MLS facility or GLS reference path
- llz_latitude: LLZ latitude in degrees decimal floating point (N positive, S negative)
- llz_longitude: LLZ longitude in degrees decimal floating point (E positive, W negative)
- llz_truebearing: true bearing of the localizer course
- llz_width: specifies the localizer course width (in degrees) of the ILS facility
- runway_identifier: runway identifier
- station_declination: angular difference between true north and the zero degree radial of the LLZ in degrees

## Localizer Marker

SQLite Table:
`tbl_pm_localizer_marker`

ASCII Format:
`airport_identifier\|area_code\|icao_code\|llz_identifier\|marker_identifier\|marker_latitude\|marker_longitude\|marker_type\|runway_identifier`

| *Field*            | *Format*     | *max.length* | *ARINC Ref* |
|--------------------|--------------|--------------|-------------|
| airport_identifier | alphanumeric | 4            | 5.3         |
| area_code          | alphanumeric | 3            | 5.14        |
| icao_code          | alphanumeric | 2            | 5.14        |
| llz_identifier     | alphanumeric | 4            | 5.44        |
| marker_identifier  | alphanumeric | 5            | 5.33        |
| marker_latitude    | numeric      | \-           | 5.36        |
| marker_longitude   | numeric      | \-           | 5.37        |
| marker_type        | alphanumeric | 3            | 5.36        |
| runway_identifier  | alphanumeric | 5            | 5.47        |

Description:

- airport_identifier: four character ICAO location identifier
- area_code: geographical area of the marker
- icao_code: location indicator of the marker
- llz_identifier: identification code of the LLZ, MLS facility or GLS reference path
- marker_identifier: marker identifier
- marker_latitude: marker latitude in degrees decimal floating point (N positive, S negative)
- marker_longitude: marker longitude in degrees decimal floating point (E positive, W negative)
- marker_type: defines the type of marker (see appendix 3.19)
- runway_identifier: runway identifier

## Terminal Procedures (SID/STAR)

SQLite Table:
`tbl_pd_sids`
`tbl_pe_stars`

ASCII Format:
`airport_identifier\|altitude_description\|altitude1\|altitude2\|arc_radius\|area_code\|authorization_required\|center_waypoint_icao_code\|center_waypoint_latitude\|center_waypoint_longitude\|center_waypoint_ref_table\|center_waypoint\|course_flag\|course\|distance_time\|path_termination\|procedure_identifier\|recommended_navaid_icao_code\|recommended_navaid_latitude\|recommended_navaid_longitude\|recommended_navaid_ref_table\|recommended_navaid\|rho\|rnp\|route_distance_holding_distance_time\|route_type\|seqno\|speed_limit_description\|speed_limit\|theta\|transition_altitude\|transition_identifier\|turn_direction\|vertical_angle\|waypoint_description_code\|waypoint_icao_code\|waypoint_identifier\|waypoint_latitude\|waypoint_longitude\|waypoint_ref_table`

| *Field*                              | *Format*     | *max.length* | *ARINC Ref* |
|--------------------------------------|--------------|--------------|-------------|
| airport_identifier                   | alphanumeric | 4            | 5.6         |
| altitude_description                 | alphanumeric | 1            | 5.29        |
| altitude1                            | numeric      | \-           | 5.30        |
| altitude2                            | numeric      | \-           | 5.30        |
| arc_radius                           | numeric      | \-           | 5.204       |
| area_code                            | alphanumeric | 3            | 5.3         |
| authorization_required               | alphanumeric | 1            | \-          |
| center_waypoint_icao_code            | alphanumeric | 2            | 5.14        |
| center_waypoint_latitude             | numeric      | \-           | 5.36        |
| center_waypoint_longitude            | numeric      | \-           | 5.37        |
| center_waypoint_ref_table            | alphanumeric | 2            | \-          |
| center_waypoint                      | alphanumeric | 5            | 5.144       |
| course_flag                          | alphanumeric | 1            | \-          |
| course                               | numeric      | \-           | 5.26        |
| distance_time                        | numeric      | \-           | 5.27        |
| path_termination                     | alphanumeric | 2            | 5.21        |
| procedure_identifier                 | alphanumeric | 6            | 5.9/10      |
| recommended_navaid_icao_code         | alphanumeric | 2            | 5.14        |
| recommended_navaid_latitude          | numeric      | \-           | 5.36        |
| recommended_navaid_longitude         | numeric      | \-           | 5.37        |
| recommended_navaid_ref_table         | alphanumeric | 2            | \-          |
| recommended_navaid                   | alphanumeric | 4            | 5.23        |
| rho                                  | numeric      | \-           | 5.25        |
| rnp                                  | numeric      | \-           | 5.211       |
| route_distance_holding_distance_time | alphanumeric | 1            | \-          |
| route_type                           | alphanumeric | 1            | 5.7         |
| seqno                                | numeric      | \-           | 5.12        |
| speed_limit_description              | alphanumeric | 1            | 5.261       |
| speed_limit                          | numeric      | \-           | 5.72        |
| theta                                | numeric      | \-           | 5.24        |
| transition_altitude                  | numeric      | \-           | 5.53        |
| transition_identifier                | alphanumeric | 5            | 5.11        |
| turn_direction                       | alphanumeric | 1            | 5.20        |
| vertical_angle                       | numeric      | \-           | 5.70        |
| waypoint_description_code            | alphanumeric | 4            | 5.17        |
| waypoint_icao_code                   | alphanumeric | 2            | 5.14        |
| waypoint_identifier                  | alphanumeric | 5            | 5.13        |
| waypoint_latitude                    | numeric      | \-           | 5.36        |
| waypoint_longitude                   | numeric      | \-           | 5.37        |
| waypoint_ref_table                   | alphanumeric | 2            | \-          |

Description:

- airport_identifier: four character ICAO location identifier
- altitude_description: designate whether a waypoint should be crossed (see appendix 3.25)
- altitude1: contain altitudes in feet or flight level
- altitude2: contain altitudes in feet or flight level
- arc_radius: used to define the radius of a precision turn
- area_code: geographical area of the procedure
- authorization_required: indicates, if this procedure needs special authorization (AR procedures)
- center_waypoint_icao_code: location indicator of the center waypoint
- center_waypoint_latitude: center fix latitude in degrees decimal floating point (N positive, S negative)
- center_waypoint_longitude: center fix longitude in degrees decimal floating point (E positive, W negative)
- center_waypoint_ref_table: reference table for more information about the specific center waypoint
- center_waypoint: represents the MSA Center, that point (Navaid or Waypoint) on which the MSA is predicated
- course_flag: magnetic or true flag (see appendix 3.45)
- course: outbound course from the waypoint identified in the record's "waypoint_identifier" field
- distance_time: indicates, if the value in the "route_distance_holding_distance_time" column references to a time value, or distance value (see appendix 3.37)
- path_termination: defines the path geometry for a single record of an terminal procedure (see appendix 3.22)
- procedure_identifier: name of the terminal procedure
- recommended_navaid_icao_code: location indicator of the navaid
- recommended_navaid_latitude: recommended navaid latitude in degrees decimal floating point (N positive, S negative)
- recommended_navaid_longitude: recommended navaid longitude in degrees decimal floating point (E positive, W negative)
- recommended_navaid_ref_table: reference table for more information about the specific navaid
- recommended_navaid: reference facility for the waypoint
- rho: defined as the geodesic distance in nautical miles to the waypoint identified in the record's "waypoint_identifier" field from the "recommended_navaid_identifier" field
- rnp: statement of the Navigation Performance necessary for operation within a defined airspace in accordance with ICAO Annex 15 and/or State published rules
- route_distance_holding_distance_time: contain segment distances/along track distances/excursion distances/DME distances
- route_type: element of the terminal procedure (see appendix 3.11, 3.12)
- seqno: sequence definition phase of the terminal procedure
- speed_limit_description: designate whether the speed limit coded at a waypoint in a terminal procedure description is a mandatory, minimum or maximum speed (see appendix 3.23)
- speed_limit: speed limit in knots
- theta: defined as the magnetic bearing to the waypoint identified in the record's "waypoint_identifier" field from the "recommended_navaid_identifier" field
- transition_altitude: transition altitude in feet
- transition_identifier: describes the type of transition to be made from the enroute environment into the terminal area and vice versa
- turn_direction: turn direction (see appendix 3.9)
- vertical_angle: defines the vertical navigation path prescribed for the procedure
- waypoint_description_code: the field provides information on the type of fix (see appendix 3.3)
- waypoint_icao_code: location indicator of the waypoint
- waypoint_identifier: navaid or waypoint identifier
- waypoint_latitude: navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)
- waypoint_longitude: navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)
- waypoint_ref_table: reference table for more information about the specific waypoint

##  Terminal Procedures (IAP) {#terminal-procedures-iap}

SQLite Table:
`tbl_pf_iaps`

ASCII Format:
`airport_identifier\|altitude_description\|altitude1\|altitude2\|arc_radius\|area_code\|authorization_required\|center_waypoint_icao_code\|center_waypoint_latitude\|center_waypoint_longitude\|center_waypoint_ref_table\|center_waypoint\|course_flag\|course\|distance_time\|gnss_fms_indication\|lnav_authorized_sbas\|lnav_level_service_name\|lnav_vnav_authorized_sbas\|lnav_vnav_level_service_name\|path_termination\|procedure_identifier\|recommended_navaid_icao_code\|recommended_navaid_latitude\|recommended_navaid_longitude\|recommended_navaid_ref_table\|recommended_navaid\|rho\|rnp\|route_distance_holding_distance_time\|route_type\|seqno\|speed_limit_description\|speed_limit\|theta\|transition_altitude\|transition_identifier\|turn_direction\|vertical_angle\|waypoint_description_code\|waypoint_icao_code\|waypoint_identifier\|waypoint_latitude\|waypoint_longitude\|waypoint_ref_table`

| *Field*                              | *Format*     | *max.length* | *ARINC Ref* |
|--------------------------------------|--------------|--------------|-------------|
| airport_identifier                   | alphanumeric | 4            | 5.6         |
| altitude_description                 | alphanumeric | 1            | 5.29        |
| altitude1                            | numeric      | \-           | 5.30        |
| altitude2                            | numeric      | \-           | 5.30        |
| arc_radius                           | numeric      | \-           | 5.204       |
| area_code                            | alphanumeric | 3            | 5.3         |
| authorization_required               | alphanumeric | 1            | \-          |
| center_waypoint_icao_code            | alphanumeric | 2            | 5.14        |
| center_waypoint_latitude             | numeric      | \-           | 5.36        |
| center_waypoint_longitude            | numeric      | \-           | 5.37        |
| center_waypoint_ref_table            | alphanumeric | 2            | \-          |
| center_waypoint                      | alphanumeric | 5            | 5.144       |
| course_flag                          | alphanumeric | 1            | \-          |
| course                               | numeric      | \-           | 5.26        |
| distance_time                        | numeric      | \-           | 5.27        |
| gnss_fms_indication                  | alphanumeric | 1            | 5.222       |
| lnav_authorized_sbas                 | alphanumeric | 1            | 5.276       |
| lnav_level_service_name              | alphanumeric | 1            | 5.275       |
| lnav_vnav_authorized_sbas            | alphanumeric | 1            | 5.276       |
| lnav_vnav_level_service_name         | alphanumeric | 1            | 5.275       |
| path_termination                     | alphanumeric | 2            | 5.21        |
| procedure_identifier                 | alphanumeric | 6            | 5.9/10      |
| recommended_navaid_icao_code         | alphanumeric | 2            | 5.14        |
| recommended_navaid_latitude          | numeric      | \-           | 5.36        |
| recommended_navaid_longitude         | numeric      | \-           | 5.37        |
| recommended_navaid_ref_table         | alphanumeric | 2            | \-          |
| recommended_navaid                   | alphanumeric | 4            | 5.23        |
| rho                                  | numeric      | \-           | 5.25        |
| rnp                                  | numeric      | \-           | 5.211       |
| route_distance_holding_distance_time | alphanumeric | 1            | \-          |
| route_type                           | alphanumeric | 1            | 5.7         |
| seqno                                | numeric      | \-           | 5.12        |
| speed_limit_description              | alphanumeric | 1            | 5.261       |
| speed_limit                          | numeric      | \-           | 5.72        |
| theta                                | numeric      | \-           | 5.24        |
| transition_altitude                  | numeric      | \-           | 5.53        |
| transition_identifier                | alphanumeric | 5            | 5.11        |
| turn_direction                       | alphanumeric | 1            | 5.20        |
| vertical_angle                       | numeric      | \-           | 5.70        |
| waypoint_description_code            | alphanumeric | 4            | 5.17        |
| waypoint_icao_code                   | alphanumeric | 2            | 5.14        |
| waypoint_identifier                  | alphanumeric | 5            | 5.13        |
| waypoint_latitude                    | numeric      | \-           | 5.36        |
| waypoint_longitude                   | numeric      | \-           | 5.37        |
| waypoint_ref_table                   | alphanumeric | 2            | \-          |

Description:

- airport_identifier: four character ICAO location identifier
- altitude_description: designate whether a waypoint should be crossed (see appendix 3.25)
- altitude1: contain altitudes in feet or flight level
- altitude2: contain altitudes in feet or flight level
- arc_radius: used to define the radius of a precision turn
- area_code: geographical area of the procedure
- authorization_required: indicates, if this procedure needs special authorization (AR procedures)
- center_waypoint_icao_code: location indicator of the center waypoint
- center_waypoint_latitude: center fix latitude in degrees decimal floating point (N positive, S negative)
- center_waypoint_longitude: center fix longitude in degrees decimal floating point (E positive, W negative)
- center_waypoint_ref_table: reference table for more information about the specific center waypoint
- center_waypoint: represents the MSA Center, that point (Navaid or Waypoint) on which the MSA is predicated
- course_flag: magnetic or true flag (see appendix 3.45)
- course: outbound course from the waypoint identified in the record's "waypoint_identifier" field
- distance_time: indicates, if the value in the "route_distance_holding_distance_time" column references to a time value, or distance value (see appendix 3.37)
- gnss_fms_indication: RNAV procedure has been authorized for GNSS based vertical navigation (see appendix 3.46)
- lnav_authorized_sbas: Level of Service authorized or not (see appendix 3.47)
- lnav_level_service_name: procedure level of service based on published procedure operating minimums information -- authorized for SBAS
- lnav_vnav_authorized_sbas: Level of Service authorized or not (see appendix 3.47)
- lnav_vnav_level_service_name: procedure level of service based on published procedure operating minimums information -- authorized for SBAS
- path_termination: defines the path geometry for a single record of an terminal procedure (see appendix 3.22)
- procedure_identifier: name of the terminal procedure
- recommended_navaid_icao_code: location indicator of the navaid
- recommended_navaid_latitude: recommended navaid latitude in degrees decimal floating point (N positive, S negative)
- recommended_navaid_longitude: recommended navaid longitude in degrees decimal floating point (E positive, W negative)
- recommended_navaid_ref_table: reference table for more information about the specific navaid
- recommended_navaid: reference facility for the waypoint
- rho: defined as the geodesic distance in nautical miles to the waypoint identified in the record's "waypoint_identifier" field from the "recommended_navaid_identifier" field
- rnp: statement of the Navigation Performance necessary for operation within a defined airspace in accordance with ICAO Annex 15 and/or State published rules
- route_distance_holding_distance_time: contain segment distances/along track distances/excursion distances/DME distances
- route_type: element of the terminal procedure (see appendix 3.13)
- seqno: sequence definition phase of the terminal procedure
- speed_limit_description: designate whether the speed limit coded at a waypoint in a terminal procedure description is a mandatory, minimum or maximum speed (see appendix 3.23)
- speed_limit: speed limit in knots
- theta: defined as the magnetic bearing to the waypoint identified in the record's "waypoint_identifier" field from the "recommended_navaid_identifier" field
- transition_altitude: transition altitude in feet
- transition_identifier: describes the type of transition to be made from the enroute environment into the terminal area and vice versa
- turn_direction: turn direction (see appendix 3.9)
- vertical_angle: defines the vertical navigation path prescribed for the procedure
- waypoint_description_code: the field provides information on the type of fix (see appendix 3.3)
- waypoint_icao_code: location indicator of the waypoint
- waypoint_identifier: navaid or waypoint identifier
- waypoint_latitude: navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)
- waypoint_longitude: navaid or waypoint latitude in degrees decimal floating point (N positive, S negative)
- waypoint_ref_table: reference table for more information about the specific waypoint

## Airport Communication

SQLite Table:
`tbl_pv_airport_communication`

ASCII Format:
`airport_identifier\|area_code\|callsign\|communication_frequency\|communication_type\|frequency_units\|guard_transmit\|icao_code\|latitude\|longitude\|narrative\|remote_facility_icao_code\|remote_facility\|sector_facility_icao_code\|sector_facility\|sectorization\|service_indicator\|time_of_operation_1\|time_of_operation_2\|time_of_operation_3\|time_of_operation_4\|time_of_operation_5\|time_of_operation_6\|time_of_operation_7`

| *Field*                   | *Format*     | *max.length* | *ARINC Ref* |
|---------------------------|--------------|--------------|-------------|
| airport_identifier        | alphanumeric | 4            | 5.6         |
| area_code                 | alphanumeric | 3            | 5.3         |
| callsign                  | alphanumeric | 25           | 5.105       |
| communication_frequency   | numeric      | \-           | 5.103       |
| communication_type        | alphanumeric | 3            | 5.101       |
| frequency_units           | alphanumeric | 1            | 5.104       |
| guard_transmit            | alphanumeric | 1            | 5.182       |
| icao_code                 | alphanumeric | 2            | 5.14        |
| latitude                  | numeric      | \-           | 5.36        |
| longitude                 | numeric      | \-           | 5.37        |
| narrative                 | alphanumeric | 60           | \-          |
| remote_facility_icao_code | alphanumeric | 2            | 5.14        |
| remote_facility           | alphanumeric | 4            | 5.200       |
| sector_facility_icao_code | alphanumeric | 2            | 5.14        |
| sector_facility           | alphanumeric | 4            | 5.185       |
| sectorization             | alphanumeric | 6            | 5.183       |
| service_indicator         | alphanumeric | 3            | 5.106       |
| time_of_operation_1       | alphanumeric | 10           | 5.195       |
| time_of_operation_2       | alphanumeric | 10           | 5.195       |
| time_of_operation_3       | alphanumeric | 10           | 5.195       |
| time_of_operation_4       | alphanumeric | 10           | 5.195       |
| time_of_operation_5       | alphanumeric | 10           | 5.195       |
| time_of_operation_6       | alphanumeric | 10           | 5.195       |
| time_of_operation_7       | alphanumeric | 10           | 5.195       |

Description:

- airport_identifier: four character ICAO location identifier
- area_code: geographical area of the facility
- callsign: name of the facility being called
- communication_frequency: specifies a frequency for the facility identified in the communicaton type field
- communication_type: specified the type of communication unit (see appendix 3.33)
- frequency_units: designate the frequency spectrum area for the frequency (see appendix 3.31)
- guard_transmit: receive voice communications or transmit voice communications (see appendix 3.48)
- icao_code: location indicator of the facility
- latitude: latitude in degrees decimal floating point (N positive, S negative)
- longitude: longitude in degrees decimal floating point (E positive, W negative)
- narrative: time of operation in plain text
- remote_facility_icao_code: location indicator of the remote facility
- remote_facility: navaid or airport with a Remote Communications Outlet (RCO) will be transmitted
- sector_facility_icao_code: location indicator of the sector facility
- sector_facility: navaid or airport upon which the information in the "sectorization" is based
- sectorization: used to define the airspace sector a communication frequency is applicable for when an airport defines sectors by bearing from the same point
- service_indicator: define the use of the frequency for the specified communication type (see appendix 3.34)
- time_of_operation_1-7: indicates the time of operation of the facility (see appendix 3.49)

## Enroute Communication

SQLite Table:
`tbl_ev_enroute_communication`

ASCII Format:
`area_code\|callsign\|communication_frequency\|communication_type\|fir_rdo_ident\|fir_uir_indicator\|frequency_units\|latitude\|longitude\|remote_facility_icao_code\|remote_facility\|remote_name\|service_indicator`

| *Field*                   | *Format*     | *max.length* | *ARINC Ref* |
|---------------------------|--------------|--------------|-------------|
| area_code                 | alphanumeric | 3            | 5.3         |
| callsign                  | alphanumeric | 30           | 5.190       |
| communication_frequency   | numeric      | \-           | 5.117       |
| communiation_type         | alphanumeric | 3            | 5.101       |
| fir_rdo_ident             | alphanumeric | 4            | 5.103       |
| fir_uir_indicator         | alphanumeric | 1            | 5.104       |
| frequency_units           | alphanumeric | 1            | 5.106       |
| latitude                  | numeric      | \-           | 5.189       |
| longitude                 | numeric      | \-           | 5.105       |
| remote_facility_icao_code | alphanumeric | 2            | 5.36        |
| remote_facility           | alphanumeric | 4            | 5.37        |
| remote_name               | alphanumeric | 25           |             |
| service_indicator         | alphanumeric | 3            |             |

Description:

- area_code: geographical area of the facility
- callsign: name of the facility being called
- communication_frequency: specifies a frequency for the facility identified in the communicaton type field
- communication_type: specified the type of communication unit (see appendix 3.33)
- fir_rdo_ident: identified the Flight Information Region or Upper Information Region
- fir_uir_indicator: ontain the identifier of a FIR, UIR or combined FIR/UIR (see appendix 3.7)
- frequency_units: designate the frequency spectrum area for the frequency (see appendix 3.31)
- latitude: latitude in degrees decimal floating point (N positive, S negative)
- longitude: longitude in degrees decimal floating point (E positive, W negative)
- remote_facility_icao_code: location indicator of the remote facility
- remote_facility: navaid or airport with a Remote Communications Outlet (RCO) will be transmitted
- remote_name: name of unmanned air/ground facility
- service_indicator: define the use of the frequency for the specified communication type (see appendix 3.34)

## Grid Mora

SQLite Table:
`tbl_as_grid_mora`

ASCII Format:
`mora01\|mora02\|mora03\|mora04\|mora05\|mora06\|mora07\|mora08\|mora09\|mora10\|mora11\|mora12\|mora13\|mora14\|mora15\|mora16\|mora17\|mora18\|mora19\|mora20\|mora21\|mora22\|mora23\|mora24\|mora25\|mora26\|mora27\|mora28\|mora29\|mora30\|quadrant_code\|starting_latitude\|starting_longitude`

| *Field*            | *Format*     | *max.length* | *ARINC Ref* |
|--------------------|--------------|--------------|-------------|
| mora01 -- mora30   | alphanumeric | 3            | 5.143       |
| quadrant_code      | alphanumeric | 1            | JS5.33      |
| Starting Latitude  | alphanumeric | 4            | 5.141       |
| Starting Longitude | alphanumeric | 4            | 5.142       |

Description:

- mora01 -- mora30: terrain and obstruction clearance within the section   outlined by latitude and longitude blocks in feet
- quadrant_code: indicates for which of the 30-minute quadrants of a one-degree grid square, defined by the "starting_latitude/starting_longitude", the grid mora record applies (see appendix 3.50)
- starting_latitude : defines the lower left corner for the first block
- starting_longitude : defines the lower left corner for the first block

## Airport MSA

SQLite Table:
`tbl_ps_airport_msa`

ASCII Format:
`airport_identifier\|area_code\|icao_code\|magnetic_true_indicator\|msa_center_icao_code\|msa_center_latitude\|msa_center_longitude\|msa_center_ref_table\|msa_center\|multiple_code\|radius_limit\|sector_altitude_1\|sector_altitude_2\|sector_altitude_3\|sector_altitude_4\|sector_altitude_5\|sector_bearing_1\|sector_bearing_2\|sector_bearing_3\|sector_bearing_4\| sector_bearing_5`

| *Field*                 | *Format*     | *max.length* | *ARINC Ref* |
|-------------------------|--------------|--------------|-------------|
| airport_identifier      | alphanumeric | 4            | 5.6         |
| area_code               | alphanumeric | 3            | 5.3         |
| icao_code               | alphanumeric | 2            | 5.14        |
| magnetic_true_indicator | alphanumeric | 1            | 5.165       |
| msa_center_icao_code    | alphanumeric | 2            | 5.14        |
| msa_center_latitude     | numeric      | \-           | 5.36        |
| msa_center_longitude    | numeric      | \-           | 5.37        |
| msa_center_ref_table    | alphanumeric | 2            | \-          |
| msa_center              | alphanumeric | 5            | 5.144       |
| multiple_code           | alphanumeric | 1            | 5.130       |
| radius_limit            | numeric      | \-           | 5.145       |
| sector_altitude_1-5     | numeric      | \-           | 5.147       |
| sector_bearing_1-5      | numeric      | \-           | 5.146       |

Description:

- airport_identifier: four character ICAO location identifier
- area_code: geographical area
- icao_code: location indicator of the airport
- magnetic_true_indicator: indicator if the course is true or magnetic
- msa_center_icao_code: location indicator of the center waypoint
- msa_center_latitude: MSA Center fix latitude in degrees decimal floating point (N positive, S negative)
- msa_center_longitude: MSA Center fix longitude in degrees decimal floating point (E positive, W negative)
- msa_center_ref_table: reference table for more information about the specific center waypoint
- msa_center: represents the MSA Center, that point (Navaid or Waypoint) on which the MSA is predicated
- multiple_code: indicate Restrictive Airspace having the same designator but subdivided or differently divided by lateral and/or vertical detail
- radius_limit: provides a 1000 foot obstacle clearance with a specific radius from the navigational facility/fix
- sector_altitude_1-5: sector altitude in feet
- sector_bearing_1-5: sector bearing in degrees

## Enroute Airways Restriction

SQLite Table:
`tbl_eu_enroute_airway_restriction`

ASCII Format:
`area_code\|block_indicator1\|block_indicator2\|block_indicator3\|block_indicator4\|block_indicator5\|block_indicator6\|block_indicator7\|end_date\|end_waypoint_icao_code\|end_waypoint_identifier\|end_waypoint_latitude\|end_waypoint_longitude\|end_waypoint_ref_table\|restriction_altitude1\|restriction_altitude2\|restriction_altitude3\|restriction_altitude4\|restriction_altitude5\|restriction_altitude6\|restriction_altitude7\|restriction_identifier\|restriction_type\|route_identifier\|start_date\|start_waypoint_icao_code\|start_waypoint_identifier\|start_waypoint_latitude\|start_waypoint_longitude\|start_waypoint_ref_table\|units_of_altitude`

| *Field*                   | *Format*     | *max.length* | *ARINC Ref* |
|---------------------------|--------------|--------------|-------------|
| area_code                 | alphanumeric | 3            | 5.3         |
| block_indicator1-7        | alphanumeric | 1            | 5.203       |
| end_date                  | alphanumeric | 7            | 5.157       |
| end_waypoint_icao_code    | alphanumeric | 2            | 5.14        |
| end_waypoint_identifier   | alphanumeric | 5            | 5.13        |
| end_waypoint_latitude     | numeric      | \-           | 5.36        |
| end_waypoint_longitude    | numeric      | \-           | 5.37        |
| end_waypoint_ref_table    | alphanumeric | 2            | \-          |
| restriction_altitude1-7   | numeric      | \-           | 5.161       |
| restriction_identifier    | numeric      | \-           | 5.154       |
| restriction_type          | alphanumeric | 2            | 5.201       |
| route_identifier          | alphanumeric | 5            | 5.8         |
| start_date                | alphanumeric | 7            | 5.157       |
| start_waypoint_icao_code  | alphanumeric | 2            | 5.14        |
| start_waypoint_identifier | alphanumeric | 5            | 5.13        |
| start_waypoint_latitude   | numeric      | \-           | 5.36        |
| start_waypoint_longitude  | numeric      | \-           | 5.37        |
| start_waypoint_ref_table  | alphanumeric | 2            | \-          |
| units_of_altitude         | alphanumeric | 1            | 5.160       |

Description:

- area_code: geographical area
- block_indicator1-7: specify that the altitudes that follow in the restriction record (see appendix 3.51)
- end_date: specific the effective date which does not corresponding with the AIRAC date
- end_waypoint: end waypoint identifier
- end_waypoint_icao_code: location indicator of the "end_waypoint"
- end_waypoint_identifier: end waypoint identifier
- end_waypoint_latitude: end waypoint latitude in degrees decimal floating point (N positive, S negative)
- end_waypoint_longitude: end waypoint longitude in degrees decimal floating point (E positive, W negative)
- end_waypoint_ref_table: reference table for more information about the specific end waypoint
- restriction_altitude1-7: specify the altitude profile for a specific restriction
- restriction_identifier: assign a unique identifier
- restriction_type: define the type of the restriction (see appendix 3.28)
- route_identifier: enroute route identifier
- start_date: specific the effective date which does not corresponding with the AIRAC date
- start_waypoint_icao_code: location indicator of the "start_waypoint"
- start_waypoint_identifier: start waypoint identifier
- start_waypoint_latitude: start waypoint latitude in degrees decimal floating point (N positive, S negative)
- start_waypoint_longitude: start waypoint longitude in degrees decimal floating point (E positive, W negative)
- start_waypoint_ref_table: reference table for more information about the specific start waypoint
- units_of_altitude: indicate the units of measurement for the restriction altitudes (see appendix 3.29)

## Controlled Airspace

SQLite Table:
`tbl_uc_controlled_airspace`

ASCII Format:
`airspace_center\|airspace_classification\|airspace_type\|arc_bearing\|arc_distance_arc_origin_latitude\|arc_origin_longitude\|area_code\|boundary_via\|controlled_airspace_name\|flightlevel\|icao_code\|latitude\|longitude\|lower_limit\|multiple_code\|seqno\|time_code\|unit_indicator_lower_limit\|unit_indicator_upper_limit\|upper_limit`

| *Field*                    | *Format*     | *max.length* | *ARINC Ref* |
|----------------------------|--------------|--------------|-------------|
| airspace_center            | alphanumeric | 5            | 5.214       |
| airspace_classification    | alphanumeric | 1            | 5.215       |
| airspace_type              | alphanumeric | 1            | 5.213       |
| arc_bearing                | numeric      | \-           | 5.120       |
| arc_distance               | numeric      | \-           | 5.119       |
| arc_origin_latitude        | numeric      | \-           | 5.36        |
| arc_origin_longitude       | numeric      | \-           | 5.37        |
| area_code                  | alphanumeric | 3            | 5.3         |
| boundary_via               | alphanumeric | 2            | 5.118       |
| controlled_airspace_name   | alphanumeric | 30           | 5.216       |
| flightlevel                | alphanumeric | 1            | 5.19        |
| icao_code                  | alphanumeric | 2            | 5.14        |
| latitude                   | numeric      | \-           | 5.36        |
| longitude                  | numeric      | \-           | 5.37        |
| lower_limit                | alphanumeric | 5            | 5.121       |
| multiple_code              | alphanumeric | 1            | 5.130       |
| seqno                      | numeric      | \-           | 5.12        |
| time_code                  | alphanumeric | 1            | 5.131       |
| unit_indicator_lower_limit | alphanumeric | 1            | 5.133       |
| unit_indicator_upper_limit | alphanumeric | 1            | 5.133       |
| upper_limit                | alphanumeric | 5            | 5.121       |

Description:

- airspace_center: define the navigation element upon which the controlled airspace being defined is predicated, but not necessarily centered
- airspace_classification: indicating the published classification of the controlled airspace, when assigned- airspace_type: indicate the type of controlled airspace (see appendix 3.24)
- arc_bearing: contains the true bearing from the "Arc Origin" position to the beginning of the arc
- arc_distance: define the distance in nautical miles from the "Arc Origin" position
- arc_origin_latitude: arc origin latitude in degrees decimal floating point (N positive, S negative)
- arc_origin_longitude: arc origin longitude in degrees decimal floating point (E positive, W negative)
- area_code: geographical area
- boundary_via: defines the path of the boundary from the position identified in the record to the next defined position (see appendix 3.26)
- controlled_airspace_name: the name of the controlled airspace when assigned
- flightlevel: defines the airway structure (see appendix 3.14)
- icao_code: location indicator of the airspace center
- latitude: latitude in degrees decimal floating point (N positive, S negative)
- longitude: longitude in degrees decimal floating point (E positive, W negative)
- lower_limit: contain the lower limits
- multiple_code: indicate Restrictive Airspace having the same designator but subdivided or differently divided by lateral and/or vertical deta
- seqno: sort order of each airspace, no duplicate sequences per airspace are possible
- time_code: Active Time (see appendix 3.36)
- unit_indicator_lower_limit: specified as "above mean sea level" (MSL) or "above ground level" (AGL)
- unit_indicator_upper_limit: specified as "above mean sea level" (MSL) or "above ground level" (AGL)
- upper_limit: contain the upper limits

## Cruising Tables

SQLite Table:
`tbl_tc_cruising_tables`

ASCII Format:
`area_code\|course_from\|course_to\|cruise_level_from1\|cruise_level_from2\|cruise_level_from3\|cruise_level_from4\|cruise_level_to1\|cruise_level_to2\|cruise_level_to3\|cruise_level_to4\|cruise_table_identifier\|mag_true\|seqno\|vertical_separation1\|vertical_separation2\|vertical_separation3\|vertical_separation4`

| *Field*                 | *Format*     | *max.length* | *ARINC Ref* |
|-------------------------|--------------|--------------|-------------|
| area_code               | alphanumeric | 3            | 5.3         |
| course_from             | numeric      | \-           | 5.135       |
| course_to               | numeric      | \-           | 5.135       |
| cruise_level_from1-4    | numeric      | \-           | 5.136       |
| cruise_level_to1-4      | numeric      | \-           | 5.136       |
| cruise_table_identifier | alphanumeric | 2            | 5.134       |
| mag_true                | alphanumeric | 1            | 5.165       |
| seqno                   | numeric      | \-           | 5.12        |
| vertical_separation1-4  | numeric      | \-           | 5.136       |

Description:

- area_code: geographical area
- course_from: indicate the lowest course for which a block of cruising levels are prescribed
- course_to: indicate the highest course for which a block of cruising levels is prescribed
- cruise_level_from1-4: indicate the lowest cruising level prescribed for use within the Course From/To fields
- cruise_level_to1-4: indicate the highest cruising level prescribed for use within the Course From/To fields
- cruise_table_identifier: indicate the cruising table (see appendix 3.6)
- mag_true: course from/to in magnetic or true degrees
- seqno: sort order of each cruise table, no duplicate sequences per cruise table are possible
- vertical_separation1-4: indicate the minimum separation prescribed to be maintained between the cruising levels

## FIR/UIR

SQLite Table:
`tbl_uf_fir_uir`

ASCII Format:
`adjacent_fir_identifier\|adjacent_uir_identifier\|arc_bearing\|arc_distance\|arc_origin_latitude\|arc_origin_longitude\|area_code\|boundry_via\|cruise_table_identifier\|fir_uir_address\|fir_uir_identifier\|fir_uir_indicator\|fir_uir_latitude\|fir_uir_longitude\|fir_uir_name\|fir_upper_limit\|reporting_units_altitude\|reporting_units_speed\|seqno\|uir_lower_limit\|uir_upper_limit`

| *Field*                  | *Format*     | *max.length* | *ARINC Ref* |
|--------------------------|--------------|--------------|-------------|
| adjacent_fir_identifier  | alphanumeric | 4            | 5.116       |
| adjacent_uir_identifier  | alphanumeric | 4            | 5.116       |
| arc_bearing              | numeric      | \-           | 5.120       |
| arc_distance             | numeric      | \-           | 5.119       |
| arc_origin_latitude      | numeric      | \-           | 5.36        |
| arc_origin_longitude     | numeric      | \-           | 5.37        |
| area_code                | alphanumeric | 3            | 5.3         |
| boundary_via             | alphanumeric | 2            | 5.118       |
| cruise_table_identifier  | alphanumeric | 2            | 5.134       |
| fir_uir_address          | alphanumeric | 4            | 5.151       |
| fir_uir_identifier       | alphanumeric | 4            | 5.116       |
| fir_uir_indicator        | alphanumeric | 1            | 5.117       |
| fir_uir_latitude         | numeric      | \-           | 5.36        |
| fir_uir_longitude        | numeric      | \-           | 5.37        |
| fir_uir_name             | alphanumeric | 25           | 5.125       |
| fir_upper_limit          | alphanumeric | 5            | 5.121       |
| reporting_units_altitude | numeric      | \-           | 5.123       |
| reporting_units_speed    | numeric      | \-           | 5.122       |
| seqno                    | numeric      | \-           | 5.12        |
| uir_lower_limit          | alphanumeric | 5            | 5.121       |
| uir_upper_limit          | alphanumeric | 5            | 5.121       |

Description:

- adjacent_fir_identifier: identifies the Flight Information Region and Upper Information Region of airspace
- adjacent_uir_identifier: identifies the Flight Information Region and Upper Information Region of airspace
- arc_bearing: contains the true bearing from the "Arc Origin" position to the beginning of the arc
- arc_distance: define the distance in nautical miles from the "Arc Origin" position
- arc_origin_latitude: arc origin latitude in degrees decimal floating point (N positive, S negative)
- arc_origin_longitude: arc origin longitude in degrees decimal floating point (E positive, W negative)
- area_code: geographical area
- boundary_via: defines the path of the boundary from the position identified in the record to the next defined position (see appendix 3.26)
- cruise_table_identifier: indicate the cruising table (see appendix 3.6)
- fir_uir_address: communication address of the FIR/UIR to supplement the FIR/UIR ident (see appendix 3.35)
- fir_uir_identifier: identifies the Flight Information Region and Upper Information Region of airspace
- fir_uir_indicator: indicate the type of controlled airspace (see appendix 3.7)
- fir_uir_latitude: FIR/UIR latitude in degrees decimal floating point (N positive, S negative)
- fir_uir_longitude: FIR/UIR longitude in degrees decimal floating point (E positive, W negative)
- fir_uir_name: the name of the controlled airspace when assigned
- fir_upper_limit: contain the FIR upper limits
- reporting_units_altitude: indicate the units of measurement concerning the altitude used in the specific FIR/UIR (see appendix 3.21)
- reporting_units_speed: indicate the units of measurement concerning True Air Speed used in the specific FIR/UIR (see appendix 3.20)
- seqno: sort order of each airspace, no duplicate sequences per airspace are possible
- uir_lower_limit: contain the UIR lower limits
- uir_upper_limit: contain the UIR upper limits

## Restrictive Airspace

SQLite Table:
`tbl_ur_restrictive_airspace`

ASCII Format:
`arc_bearing\|arc_distance\|arc_origin_latitude\|arc_origin_longitude\|area_code\|boundary_via\|flightlevel\|icao_code\|latitude\|longitude\|lower_limit\|multiple_code\|restrictive_airspace_designation\|restrictive_airspace_name\|restrictive_type\|seqno\|unit_indicator_lower_limit\|unit_indicator_upper_limit\|upper_limit`

| *Field*                          | *Format*     | *max.length* | *ARINC Ref* |
|----------------------------------|--------------|--------------|-------------|
| arc_bearing                      | numeric      | \-           | 5.120       |
| arc_distance                     | numeric      | \-           | 5.119       |
| arc_origin_latitude              | numeric      | \-           | 5.36        |
| arc_origin_longitude             | numeric      | \-           | 5.37        |
| area_code                        | alphanumeric | 3            | 5.3         |
| boundary_via                     | alphanumeric | 2            | 5.12        |
| flightlevel                      | alphanumeric | 1            | 5.19        |
| icao_code                        | alphanumeric | 2            | 5.14        |
| latitude                         | numeric      | \-           | 5.36        |
| longitude                        | numeric      | \-           | 5.37        |
| lower_limit                      | alphanumeric | 5            | 5.121       |
| multiple_code                    | alphanumeric | 1            | 5.130       |
| restrictive_airspace_designation | alphanumeric | 10           | 5.129       |
| restrictive_airspace_name        | alphanumeric | 30           | 5.126       |
| restrictive_type                 | alphanumeric | 1            | 5.128       |
| seqno                            | numeric      | \-           | 5.12        |
| unit_indicator_lower_limit       | alphanumeric | 1            | 5.133       |
| unit_indicator_upper_limit       | alphanumeric | 1            | 5.133       |
| upper_limit                      | alphanumeric | 5            | 5.121       |

Description:

- arc_bearing: contains the true bearing from the "Arc Origin" position to the beginning of the arc
- arc_distance: define the distance in nautical miles from the "Arc Origin" position
- arc_origin_latitude: arc origin latitude in degrees decimal floating point (N positive, S negative)
- arc_origin_longitude: arc origin longitude in degrees decimal floating point (E positive, W negative)
- area_code: geographical area of the airspace
- boundary_via: defines the path of the boundary from the position identified in the record to the next defined position (see appendix 3.26)
- flightlevel: defines the airway structure (see appendix 3.14)
- icao_code: location indicator of the airspace
- latitude: latitude in degrees decimal floating point (N positive, S negative)
- longitude: longitude in degrees decimal floating point (E positive, W negative)
- lower_limit: contain the lower limits
- multiple_code: indicate Restrictive Airspace having the same designator but subdivided or differently divided by lateral and/or vertical detail
- restrictive_airspace_designation: contains the number or name that uniquely identifies the restrictive airspace
- restrictive_airspace_name: name of the restrictive airspace when assigned
- restrictive_type: indicate the type of Airspace in which the flight of aircraft is prohibited or restricted (see appendix 3.28)
- seqno: sort order of each airspace, no duplicate sequences per airspace are possible
- unit_indicator_lower_limit: specified as "above mean sea level" (MSL) or "above ground level" (AGL)
- unit_indicator_upper_limit: specified as "above mean sea level" (MSL) or "above ground level" (AGL)
- upper_limit: contain the upper limits

## Gate

SQLite Table:
`tbl_pb_gates`

ASCII Format:
`airport_identifier\|area_code\|gate_identifier\|gate_latitude\|gate_longitude\|icao_code\|name`

| *Field*            | *Format*     | *max.length* | *ARINC Ref* |
|--------------------|--------------|--------------|-------------|
| airport_identifier | alphanumeric | 4            | 5.6         |
| area_code          | alphanumeric | 3            | 5.3         |
| gate_identifier    | alphanumeric | 5            | 5.56        |
| gate_latitude      | numeric      | \-           | 5.36        |
| gate_longitude     | numeric      | \-           | 5.37        |
| icao_code          | alphanumeric | 2            | 5.14        |
| name               | alphanumeric | 25           | 5.60        |

Description:

- airport_identifier: four character ICAO location identifier
- area_code: geographical area of the gate
- gate_identifier: airport gate identifier
- gate_latitude: latitude in degrees decimal floating point (N positive, S negative)
- gate_longitude: longitude in degrees decimal floating point (E positive, W negative)
- icao_code: location indicator of the gate
- name: name of the gate

## GLS

SQLite Table:
`tbl_pt_gls`

ASCII Format:
`airport_identifier\|area_code\|gls_approach_bearing\|gls_approach_slope\|gls_category\|gls_channel\|gls_ref_path_identifier\|gls_station_ident\|icao_code\|magnetic_variation\|runway_identifier\|station_elevation\|station_latitude\|station_longitude\|station_type`

| *Field*                 | *Format*     | *max.length* | *ARINC Ref* |
|-------------------------|--------------|--------------|-------------|
| airport_identifier      | alphanumeric | 4            | 5.6         |
| area_code               | alphanumeric | 3            | 5.3         |
| gls_approach_bearing    | numeric      | \-           | 5.47        |
| gls_approch_slope       | numeric      | \-           | 5.52        |
| gls_category            | alphanumeric | 1            | 5.80        |
| gls_channel             | numeric      | \-           | 5.244       |
| gls_ref_path_identifier | alphanumeric | 4            | 5.44        |
| gls_station_ident       | alphanumeric | 4            | 5.243       |
| icao_code               | alphanumeric | 2            | 5.14        |
| magnetic_variation      | numeric      | \-           | 5.39        |
| runway_identifier       | alphanumeric | 5            | 5.46        |
| station_elevation       | numeric      | \-           | 5.74        |
| station_latitude        | numeric      | \-           | 5.36        |
| station_longitude       | numeric      | \-           | 5.37        |
| station_type            | alphanumeric | 3            | 5.247       |

Description:

- airport_identifier: four character ICAO location identifier
- area_code: geographical area of the GLS ground station
- gls_approach_bearing: magnetic bearing of the GLS course
- gls_approach_slope: glide slope angle of an GLS approach in degrees
- gls_category: ILS/MLS/GLS performance categories (see appendix 3.18)
- gls_channel: identifies the channel that will be decoded to identify the augmentation system used. 20001 -- 39999 for GBAS, 40000 -- 99999 for SBAS
- gls_ref_path_identifier: Identification code of the GLS Reference Path
- gls_station_ident: identification code for retrieval of such a transmitter (not a transmitted identifier)
- icao_code: location indicator of the gate
- magnetic_variation: specifies the angular difference between true north and magnetic north at the location
- runway_identifier: runway identifier
- station_elevation: elevation of GLS ground station in feet
- station_latitude: latitude in degrees decimal floating point (N positive, S negative)
- station_longitude: longitude in degrees decimal floating point (E positive, W negative)
- station_type: identifies the type of the different ground station (see appendix 3.32)

## Path Point

SQLite Table:
`tbl_pp_pathpoint`

ASCII Format:
`airport_icao_code\|airport_identifier\|approach_performance_designator\|approach_procedure_ident\|approach_type_identifier\|area_code\|course_width_at_threshold\|flight_path_alignment_point_latitude\|flight_path_alignment_point_longitude\|fpap_ellipsoid_height\|fpap_orthometric_height\|glide_path_angle\|gnss_channel_number\|hal\|landing_threshold_point_latitude\|landing_threshold_point_longitude\|length_offset\|ltp_ellipsoid_height\|operation_type\|path_point_tch\|reference_path_data_selector\|reference_path_identifier\|route_indicator\|runway_identifier\|sbas_service_provider_identifier\|tch_units_indicator\|val`

| *Field*                           | *Format*     | *max.length* | *ARINC Ref* |
|-----------------------------------|--------------|--------------|-------------|
| airport_icao_code                 | alphanumeric | 4            | 5.14        |
| airport_identifier                | alphanumeric | 4            | 5.6         |
| approach_performance_designator   | alphanumeric | 1            | 5.258       |
| approach_procedure_ident          | alphanumeric | 6            | 5.10        |
| approach_type_identifier          | alphanumeric | 10           | 5.262       |
| area_code                         | alphanumeric | 3            | 5.3         |
| course_width_at_threshold         | numeric      | \-           | 5.228       |
| flight_path_alignment_point_lat   | numeric      | \-           | 5.267       |
| flight_path_alignment_point_lon   | numeric      | \-           | 5.268       |
| fpap_ellipsoid_height             | numeric      | \-           | 5.225       |
| fpap_orthometric_height           | numeric      | \-           | 5.227       |
| glide_path_angle                  | numeric      | \-           | 5.226       |
| gnss_channel_number               | numeric      | \-           | 5.244       |
| hal                               | numeric      | \-           | 5.263       |
| landing_threshold_point_latitude  | numeric      | \-           | 5.267       |
| landing_threshold_point_longitude | numeric      | \-           | 5.268       |
| length_offset                     | numeric      | \-           | 5.259       |
| ltp_ellipsoid_height              | numeric      | \-           | 5.225       |
| ltp_orthometric_height            | numeric      | \-           | 5.227       |
| operation_type                    | numeric      | \-           | 5.223       |
| path_point_tch                    | numeric      | \-           | 5.265       |
| reference_path_data_selector      | numeric      | \-           | 5.256       |
| reference_path_identifier         | alphanumeric | 4            | 5.257       |
| route_indicator                   | alphanumeric | 1            | 5.224       |
| runway_identifier                 | alphanumeric | 4            | 5.46        |
| sbas_service_provider_identifier  | numeric      | \-           | 5.255       |
| tch_units_indicator               | alphanumeric | 1            | 5.266       |
| val                               | numeric      | \-           | 5.264       |

Description:

- airport_icao_code: location indicator of the airport
- airport_identifier: four character ICAO location identifier
- approach_performance_designator: indicate the type or category of approach approach_procedure_ident: Identifier of the approach route
- approach_type_identifier: Identifies the approach types published on a given approach procedure
- area_code: geographical area of the point
- course_width_at_threshold: width of the lateral course at the Landing Threshold Point (LTP)
- flight_path_alignment_point_latitude: latitude of the navigation feature identified (N positive, S negative)
- flight_path_alignment_point_longitude: longitude of the navigation feature identified (E positive, W negative)
- fpap_ellipsoid_height: height of a surveyed point in reference to the WGS-84 ellipsoid
- fpap_orthometric_height: height of a surveyed point in reference to Mean Sea Level (MSL)
- glide_path_angle: angle in degrees
- gnss_channel_number: identifies the channel
- hal: horizontal alert limit is the radius of a circle in the horizontal plane
- landing_threshold_point_latitude: latitude of the navigation feature identified (N positive, S negative)
- landing_threshold_point_longitude: longitude of the navigation feature identified (E positive, W negative)
- length_offset: distance from the stop end of the runway (SER) to the FPAP
- ltp_ellipsoid_height: height of a surveyed point in reference to the WGS-84 ellipsoid
- ltp_orthometric_height: height of a surveyed point in reference to Mean Sea Level (MSL)
- operation_type: contains information on the type of final approach
- segment path_point_tch: height above the runway threshold (LTP)
- reference_path_data_selector: enables the automatic tuning of a procedure by ground based augmentation systems (GBAS) avionics
- reference_path_identifier: identifier to confirm selection of the correct approach procedure
- route_indicator: a single alpha character used to differentiate between multiple final approach segments to the same runway
- runway_identifier: runway identifier
- sbas_service_provider_identifier: associate the approach procedure to a particular satellite based approach system service provider
- tch_units_indicator: define the units (meters or feet)
- val: vertical alert limit is half the length of a segment on the vertical axis

# **Appendixes**

### Navaid Class

|                                         | 1   | 2   | 3   | 4     | 5     |
| --------------------------------------- | --- | --- | --- | ----- | ----- |
| **Facility**                            |     |     |     |       |       |
| VOR                                     | V   |     |     |       |       |
| DME                                     |     | D   |     |       |       |
| TACAN                                   |     | T   |     |       |       |
| MIL TACAN                               |     | M   |     |       |       |
| ILS/DME                                 |     | I   |     |       |       |
| ILS/TACAN                               |     | I   |     |       |       |
| MLS/DME N                               |     | N   |     |       |       |
| MLS/DME P                               |     | P   |     |       |       |
| **Range Power**                         |     |     |     |       |       |
| Terminal                                |     |     | T   |       |       |
| Low Altitude                            |     |     | L   |       |       |
| High Altitude                           |     |     | H   |       |       |
| Undefined                               |     |     | U   |       |       |
| ILS/TACAN                               |     |     | C   |       |       |
| **Additional Information**              |     |     |     |       |       |
| Bias ILS/DME or ILS/TACAN               |     |     |     | D     |       |
| Automatic Transcribed Weather Broadcast |     |     |     | A     |       |
| Scheduled Weather Broadcast             |     |     |     | B     |       |
| No voice on Frequency                   |     |     |     | W     |       |
| Voice on Frequency                      |     |     |     | blank |       |
| **Collocation**                         |     |     |     |       |       |
| Collocated Navaid                       |     |     |     |       | blank |
| Non-Collocated Navaid                   |     |     |     |       | N     |

### NDB Class

|                                         | 1   | 2   | 3     | 4     | 5   | Note        |
| --------------------------------------- | --- | --- | ----- | ----- | --- | ----------- |
| **Facility**                            |     |     |       |       |     |             |
| NDB                                     | H   |     |       |       |     |             |
| SABH                                    | S   |     |       |       |     |             |
| Marine Bacon                            | M   |     |       |       |     |             |
| Inner Marker                            |     | I   |       |       |     |             |
| Middle Marker                           |     | M   |       |       |     |             |
| Outer Marker                            |     | O   |       |       |     |             |
| Back Marker                             |     | C   |       |       |     |             |
| **Range Power**                         |     |     |       |       |     |             |
| 200 Watts or More                       |     |     | H     |       |     | (min 75 NM) |
| 50 to 1999 Watts                        |     |     | blank |       |     | (min 50 NM) |
| 25 to Less Than 50                      |     |     | M     |       |     | (min 25 NM) |
| Less Than 25 Watts                      |     |     | L     |       |     | (min 15 NM) |
| **Additional Information**              |     |     |       |       |     |             |
| Automatic Transcribed Weather Broadcast |     |     |       | A     |     |             |
| Scheduled Weather Broadcast             |     |     |       | B     |     |             |
| No voice on Frequency                   |     |     |       | W     |     |             |
| Voice on Frequency                      |     |     |       | blank |     |             |
| **Collocation**                         |     |     |       |       |     |             |
| BFO Operation                           |     |     |       |       | B   |             |

## Waypoint Description Code

| **Description                                               | **1** | **2** | **3** | **4** |
|-------------------------------------------------------------|-------|-------|-------|-------|
| Airport as Waypoint                                         | A     |       |       |       |
| Essential Waypoint                                          | E     |       |       |       |
| Off Airway Waypoint                                         | F     |       |       |       |
| Runway as Waypoint                                          | G     |       |       |       |
| Heliport as Waypoint                                        | H     |       |       |       |
| NDB navaid as Waypoint                                      | N     |       |       |       |
| Phantom Waypoint                                            | P     |       |       |       |
| Non-Essential Waypoint                                      | R     |       |       |       |
| Transition Essential Waypoint                               | T     |       |       |       |
| VHF Navaid as Waypoint                                      | V     |       |       |       |
| end of SID/STAR/IAP route Type                              |       | B     |       |       |
| end of enroute Airway or terminal procedure                 |       | E     |       |       |
| uncharted airway intersection                               |       | U     |       |       |
| Fly-Over Waypoint                                           |       | Y     |       |       |
| unnamed Stepdown Fix After final Approach Fix               |       |       | A     |       |
| unnamed Stepdown Fix before Final Approach Fix              |       |       | B     |       |
| ATC Compulsory Waypoint                                     |       |       | C     |       |
| Oceanic Gateway Waypoint                                    |       |       | G     |       |
| First leg of Missed Approach Procedure                      |       |       | M     |       |
| Path Point Fix                                              |       |       | P     |       |
| Named Stepdown Fix                                          |       |       | S     |       |
| Initial Approach Fix                                        |       |       |       | A     |
| Intermediate Approach Fix                                   |       |       |       | B     |
| Initial Approach Fix with Holding                           |       |       |       | C     |
| Initial Approach with Final Approach Course Fix             |       |       |       | D     |
| Final End Point Fix                                         |       |       |       | E     |
| Published Final Approach Fix or Database Final Approach Fix |       |       |       | F     |
| Holding Fix                                                 |       |       |       | H     |
| Final Approach Course Fix                                   |       |       |       | I     |
| Published Missed Approach Point Fix                         |       |       |       | M     |

## Waypoint Type for Enroute Waypoints (EA)

| **Waypoint Type**                                         | **1** | **2** | **3** |
|-----------------------------------------------------------|-------|-------|-------|
| Combined named intersection and RNAV                      | C     |       |       |
| unnamed, charted intersection                             | I     |       |       |
| NDB navaid as waypoint                                    | N     |       |       |
| named intersection                                        | R     |       |       |
| uncharted Airway intersection                             | U     |       |       |
| VFR Waypoint                                              | V     |       |       |
| RNAV Waypoint                                             | W     |       |       |
| Final Approach Fix                                        |       | A     |       |
| Initial and Final Approach Fix                            |       | B     |       |
| Final Approach Course Fix                                 |       | C     |       |
| Intermediate Approach Fix                                 |       | D     |       |
| Off-Route intersection in the FAA National Ref System     |       | E     |       |
| Off-Route Intersection                                    |       | F     |       |
| Initial Approach Fix                                      |       | I     |       |
| Final Approach Course Fix at Initial Approach Fix         |       | K     |       |
| Final Approach Course Fix at Intermediate Approach Fix    |       | L     |       |
| Missed Approach Fix                                       |       | M     |       |
| Initial Approach Fix and Missed Approach Fix              |       | N     |       |
| Oceanic Entry/Exit Waypoint                               |       | O     |       |
| Pitch and Catch Point in the FAA High Altitude Redesign   |       | P     |       |
| AACAA and SUA Waypoints in the FAA High Altitude Redesign |       | S     |       |
| FIR/UIR or Controlled Airspace Intersection               |       | U     |       |
| Latitude/Longitude Intersection Full Degree of Latitude   |       | V     |       |
| Latitude/Longitude Intersection, Half Degree Latitude     |       | W     |       |
| SID                                                       |       |       | D     |
| STAR                                                      |       |       | E     |
| Approach                                                  |       |       | F     |
| Multiple                                                  |       |       | Z     |

## Waypoint Type for Terminal Waypoints (PC)

| **Waypoint Type**                                      | **1** | **2** | **3** |
|--------------------------------------------------------|-------|-------|-------|
| ARC Center Fix Waypoint                                | A     |       |       |
| Combined Named Intersection and RNAV Waypoint          | C     |       |       |
| Unnamed, Charted Intersection                          | I     |       |       |
| Middle Marker as Waypoint                              | M     |       |       |
| Terminal NDB Navaid as Waypoint                        | N     |       |       |
| Outer Marker as Waypoint                               | O     |       |       |
| Named Intersection                                     | R     |       |       |
| VFR Waypoint                                           | V     |       |       |
| RNAV Waypoint                                          | W     |       |       |
| Final Approach Fix                                     |       | A     |       |
| Initial Approach Fix and Final Approach Fix            |       | B     |       |
| Final Approach Course Fix                              |       | C     |       |
| Intermediate Approach Fix                              |       | D     |       |
| Initial Approach Fix                                   |       | I     |       |
| Final Approach Course Fix at Initial Approach Fix      |       | K     |       |
| Final Approach Course Fix at Intermediate Approach Fix |       | L     |       |
| Missed Approach Fix                                    |       | M     |       |
| Initial Approach Fix and Missed Approach Fix           |       | N     |       |
| Unnamed Stepdown Fix                                   |       | P     |       |
| Named Stepdown Fix                                     |       | S     |       |
| FIR/UIR or Controlled Airspace Intersection            |       | U     |       |
| SID                                                    |       |       | D     |
| STAR                                                   |       |       | E     |
| Approach                                               |       |       | F     |
| Multiple                                               |       |       | Z     |

## Cruise Table Identifier

| **Field** | **Description**                    |
|-----------|------------------------------------|
| AA        | ICAO standard cruise table         |
| AO        | Exception to ICAO cruise table     |
| BB-ZZ     | Modified cruise table              |
| BO-ZO     | Exception to modified cruise table |

## FIR/UIR Indicator

| **Field** | **Type**         |
|-----------|------------------|
| F         | FIR              |
| U         | UIR              |
| B         | Combined FIR/UIR |

## Waypoint Usage

| **Usage**             | **1** | **2** |
|-----------------------|-------|-------|
| High and Low Altitude |       | B     |
| High Altitude only    |       | H     |
| Low Altitude only     |       | L     |
| Terminal Use Only     |       | blank |
| RNAV                  | R     |       |

## Turn Direction

| **Turn** | **Description** |
|----------|-----------------|
| L        | Left turn       |
| R        | Right turn      |

## Route Type for Enroute Airways

| **Airway Type**                                | **Field Content** |
|------------------------------------------------|-------------------|
| Control                                        | C                 |
| Direct Route                                   | D                 |
| Helicopter Route                               | H                 |
| Offical Designated Airways expect RNAV Airways | O                 |
| RNAV Airways                                   | R                 |
| Undesignated ATS Route                         | S                 |

## Route Type for SIDs (PD)

| **SID Route Type**            | **Field Content** |
|-------------------------------|-------------------|
| Engine Out SID                | 0                 |
| SID Runway Transition         | 1                 |
| SID or SID Common Route       | 2                 |
| SID Enroute Transition        | 3                 |
| RNAV SID Runway Transition    | 4                 |
| RNAV SID or SID Common Route  | 5                 |
| RNAV SID Enroute Transition   | 6                 |
| FMS SID Runway Transition     | F                 |
| FMS SID or SID Common Route   | M                 |
| FMS SID Enroute Transition    | S                 |
| Vector SID Runway Transition  | T                 |
| Vector SID Enroute Transition | V                 |

## Route Type for STARs (PE)

| **STAR Route Type**                | **Field Content** |
|------------------------------------|-------------------|
| STAR Enroute Transition            | 1                 |
| STAR or STAR Common Route          | 2                 |
| STAR Runway Transition             | 3                 |
| RNAV STAR Enroute Transition       | 4                 |
| RNAV STAR or STAR Common Route     | 5                 |
| RNAV STAR Runway Transition        | 6                 |
| Profile Descent Enroute Transition | 7                 |
| Profile Descent Common Route       | 8                 |
| Profile Descent Runway Transition  | 9                 |
| FMS STAR Enroute Transition        | F                 |
| FMS STAR or STAR Common Route      | M                 |
| FMS STAR Runway Transition         | S                 |

## Route Type of IAPs (PF)

| **IAP Route Type**               | **Field Content** |
|----------------------------------|-------------------|
| Approach Transition              | A                 |
| Localizer/Back course Approach   | B                 |
| VORDME Approach                  | D                 |
| FMS Approach                     | F                 |
| IGS Approach                     | G                 |
| ILS Approach                     | I                 |
| GLS Approach                     | J                 |
| LOC only Approach                | L                 |
| MLS Approach                     | M                 |
| NDB Approach                     | N                 |
| GPS Approach                     | P                 |
| NDB DME Approach                 | Q                 |
| RNAV Approach                    | R                 |
| VOR Approach using VORDME/VORTAC | S                 |
| TACAN Approach                   | T                 |
| SDF Approach                     | U                 |
| VOR Approach                     | V                 |
| MLS Type A Approach              | W                 |
| LDA Approach                     | X                 |
| MLS Type B and C Approach        | Y                 |
| Missed Approach                  | Z                 |

## Level

| **Level** | **Description**    |
|-----------|--------------------|
| B         | All Altitudes      |
| H         | High Level Airways |
| L         | Low Level Airways  |

## Directional Restriction

| **Direction** | **Description**                                          |
|---------------|----------------------------------------------------------|
| F             | One way in direction route is coded (Forward)            |
| B             | One way in opposite direction route is coded (backwards) |
| blank         | no restrictions on direction                             |

## IFR Capability

| **Direction** | **Description**                            |
|---------------|--------------------------------------------|
| Y             | Instrument Approach Procedure published    |
| N             | no Instrument Approach Procedure published |

## Longest Runway Surface

| **Code** | **Description**                      |
|----------|--------------------------------------|
| H        | Hard Surface (asphalt or concrete)   |
| S        | Soft Surface (gravel, grass or soil) |
| W        | Water Runway                         |
| U        | undefined                            |

## ILS/MLS/GLS Category

| **Definition**                     | **Category/Classification** |
|------------------------------------|-----------------------------|
| ILS Localizer Only, no glideslope  | 0                           |
| ILS Localizer/MLS/GLS Category I   | 1                           |
| ILS Localizer/MLS/GLS Category II  | 2                           |
| ILS Localizer/MLS/GLS Category III | 3                           |
| IGS Facility                       | I                           |
| LDA Facility with Glideslope       | L                           |
| LDA Facility no Glideslope         | A                           |
| SDF Facility with Glideslope       | S                           |
| SDF Facility no Glideslope         | F                           |

## Marker Type

| **Field** | **Type of Facility** |
|-----------|----------------------|
| -IM       | Inner Marker         |
| -MM       | Middle Marker        |
| -OM       | Outer Marker         |
| -BM       | Back Marker          |
| L\--      | Locator at Marker    |

\- blank

## Reporting Units Speed

| **Field** | **Reporting Units**   |
|-----------|-----------------------|
| 0         | not specified         |
| 1         | TAS in Knots          |
| 2         | TAS in Mach           |
| 3         | TAS in Kilometer/hour |

## Reporting Units Altitude

| **Field** | **Reporting Units**     |
|-----------|-------------------------|
| 0         | not specified           |
| 1         | Altitude in Flightlevel |
| 2         | Altitude in Meters      |
| 3         | Altitude in Feet        |

## Path and Termination

| **Path & Termination** | **Description**                       |
|------------------------|---------------------------------------|
| IF                     | Initial Fix                           |
| TF                     | Track to a Fix                        |
| CF                     | Course to a Fix                       |
| DF                     | Direct to a Fix                       |
| FA                     | Fix to an Altitude                    |
| FC                     | Track from a Fix for a Distance       |
| FD                     | Track from a Fix to a DME Distance    |
| FM                     | From a Fix to a Manual termination    |
| CA                     | Course to an Altitude                 |
| CD                     | Course to a DME Distance              |
| CI                     | Course to an Intercept                |
| CR                     | Course to a Radial termination        |
| RF                     | Constant Radius Arc                   |
| AF                     | Arc to Fix                            |
| VA                     | Heading to an Altitude                |
| VD                     | Heading to a DME Distance termination |
| VI                     | Heading to an Intercept               |
| VM                     | Heading to a Manual termination       |
| VR                     | Heading to a Radial termination       |
| PI                     | 045/180 Procedure turn                |
| HA, HF, HM             | Holding in lieu of Procedure Turn     |

## Speed Limit Description

| **Speed Limit** | **Description**                                                           |
|-----------------|---------------------------------------------------------------------------|
| @               | Mandatory Speed, cross fix at speed specified in the Speed Limit field    |
| \+              | Minimum Speed, cross fix at or above speed specified in Speed Limit field |
| \-              | Maximum Speed, cross fix at or below speed specified in Speed Limit field |

## Airspace Type

| **Field** | **Description**                                      |
|-----------|------------------------------------------------------|
| A         | Class C Airspace (USA)                               |
| C         | Control Area, ICAO Designation (CTA)                 |
| K         | Control Area, ICAO Designation (CTA)                 |
| M         | Terminal Control Area, ICAO Designation (TMA or TCA) |
| Q         | Military Control Zone (MCTR)                         |
| R         | Radar Zone or Radar Area (USA)                       |
| T         | Class B Airspace (USA)                               |
| W         | Terminal Control Area (TCA)                          |
| X         | Terminal Area (TMA)                                  |
| Y         | Terminal Radar Service Area (TRSA)                   |
| Z         | Class D Airspace, ICAO Designation (CTR)             |

## Altitude Description

| **Field** | **Description**                                                                                                      |
|-----------|----------------------------------------------------------------------------------------------------------------------|
| \+        | at or above altitude specified in Altitude1 field                                                                    |
| \-        | at or below altitude specified in Altitude1 field                                                                    |
| @         | at altitude specified in Altitude1 field                                                                             |
| B         | at or above to at or below altitudes in Altitude1 field and Altitude2 field                                          |
| C         | at or above altitude specified in Altitude2 field                                                                    |
| G         | Glide Slope altitude (MSL) specified in Altitude2 field and at altitude specified in Altitude1 field                 |
| H         | Glide Slope altitude (MSL) specified in Altitude2 field and at or above altitude specified in Altitude1 field        |
| I         | Glide Slope Intercept Altitude specified in Altitude2 field and at altitude specified in Altitude1 field             |
| J         | Glide Slope Intercept Altitude specified in Altitude2 field and at or above altitude specified in Altitude1 field    |
| V         | at altitude on the coded vertical angle in the Altitude2 field and at or above altitude specified in Altitude1 field |
| X         | at altitude on the coded vertical angle in Altitude2 field and at altitude specified in Altitude1 field              |
| Y         | at altitude on the coded vertical angle in Altitude2 field and at or below altitude specified in the Altitude1 field |

## Boundary Via

| **Field** | **Description**                            |
|-----------|--------------------------------------------|
| C-        | Circle                                     |
| G-        | Great Circle                               |
| H-        | Rhumb Line                                 |
| L-        | Counter Clockwise ARC                      |
| R-        | Clockwise ARC                              |
| -E        | End of description, return to origin point |

\- blank

## Restrictive Airspace Type

| **Field** | **Type**                 |
|-----------|--------------------------|
| A         | Alert                    |
| C         | Caution                  |
| D         | Danger                   |
| M         | Military Operations Area |
| P         | Prohabited               |
| R         | Restricted               |
| T         | Training                 |
| W         | Warning                  |
| U         | Unknown                  |

#   {#section .unnumbered}

## Restriction Type

| **Field** | **Description**            |
|-----------|----------------------------|
| AE        | Altitude exclusion         |
| TC        | Cruising Table Replacement |
| SC        | Seasonal Restriction       |
| NR        | Note Restriction           |

## Units of Altitude

| **Field** | **Description**                                           |
|-----------|-----------------------------------------------------------|
| F         | Restriction Altitudes are expressed in hundreds of feet   |
| K         | Restriction Altitudes are expressed in metric Flightlevel |
| L         | Restriction Altitudes are expressed in feet Flightlevel   |
| M         | Restriction Altitudes are expressed in tens of meters     |

## Block Indicator

| **Field** | **Description**                         |
|-----------|-----------------------------------------|
| B         | indicates an altitude block             |
| I         | indicates an individual altitudes block |

## Frequency Units  {#frequency-units}

| **Field** | **Description**                             |
|-----------|---------------------------------------------|
| H         | High Frequency (3.000 kHz -- 30.000 kHz)    |
| V         | Very High Frequency (30.000 kHz -- 200 MHz) |
| U         | Ultra High Frequency (200 MHz -- 3.000 MHz) |
| C         | Communication Channel for 8.33 kHz spacing  |

## Station Type

| **Field** | **Description**         |
|-----------|-------------------------|
| L\--      | LAAS/GLS ground station |
| C\--      | SCAT-1 station          |

\- blank

## Communication Type  {#communication-type}

|           |                                                | Communication |             |          |
|-----------|------------------------------------------------|---------------|-------------|----------|
| **Field** | **Description**                                | **Airport**   | **Enroute** | **Both** |
| ACC       | Area Control Center                            |               |             | X        |
| ACP       | Airlift Command Post                           | X             |             |          |
| AIR       | Air to Air                                     | X             |             |          |
| APP       | Approach Control                               | X             |             |          |
| ARR       | Arrival Control                                | X             |             |          |
| ASO       | Automatic Surface Oserving System (ASOS)       | X             |             |          |
| ATI       | Automatic Terminal Information Services (ATIS) | X             |             |          |
| AWI       | Airport Weather Information Broadcast (AWIB)   | X             |             |          |
| AWO       | Automatic Weather Observing Service (AWOS)     |               |             | X        |
| AWS       | Aerodrome Weather Information Service (AWIS)   | X             |             |          |
| CLD       | Clearance Delivery                             | X             |             |          |
| CPT       | Clearance, Pre-Taxi                            | X             |             |          |
| CTA       | Control Area (Terminal)                        | X             |             |          |
| CTL       | Control                                        |               |             | X        |
| DEP       | Departure Control                              | X             |             |          |
| DIR       | Director (Approach Control Radar)              | X             |             |          |
| EFS       | Enroute Flight Advisory Service (EFAS)         |               | X           |          |
| EMR       | Emergency                                      |               |             | X        |
| FSS       | Flight Service Station                         |               |             | X        |
| GCO       | Ground Comm Outlet                             | X             |             |          |
| GND       | Ground Control                                 | X             |             |          |
| GET       | Gate Control                                   | X             |             |          |
| HEL       | Helicopter Frequency                           | X             |             |          |
| INF       | Information                                    |               |             | X        |
| MIL       | Military Frequency                             |               |             | X        |
| MUL       | Multicom                                       |               |             | X        |
| OPS       | Operations                                     | X             |             |          |
| PAL       | Pilot Activated Lighting                       | X             |             |          |
| RDO       | Radio                                          |               |             | X        |
| RDR       | Radar                                          |               |             | X        |
| RFS       | Remote Flight Service Station (RFSS)           |               |             | X        |
| RMP       | Ramp/Taxi Control                              | X             |             |          |
| RSA       | Airport Radar Service Area (ARSA)              | X             |             |          |
| TCA       | Terminal Control Area (TCA)                    | X             |             |          |
| TMA       | Terminal Control Area (TMA)                    | X             |             |          |
| TML       | Terminal                                       | X             |             |          |
| TRS       | Terminal Radar Service Area (TRSA)             | X             |             |          |
| TWE       | Transcriber Weather Broadcast (TWEB)           |               | X           |          |
| TWR       | Tower, Air Traffic Control                     | X             |             |          |
| UAC       | Upper Area Control                             |               | X           |          |
| UNI       | Unicom                                         | X             |             |          |
| VOL       | Volmet                                         |               | X           |          |

## Service Indicator

| **Field** | **Description**                             |
|-----------|---------------------------------------------|
| A\--      | Airport Advisory Service (AAS)              |
| C\--      | Community Aerodrome Radio Station (CARS)    |
| D\--      | Departure Service                           |
| F\--      | Flight Information Service (FIS)            |
| I\--      | Initial Contact (IC)                        |
| L\--      | Arrival Service                             |
| P\--      | Pre-Departure Clearance (Data Link Service) |
| S\--      | Aerodrof Flight Information Service (AFIS)  |
| T         | Terminal Area Control                       |
| -A-       | Aerodrome Traffic Frequency (ATF)           |
| -C-       | Common Traffic Advisory Frequency (CTAF)    |
| -M-       | Mandatory Frequency (MF)                    |
| -R-       | Air/Air                                     |
| -S-       | Secondary Frequency                         |
| \--A      | Air/Ground                                  |
| \--D      | VHF Direction Finding Service (VDF)         |
| \--G      | Remote Communications Air to Ground (RCAG)  |
| \--L      | Language other than English                 |
| \--M      | Military Use Frequency                      |
| \--P      | Pilot Controlled Light (PCL)                |
| \--R      | Remote Communications Outlet (RCO)          |

\- blank

## FIR/UIR Address

| **Field** | **Description**               |
|-----------|-------------------------------|
| ZQZX      | related to an IFR Flight      |
| ZFZX      | related to an VFR Flight      |
| ZOZX      | related to an Oceanic FIR/UIR |
| ZRZX      | related to all other FIR/UIR  |

## Time Codes

| **Field** | **Description**                                         |
|-----------|---------------------------------------------------------|
| C         | active continuously, including holidays                 |
| H         | active continuously, excluding holidays                 |
| N         | active none continuously, refer to continuation records |
| (blank)   | active times announced by Notams                        |

## Time Distance

| **Field** | **Description**            |
|-----------|----------------------------|
| T         | Time in minute(s)          |
| D         | Distance in nautical miles |


## Airport Type

| **Field** | **Description**                                                               |
|-----------|-------------------------------------------------------------------------------|
| C         | Public open airport/heliport (civil)                                          |
| M         | Military airport/heliport (nonpublic)                                         |
| P         | Airport is privately operated (added per specific manufactureer request only) |

## Fuel Type

Each position at this field will indicate availability based on:

| **Field** | **Description**                             |
|-----------|---------------------------------------------|
| Y         | The fuel type is available                  |
| N         | The fuel type is not available              |
| P         | The fuel type is available on prior request |

| **Pos** | **Description**          |
|---------|--------------------------|
| 1       | Unspecified              |
| 2       | 73 octane                |
| 3       | 80-87 octane             |
| 4       | 100 low lead (LL) octane |
| 5       | 100-130 octane           |
| 6       | 115-145 octane           |
| 7       | Mogas                    |
| 8       | JET                      |
| 9       | JET A                    |
| 10      | JET A-1                  |
| 11      | JET A+                   |
| 12      | JET B                    |
| 13      | JET 4                    |
| 14      | JET 5                    |

## Time Zone

| **Field** | **Difference to Zulu time** |
|-----------|-----------------------------|
| Z         | 0                           |
| A         | -1                          |
| B         | -2                          |
| C         | -3                          |
| D         | -4                          |
| E         | -5                          |
| F         | -6                          |
| G         | -7                          |
| H         | -8                          |
| I         | -9                          |
| K         | -10                         |
| L         | -11                         |
| M         | -12                         |
| N         | +1                          |
| O         | +2                          |
| P         | +3                          |
| Q         | +4                          |
| R         | +5                          |
| S         | +6                          |
| T         | +7                          |
| U         | +8                          |
| V         | +9                          |
| W         | +10                         |
| X         | +11                         |
| Y         | +12                         |

## Part Time Lights

| **Field** | **Description**                                            |
|-----------|------------------------------------------------------------|
| Y         | Part time runway lighting availability at an airport       |
| N         | Full time (24h) runway lighting availability at an airport |

## Runway Lighting

| **Field** | **Description**                 |
|-----------|---------------------------------|
| Y         | Runway has edge lights          |
| N         | No runway edge lights available |

## Traffic Pattern

| **Field** | **Description**            |
|-----------|----------------------------|
| L         | left-hand traffic pattern  |
| R         | right-hand traffic pattern |

## Course Flag

| **Field** | **Description** |
|-----------|-----------------|
| M         | Magnetic Course |
| T         | True Course     |

## GNSS/FMS Indicator

| **Field** | **Description**                                                          |
|-----------|--------------------------------------------------------------------------|
| 0         | Procedure not authorized for GNSS/FMS                                    |
| 1         | Procedure authorized for GNSS (primary navaids operating and monitored)  |
| 2         | Procedure authorized for GNSS (primary navaids installed, not monitored) |
| 3         | Procedure authorized for GNSS (GPS or GNSS in the title)                 |
| 4         | Procedure authorized for FMS                                             |
| 5         | Procedure authorized for FMS and/or GNSS                                 |
| A         | RNAV(GPS) or RNAV(GNSS) procedure SBAS used authorized                   |
| B         | RNAV(GPS) or RNAV(GNSS) procedure use not authorized SBAS                |
| C         | RNAV(GPS) or RNAV(GNSS) procedure, SBAS used not specific                |
| P         | Stand alone GPS(GNSS) procedure                                          |
| U         | Procedure authorization not specified                                    |

## Level of Service Authorized

| **Field** | **Description**                                                 |
|-----------|-----------------------------------------------------------------|
| A         | Designated Level of Service is authorized for the procedure     |
| N         | Designated Level of Sercice is not authorized for the procedure |

## Guard/Transmit

| **Field** | **Description**         |
|-----------|-------------------------|
| G         | Radio guards (receives) |
| T         | Radio transmits         |

## Time Of Operation

The first two positions identify days of the week, with Monday equal to 1 and Sunday equal to 7. A single day, for example, Monday, is depicted as "01". A consecutive series of days, for example Monday through Friday, is depicted as "15". Non-consecutive days require multiple Time of Operation entries. The remaining 8 characters define a starting time of four characters and an ending time of four characters. These times are in the format HHMM (H=hours, M= minutes) using a 24 hour time system.

Times of Operation can also be expressed in terms of Sunrise (SR) and Sunset (SS). When a "Time of Operation" is defined as starting at or ending at Sunrise, that time is specified as "000R". When a "Time of Operation" is defined as starting at or ending at Sunset, that time is specified as "000S". When a "Time of Operation" is defined as starting at or ending at a certain number of hours/minutes before or after Sunrise or Sunset, those times are specified

## Quadrant Code

| **Field** | **Description**    |
|-----------|--------------------|
| A         | Southwest quadrant |
| B         | Northwest quadrant |
| C         | Northeast quadrant |
| D         | Southeast quadrant |

## Block Indicator

| **Field** | **Description**                                              |
|-----------|--------------------------------------------------------------|
| B         | all altitudes from xxxx to yyyy feet are not availabe        |
| I         | individual altitudes of xxxx and yyyy feet are not available |

## Procedure Leg Data Fields (minimum requirements)

AF (Arc to a Fix)                       - FixIdentifier, TurnDirection, RecommendedNavaid, Theta (fix radial), RHO, MagCourse (boundary radial)
CA (Course to an Altitude)              - MagCourse, Alt1+ (at or above)
CD (Course to a DME Distance)           - RecommendedNavaid, MagCourse RouteDISTANCEHoldingTime (DME distance)
CF (Course to a Fix)                    - FixIdentifier, RecommendedNavaid, Theta, RHO, MagCourse (course to specific fix), RouteDISTANCEHoldingTime (Path length)
CI (Course to Intercept)                - MagCourse
CR (Course to a Radial termination)     - RecommendedNavaid, Theta, MagCourse
DF (Direct to a Fix)                    - FixIdentifier, FlyOver
FA (Fix to an Altitude)                 - FixIdentifier, RecommendedNavaid, Theta, RHO, MagCourse, Alt1+ (at or above)
FC (Track from a Fix for a Distance)    - FixIdentifier, FlyOver, RecommendedNavaid, Theta, RHO, MagCourse, RouteDISTANCEHoldingTime (Path length)
FD (Track from a Fix to a DME Distance) - FixIdentifier, RecommendedNavaid, Theta, RHO, MagCourse, RouteDISTANCEHoldingTime (DME distance)
FM (From a Fix to Manual termination)   - FixIdentifier, RecommendedNavaid, Theta, RHO, MagCourse
HA (Holding mandatory)                  - FixIdentifier, TurnDirection, MagCourse, RouteTIMEDistanceHoldingTime, Alt1+ (at or above)
HF (Holding in lieu of Procedure Turn)  - FixIdentifier, TurnDirection, MagCourse, RouteTIMEDistanceHoldingTime
HM (Holding mandatory)                  - FixIdentifier, TurnDirection, MagCourse, RouteTIMEDistanceHoldingTime
IF (Initial Fix)                        - FixIdentifier
PI (045/180 Procedure Turn)             - FixIdentifier, TurnDirection, RecommendedNavaid, Theta, RHO, MagCourse, RouteTimeDISTANCEHoldingTime (excursion distance from fix), Alt1
RF (Constant Radius Arc)                - FixIdentifier, TurnDirection, Theta (inbound tangential track), MagCourse, RouteTimeDISTANCEHoldingTime (along track distance), ArcCenter
TF (Track to a Fix)                     - FixIdentifier, Overfly
VA (Heading to an Altitude)             - MagCourse (Heading), Alt1+ (at or above)
VD (Heading to a DME Distance)          - RecommendedNavaid, MagCourse (Heading), RouteTimeDISTANCEHoldingTime (DME distance)
VI (Heading to an Intercept)            - MagCourse (Heading)
VM (Heading to a Manual Termination)    - MagCourse(Heading)
VR (Heading to a Radial)                - RecommendedNavaid, Theta, MagCourse (Heading)