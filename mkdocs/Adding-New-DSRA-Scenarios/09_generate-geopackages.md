# 9. Generate GeoPackages

Currently, the GeoPackages are generated after a successful stack build on a Windows environment a batch file. The batch file relies on the system having installed below:

- [GDAL ogr2ogr](https://gdal.org/programs/ogr2ogr.html)
- [7zip](https://www.7-zip.org/)

The batch file uses ogr2ogr to convert the tables from the PostgreSQL database into GeoPackages and 7zip to compress the GeoPackages afterward. The names of the GeoPackages are based on the same names in the PostgreSQL tables.

A sample bat script for for the latest 13 scenario DSRA outputs can be found in [here](https://github.com/OpenDRR/opendrr-data-store/blob/master/scripts/Data%20Export/3.create_dsra_outputs_combined_Apr2023_13_new_scenarios.bat).

Sample snippet from the script above for indicators_s GeoPackages:

```bat
REM Geopackage dsra, _indicators_s
FOR %%x IN (dsra_acm4p9_vedderfault, ^
	dsra_acm5p0_georgiastraitfault, ^
	dsra_acm5p0_mysterylake, ^
	dsra_acm5p2_beaufortfault, ^
	dsra_acm5p2_vedderfault, ^
	dsra_acm5p5_southeypoint, ^
	dsra_acm5p7_southeypoint, ^
	dsra_acm7p7_queencharlottefault, ^
	dsra_acm8p0_queencharlottefault, ^
	dsra_scm5p0_burlingtontorontostructuralzone, ^
	dsra_scm5p0_rougebeach, ^
	dsra_scm5p6_gloucesterfault, ^
	dsra_scm5p9_millesilesfault) DO ogr2ogr -f "gpkg" D:\Workspace\data\view_outputs\all_indicators\earthquake_scenarios\%%x_indicators_s.gpkg PG:"host=localhost user=postgres dbname=opendrr password=admin port=5432" -sql "SELECT * FROM results_%%x.%%x_indicators_s" -nln %%x_indicators_s
	
REM earthquake scenarios
CD /D "D:\Workspace\data\view_outputs\all_indicators\earthquake_scenarios\" && FOR %%i IN (*.*)	DO 7z.exe a "%%~ni.zip" "%%i"
DEL *.gpkg
```

The user can adjust the script to include which dsra scenarios to be exported {eqScenario}, where to export the GeoPackages {drive}, {Directory Path} and database information (user=postgres dbname=opendrr password=admin port=5432) by modifying the script accordingly.

```bat
REM Geopackage dsra, _indicators_s
FOR %%x IN ({eqScenario}) DO ogr2ogr -f "gpkg" {Directory Path}\%%x_indicators_s.gpkg PG:"host=localhost user=postgres dbname=opendrr password=admin port=5432" -sql "SELECT * FROM results_%%x.%%x_indicators_s" -nln %%x_indicators_s

REM earthquake scenarios
CD /D "{Directory Path}\" && FOR %%i IN (*.*) DO 7z.exe a "%%~ni.zip" "%%i"
DEL *.gpkg
```

In the script, the following outputs are generated per scenario:

- {eqScenario}_indicators_b
    - building level indicators
- {eqScenario}_indicators_s
    - sauid level indicators
- {eqScenario}_indicators_csd
    - census subdivision level indicators
- {eqScenario}_indicators_shakemap
    - shakemap points
- {eqScenario}_indicators_shakemap_hexgrid_1km
    - shakemap point max values attributed to a EPSG 3857 1km hexgrid with shorelines clipped
- {eqScenario}_indicators_shakemap_hexgrid_1km_uc
    - shakemap point max values attributed to a EPSG 3857 1km hexgrid with shorelines unclipped
- {eqScenario}_indicators_shakemap_hexgrid_5km
    - shakemap point max values attributed to a EPSG 3857 5km hexgrid with shorelines clipped
- {eqScenario}_indicators_shakemap_hexgrid_5km_uc
    - shakemap point max values attributed to a EPSG 3857 5km hexgrid with shorelines unclipped
- {eqScenario}_indicators_shakemap_hexgrid_10km
    - shakemap point max values attributed to a EPSG 3857 10km hexgrid with shorelines clipped
- {eqScenario}_indicators_shakemap_hexgrid_10km_uc
    - shakemap point max values attributed to a EPSG 3857 10km hexgrid with shorelines unclipped
- {eqScenario}_indicators_shakemap_hexgrid_25km
    - shakemap point max values attributed to a EPSG 3857 25km hexgrid with shorelines clipped
- {eqScenario}_indicators_shakemap_hexgrid_25km_uc
    - shakemap point max values attributed to a EPSG 3857 25km hexgrid with shorelines unclipped
- {eqScenario}_indicators_shakemap_hexgrid_50km_uc
    - shakemap point max values attributed to a EPSG 3857 50km hexgrid with shorelines unclipped
- {eqScenario}_indicators_shakemap_hexgrid_100km_uc
    - shakemap point max values attributed to a EPSG 3857 100km hexgrid with shorelines unclipped
- shakemap_scenario_extents
    - table showing extents (polygon) of each scenario in the PostgreSQL database
