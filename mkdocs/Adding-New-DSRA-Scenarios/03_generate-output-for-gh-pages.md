# 3. Generate outputs and summaries for GitHub Pages?

### Goals

Generate the necessary summary, map preview, etc. for:

1. Data and Download (?) page for earthquake-scenarios, see <https://opendrr.github.io/earthquake-scenarios/en/index.html>
2. For data needed by stack build (TODO: which files exactly?)
3. For RiskProfiler at <https://www.riskprofiler.ca>, e.g. FINISHED/scenarios.csv (TODO: check to see how HabitatSeven is using that file, and whether there are other files.)

### Steps

1. Run ../scripts/TakeSnapshot.py for each new scenario, using values from the .csv filenames

    This is to generate the map preview image (.png) and summary file (.md).
    See [commit e79aed6](https://github.com/OpenDRR/earthquake-scenarios/pull/77/commits/e79aed677ede45301b804c589c471dafc5b4079b) for an example.

    1. cd into FINISHED directory

    2. run `python3 ../scripts/TakeSnapshot.py {scenario name} {EXPO} {HAZ} {DMGb0} {DMGr1} {LOSSb0} {LOSSr1}` where:
        * EXPO (b or s) from last letter of scenario .csv file names
        * HAZ from s_shakemap .csv filename
        * DMGb0 from s_dmgbyasset .csv filename following b0_
        * DMGr1 from s_lossesbyasset .csv filename following r1_
        * LOSSb0 from s_lossesbyasset .csv filename following b0_
        * LOSSr1 from s_lossesbyasset .csv filename following r1_

        For example:
        ```
        ../scripts/TakeSnapshot.py ACM4p9_GeorgiaStraitFault b 1 2 3 4 5
        ../scripts/TakeSnapshot.py ACM7p4_DenaliFault b 181 182 183 184 185
        ../scripts/TakeSnapshot.py SCM5p5_ConstanceBay b 118 119 120 121 122
        ```

        Update: As of 2023-04-20, there is now a faster and more automatic method using ../scripts/run-TakeShapshot.  Edit the `scenarios` array therein, and run the script, and commit the new or updated FINISHED/*.{md,png} files.

2. Run ../scripts/FinishedMap.sh from FINISHED directory

    which updates FinishedScenarios{,Fr}.geojson, FinishedScenarios.md and scenarios.csv files.

3. Add French translations for added scenarios

    The descriptions are currently translated using DeepL Translator (deepl.com) with the following adjustments:

    - Quotation marks are replaced with guillemets « »
    - U+202F ` ` NARROW NO-BREAK SPACE (NNBSP) (espace fine insécable) are used before %, », and after «.
    - Typewriter apostrophes (') are replaced with typographic apostrophes (’)

4. Calculate or find the map extents, and fill them into appropriate documents:
    - There are 3 sets of map extents for each earthquake scenarios:
        - sauid extent
        - csd extent
        - 5km extent
    - Will (@wkhchow) often calculates them and saves them in the Google Docs named Earthquake_Scenario_Extents
    - Update https://github.com/OpenDRR/riskprofiler/wiki/RiskProfiler-Datasets (started by Joost, updated by Drew, and updated and maintained by Damon)
        - for Anthony: `cd ~/NRCan/OpenDRR/riskprofiler.wiki` and `git log -p RiskProfiler-Datasets.md` to see past changes
    - Edit docs/_pages/{en,fr}/dsra_scenario_map.md to fix map extents
    - TODO: Create a new wiki page to document map extents in more detail.

5. Commit GeoPackage files in FINISHED/geopackages/ directory.
   Don’t forget scenarios_scenario_extents.zip which is found in Will’s OneDrive, e.g:
    - indicators/3._earthquake_scenarios/scenario_info/shakemap_scenario_extents.zip
    - indicators/3._earthquake_scenarios/13_new_scenarios_Apr2023/scenario_info/shakemap_scenario_extents.zip

    They are automatically uploaded as release assets (.github/workflows/generate_assets.yml) when a new release is published, and made available for download from GitHub Pages.

6. Run Jekyll locally to preview the GitHub Pages

    Anthony’s port 4000 is taken up by NoMachine, so he runs the following instead:
    ```
    bundle exec jekyll serve --trace --baseurl '' --port 4001
    ```

7. Upload/push the resulting changes to GitHub.  You can push to your own fork (e.g. https://anthonyfok.github.io/earthquake-scenarios/)

8. Create a pull request

9. After the pull request has been reviewed and merged, create/tag a new release.  This will trigger the upload of release assets which are referenced by the final GitHub Pages

10. Check <https://opendrr.github.io/earthquake-scenarios/> to make sure everything is OK.

    - Check if the GeoPackage files are downloadable (make sure the release version, say, v1.4.4, is in the URL)
    - Click on each map and check if it is showing the correct location (map extent) with correct shakemap data (vector tiles)
    - TODO: more checks?
