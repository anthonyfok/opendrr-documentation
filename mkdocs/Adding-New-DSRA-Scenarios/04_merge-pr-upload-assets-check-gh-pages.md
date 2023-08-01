# 4. “Merge” Pull Request, upload release assets, check GitHub Pages

- Create/update CHANGELOG.md especially when there are renames that would introduce breaking changes.
- “Merge” pull request
    - Anthony personally prefers doing it from the command-line to avoid merge bubbles, e.g. `git push origin feature-branch:master`
- Draft a new release, creating a new tag (e.g. `v1.2.1`), and publish it.
- Check to make sure the release assets are uploaded correctly by the “Upload release assets” GitHub Actions workflow.
- Check number of assets
    - e.g. Why are there 197 assets for v1.2.0 and v1.2.1?
        - 9 scenarios, each normally with 7 CSV files and 14 zipped GeoPackage files:  9×(7+14) = 189
        - 2 extra (test?) ShakeMap MMI (Modified Mercalli Intensity) CSV files, see scripts/convert_to_MMI.py:
            - s_shakemap_ACM7p0_GeorgiaStraitFault_124_MMI.csv
            - s_shakemap_ACM7p3_LeechRiverFullFault_107_MMI.csv
        - 2 Excel spreadsheet files:
            - dsra_attributes_en.xlsx
            - dsra_attributes_fr.xlsx
        - 1 extra CSV file: scenarios.csv
        - 1 extra ZIP file: shakemap_scenario_extents.zip
        - 2 Source code archives (.tar.gz, .zip)
