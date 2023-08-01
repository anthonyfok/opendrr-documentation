# 10. Generate vector tiles

The vector tiles are generated from the GeoPackages using:

- [GeoServer](https://geoserver.org/)
- [Vector Tiles Extension](https://docs.geoserver.org/stable/en/user/extensions/vectortiles/install.html)

For RiskProfiler purposes only the following GeoPackages are required to create the vector tiles:

- {eqScenario}_indicators_s
- {eqScenario}_indicators_csd
- {eqScenario}_indicators_shakemap_hexgrid_1km
- {eqScenario}_indicators_shakemap_hexgrid_5km
- {eqScenario}_indicators_shakemap_hexgrid_25km

Each set of geopackage needs **2 sets** of vectors tiles in **EPSG 4326** and **EPSG 900913**. In total for one {eqScenario}, there would be a total of 10 vector tile datasets that are created.

## Steps for running GeoServer

1. Install GeoServer and Vector Tiles extension (see [Notes](#notes)).
2. Start GeoServer and Open GeoServer Web Portal. Login to Geoserver (default credentials are admin/geoserver).
3. In Tile Caching section => Blobstores => Add new
    - Type of BlobStore: File BlobStore, Identifier: opendrr
    - Make sure Enabled/Default are checked
    - Base Directory: *Whatever directory you want the tiles to be stored* i.e. C:\GeoServer_Tiles\
    - Tiles directory layout: SLIPPY
4. In Data section => Stores => Add new Store
    - Vector Data Sources => GeoPackage
    - Make sure Workspace is set to the default BlobStore created in step 3, in Connection Parameters browse and select the GeoPackage. Under Data Source Name, add the name of the GeoPackage i.e. {eqScenario}_indicators_s and save.
5. In New Layer, hit Publish on the Layer name that was created in step 4. Scroll down to Bounding Boxes, click Compute from data and Compute from native bounds to get the bounding box extents. Scroll back up to the top and click on Tile Caching tab, ensure metatiling factors are set to 1 tiles wide by 1 tiles high. In Tile Image Format section, check application/vnd.mapbox-vector-tile and uncheck image/jpeg and image/png. Leave all other items default and click Save.
6.  In Tile Caching section => Tile Layers
    - Select seed/Truncate for the layer you want to create the layers
    - Number of tasks to use: Select the number of threads you want this process to use (see [Notes](#notes))
    - EPSG: 4326
    - Format: application/vnd.mapbox-vector-tile
    - Zoom stop: (see [Notes](#notes))
    - Repeat the step for EPSG 900913
7. Repeat steps 4 to 7 for all the GeoPackage.
8. Once complete, all the tiles will be located in the default path specified in step 3 i.e. C:\GeoServer_Tiles\. The tiles can then be compressed and uploaded.
9. *Check with Anthony on the steps after.*


## Notes

- Step 1
    - Extension installation mentions to copy all extracted contents to the WEB-INF/lib which can be hard to find. The precise default path in Windows is

        > C:\Program Files\GeoServer\webapps\geoserver\WEB-INF\lib

- Step 6
    - Generating the vector tiles requires writing of millions of files, and depending on the size of the scenario, can take hours to generate.
      **A faster computer with adequate CPU, RAM, and storage is recommended when generating the tiles.**
      Currently the tiles are generated using an AWS EC2 instance (16 vCPUs, 32gb RAM, 200GB storage using c6i.4xlarge tier).
    - Intel CPUs has better performance over AMD
    - GeoServer supports up to 16 threads at a time, i.e. you can run several processes with 8 threads each and it will run both. If you add another process it will just queue up until one of the processes are completed.
    - Zoom stop levels for each GeoPackage:
        - _s, _csd, _1km, _5km = Zoom stop: 14
        - 25km = Zoom stop: 12
		
- General
    -  While generating the tiles, the Geoserver log (default location C:\Program Files\GeoServer\logs) file grows quite large, it can be safely erased once all the tiles are finished processing. The log file will be recreated once GeoServer starts again.

