# 2. Fix-up and create PR for new DSRA scenarios (e.g. by Jeremy)

## Background

- Anthony simply duplicated Jeremy Rimando Google Drive structure and made no attempt to make it fit OpenDRR/earthquake-scenarios
- Damon created OpenDRR/DSRA-processing to automate the fix-up and addition of these scenarios to OpenDRR/earthquake-scenarios
- Tiegan checked the validity of each scenarios, and determined that 17 of the scenarios are good for publishing right now.
- 4 of the scenarios were added in October (?) 2022; 3 of which are subsequently renamed (awaiting PR review in late March 2023):
    1. ~~ACM7p4_BurwashLanding~~ ACM7p4_DenaliFault (No. 3)
    2. ~~ACM4p9_Capilano5~~ ACM4p9_GeorgiaStraitFault (No. 4)
    3. SCM5p0_Montreal (No. 7)
    4. ~~SCM5p5_Ottawa~~ SCM5p5_ConstanceBay (No. 9)
- 13 are being added (March – April 2023), see https://github.com/OpenDRR/earthquake-scenarios/issues/74#issuecomment-1410705741


* https://github.com/OpenDRR/DSRA-processing/blob/main/README.md

> ## DSRA-processing
>
> For processing of new scenarios before moving them to earthquake-scenarios repo
>
> Steps:
>
> 1. Create new branch - use format "scenarios-Y.M.D.Time", ex. scenarios-2022.09.13.1126
>
> 2. Add scenario files to their respective directories:
>     - 7 files (.csv or .xz) per scenario to /FINISHED: s_consequnces b0 and r1, s_dmgbyasset b0 and r1, s_lossesbyasset b0 and r1, and s_shakemap
>     - 5 files (.ini) per scenario to /initializations: s_Damage b0 and r1, s_Hazard, s_Risk b0 and r1
>     - One rupture_ .xml file per scenario to /ruptures
>
> 3. Delete the README.md files in those three directories.
>
> 4. Push new branch, github action will process the files, and open PR to merge new scenarios in earthquake-scenarios repo.
>
> 5. Branch can then be used for stack build to generate data for added scenarios.

### What Anthony learned from Damon’s observations and insights:

- "One rupture_ .xml file per scenario to /ruptures, so that's why Tiegan uses the rupture file name (and not the initialization files) to refer to certain events!

### Brainstorming: How Anthony deviates from Damon’s procedure (or to elaborate on the procedure)

- Instead of scenarios-Y.M.D.Time, I tend to use what I call the "codename" of the event.
- Try to create a branch for each scenario (like Damon does), but will see if I can "stack" (rebase) them on top of one another to create one big PR
- If there are any large files, may need to commit them over several commits... (maybe not needed here because we are using Git LFS?) to be confirmed
- TODO

### How did Damon do it for the ~~6~~ 5 new scenarios added in October 2022?

Summary:

> @DamonU2 added five new scenarios in PR OpenDRR/earthquake-scenarios#69 and removed "Duncan" in OpenDRR/earthquake-scenarios#70 circa October 6 - 13, 2022.

Details (Retracing):

At OpenDRR/earthquake-scenarios:

* Pull Request #63 (2022-09-15) was automatically created by .github/workflows/process_scenarios.yml, apparently triggered by
  ```
  commit eadaad562a3b7000d9abb9a22ab28ce5bdd62c99
  Author: Damon Ulmi <damon_ulmi@hotmail.com>
  Date:   Thu Sep 15 11:14:36 2022 -0700

  Removed __pycache__, and added it to .gitignore
  ```
  Damon closed the unintended pull request on the same day.

* Pull Requests #64 to #68 (2022-09-28) were also automatically created by .github/workflows/process_scenarios.yml, triggered by Anthony’s push to these 5 branches:

  - ACM5p3_Duncan
  - ACM7p4_BurwashLanding
  - IDM5p0_Masset
  - SCM5p0_Montreal
  - SCM5p5_Ottawa

  Anthony closed these pull requests on the same day.

* Pull Request #69 is Da Man!  [Added five new scenarios and updated documentation by DamonU2 · Pull Request #69 · OpenDRR/earthquake-scenarios](https://github.com/OpenDRR/earthquake-scenarios/pull/69).  In particular, these two commits:
  - [Added six new scenarios, and updated documentation (commit 18e1fba)](https://github.com/OpenDRR/earthquake-scenarios/pull/69/commits/18e1fbac0b4682697b4671c0d90582de95e138af)
  - [Added five new scenarios and updated documentation by DamonU2 · Pull Request #69 · OpenDRR/earthquake-scenarios](https://github.com/OpenDRR/earthquake-scenarios/pull/69/commits/e696271de6a029e96b73583d01b4cccf14d5e114)

* And then there was the subsequent [Remove duncan by DamonU2 · Pull Request #70 · OpenDRR/earthquake-scenarios](https://github.com/OpenDRR/earthquake-scenarios/pull/70) too.

### How is Anthony doing it for the 13 new scenarios

(Note: It is a bit manual and ad hoc (?), but we may be able to streamline/automate the process in the future.)

1. In the ~/NRCan/OpenDRR/earthquake-scenarios-2022-jrimando-google-drive directory, run csv-to-yaml.py (which was created by telling ChatGPT to “Write a Python script that converts CSV with rows containing specific value to YAML using ruamel.yaml” and making minor fixes and modifications:

    ```python
    #!/usr/bin/python3

    import csv
    import ruamel.yaml

    # Open the CSV file
    with open('ScenarioReview_TH_Oct2022-2023-02-22.csv', newline='') as csvfile:
        # Read the CSV data into a list of dictionaries
        reader = csv.DictReader(csvfile)

        # Filter out already committed scenarios and non-approved scenarios
        data = []
        for row in reader:
            if row['Scenario Num'] not in ['3', '4', '7', '9', '']:
                data.append(row)

    # Convert the data to YAML format using ruamel.yaml
    yaml = ruamel.yaml.YAML()
    yaml.indent(mapping=2, sequence=4, offset=2)

    # Write the YAML data to a file
    with open('output.yaml', 'w') as yamlfile:
        yaml.dump(data, yamlfile)
    ```
    Then, do some quick sanity checks:

    ```console
    $ grep EVENT: output.yaml
      - EVENT: rupture_ACM5p2_Abbotsford_syst.xml
      - EVENT: rupture_ACM5p0_Burnaby_syst.xml
      - EVENT: rupture_ACM5p7_Ladysmith_syst.xml
      - EVENT: rupture_ACM4p9_MatsquiMain2_syst.xml
      - EVENT: rupture_SCM5p9_Montreal_syst.xml
      - EVENT: rupture_SCM5p6_Ottawa_syst.xml
      - EVENT: rupture_ACM5p2_PortAlberni_syst.xml
      - EVENT: rupture_ACM7p7_QueenCharlotte_syst.xml
      - EVENT: rupture_ACM5p0_Richmond_syst.xml
      - EVENT: rupture_ACM8p0_SkeenaQueenCharlotteE_syst.xml
      - EVENT: rupture_SCM5p0_Toronto_BTSZ_syst.xml
      - EVENT: rupture_SCM5p0_Toronto_CMMBZ_syst.xml
      - EVENT: rupture_ACM5p5_Tsussie6_syst.xml
    $ grep EVENT: output.yaml | wc
         13      39     607
    ```

Get the actual “old” codenames named by Jeremy:

```console
$ grep EVENT: output.yaml | cut -f2- -d_ | sed 's/_syst.xml$//' > 13-scenarios-jeremy-codename.txt
$ cat 13-scenarios-jeremy-codename.txt
ACM5p2_Abbotsford
ACM5p0_Burnaby
ACM5p7_Ladysmith
ACM4p9_MatsquiMain2
SCM5p9_Montreal
SCM5p6_Ottawa
ACM5p2_PortAlberni
ACM7p7_QueenCharlotte
ACM5p0_Richmond
ACM8p0_SkeenaQueenCharlotteE
SCM5p0_Toronto_BTSZ
SCM5p0_Toronto_CMMBZ
ACM5p5_Tsussie6
```

More checks: To see if we have an identical number of files related to each scenario:

```
$ for i in $(cat 13-scenarios-jeremy-codename.txt); do printf "%-28s" "$i"; find . -name "*${i}*" -type f | wc; done
ACM5p2_Abbotsford                19      19    2197
ACM5p0_Burnaby                   19      19    2050
ACM5p7_Ladysmith                 19      19    2142
ACM4p9_MatsquiMain2              19      79    2385
SCM5p9_Montreal                  19      19    2088
SCM5p6_Ottawa                    19      19    1995
ACM5p2_PortAlberni               19      37    2279
ACM7p7_QueenCharlotte            19      37    2429
ACM5p0_Richmond                  19      19    2096
ACM8p0_SkeenaQueenCharlotteE     19      91    2898
SCM5p0_Toronto_BTSZ              19      19    2227
SCM5p0_Toronto_CMMBZ             19      19    2272
ACM5p5_Tsussie6                  19      61    2147
```

Check

```
$ find . -name '*ACM5p2_Abbotsford*' -type f
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_sitemesh_ACM5p2_Abbotsford_syst_1.csv.xz
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_gmfdata_ACM5p2_Abbotsford_syst_1.csv.xz
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_lossesbyasset_ACM5p2_Abbotsford_syst_r1_5_b.csv.xz
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_Damage_ACM5p2_Abbotsford_syst_b0r1_b.log
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_dmgbyasset_ACM5p2_Abbotsford_syst_r1_3_b.csv.xz
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_consequences_ACM5p2_Abbotsford_syst_b0_2_b.csv.xz
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_Risk_ACM5p2_Abbotsford_syst_b0r1_b.log
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_Hazard_ACM5p2_Abbotsford_syst.log
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_consequences_ACM5p2_Abbotsford_syst_r1_3_b.csv.xz
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_shakemap_ACM5p2_Abbotsford_syst_1.csv.xz
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_dmgbyasset_ACM5p2_Abbotsford_syst_b0_2_b.csv.xz
./outputs/Systematic/Abbotsford-Systematic/ACM5p2_Abbotsford_syst/s_lossesbyasset_ACM5p2_Abbotsford_syst_b0_4_b.csv.xz
./initializations/Systematic/rupture_ACM5p2_Abbotsford_syst.csv
./initializations/Systematic/Abbotsford_ScenarioIni_Files_Systematic/ACM5p2_Abbotsford_syst/s_Damage_ACM5p2_Abbotsford_syst_b0_b.ini
./initializations/Systematic/Abbotsford_ScenarioIni_Files_Systematic/ACM5p2_Abbotsford_syst/s_Risk_ACM5p2_Abbotsford_syst_b0_b.ini
./initializations/Systematic/Abbotsford_ScenarioIni_Files_Systematic/ACM5p2_Abbotsford_syst/rupture_ACM5p2_Abbotsford_syst.xml
./initializations/Systematic/Abbotsford_ScenarioIni_Files_Systematic/ACM5p2_Abbotsford_syst/s_Risk_ACM5p2_Abbotsford_syst_r1_b.ini
./initializations/Systematic/Abbotsford_ScenarioIni_Files_Systematic/ACM5p2_Abbotsford_syst/s_Damage_ACM5p2_Abbotsford_syst_r1_b.ini
./initializations/Systematic/Abbotsford_ScenarioIni_Files_Systematic/ACM5p2_Abbotsford_syst/s_Hazard_ACM5p2_Abbotsford_syst.ini
```

Copy the content of 13-scenarios-jeremy-codename.txt into the ~~SCENARIOS_JEREMY_CODENAME~~ `SCENARIOS` array definition in ~/NRCan/OpenDRR/DSRA-processing/copy-new-scenarios-from-jrimando-repo.sh.

Disable the git commit and push stuff.  We’ll be doing that manually.

After fixing up copy-new-scenarios-from-jrimando-repo.sh to my satisfaction, I ran it, and it copied the files for all 13 scenarios.

Thanks to Damon’s helpful tips in README.md, we know that we should have:

- 7 × 13 = 91 files in FINISHED/ directory
- 5 × 13 = 65 files in initializations/ directory
- 1 × 13 = 13 files in ruptures/ directory

I then run:

```sh
xz -dv FINISHED/*.xz
```

to decompress the *.csv.xz files to *.csv.


### Prepare for commits

Create our new branch `add-13-scenarios-apr2023` on top of the `rename-and-organize` branch which is awaiting review and approval.

```sh
git switch rename-and-organize
git switch -c add-13-new-scenarios
```

Move everything over to OpenDRR/earthquake-scenarios:

```bash
cd ../DSRA-processing
mv -vi FINISHED/*.csv ../earthquake-scenarios/FINISHED/
mv -vi initializations/*.ini ../earthquake-scenarios/initiailizations/
mv -vi ruptures/*.xml ../earthquake-scenarios/ruptures/
```

Back at OpenDRR/earthquake-scenarios, check if the files are copied correctly:

> (7 + 5 + 1) files/scenario × 13 scenarios  
> = 13 files/scenario × 13 scenarios  
> = 169 files

```
$ git status | egrep 'FINISHED|initializations|ruptures' | wc
    171     171    9735
(I had two other unrelated files lying around)
```

#### git add

```sh
git add FINISHED/*.csv initializations/*.ini ruptures/*.xml
```

This will take some time (almost 7 minutes on my computer over NFS) due to git-lfs copying the CSV files into .git/lfs/objects.  The directory will grow quite a bit in size, 13GiB perhaps?

Check number of files again:

```
$ git status | grep "new files:" | wc
    169     507   11714
```

#### git commit

Prepare for commit message:

```
ACM5p2_Abbotsford            → ACM5p2_VedderFault
ACM5p0_Burnaby               → ACM5p0_MysteryLake
ACM5p7_Ladysmith             → ACM5p7_SoutheyPoint
ACM4p9_MatsquiMain2          → ACM4p9_VedderFault
SCM5p9_Montreal              → SCM5p9_MillesIlesFault
SCM5p6_Ottawa                → SCM5p6_GloucesterFault
ACM5p2_PortAlberni           → ACM5p2_BeaufortFault
ACM7p7_QueenCharlotte        → ACM7p7_QueenCharlotteFault
ACM5p0_Richmond              → ACM5p0_GeorgiaStraitFault
ACM8p0_SkeenaQueenCharlotteE → ACM8p0_QueenCharlotteFault
SCM5p0_Toronto_BTSZ          → SCM5p0_BurlingtonTorontoStructuralZone
SCM5p0_Toronto_CMMBZ         → SCM5p0_RougeBeach
ACM5p5_Tsussie6              → ACM5p5_SoutheyPoint
```

#### Actual rename

(very ugly renaming by Anthony (me); should automate more but too sleepy-head to do so...)

```bash
sed -E 's/ACM5p2_Abbotsford(_syst)?/ACM5p2_VedderFault/;
        s/ACM5p0_Burnaby(_syst)?/ACM5p0_MysteryLake/;
        s/ACM5p7_Ladysmith(_syst)?/ACM5p7_SoutheyPoint/;
        s/ACM4p9_MatsquiMain2(_syst)?/ACM4p9_VedderFault/;
        s/SCM5p9_Montreal(_syst)?/SCM5p9_MillesIlesFault/;
        s/SCM5p6_Ottawa(_syst)?/SCM5p6_GloucesterFault/;
        s/ACM5p2_PortAlberni(_syst)?/ACM5p2_BeaufortFault/;
        s/ACM7p7_QueenCharlotte(_syst)?/ACM7p7_QueenCharlotteFault/;
        s/ACM5p0_Richmond(_syst)?/ACM5p0_GeorgiaStraitFault/;
        s/ACM8p0_SkeenaQueenCharlotteE(_syst)?/ACM8p0_QueenCharlotteFault/;
        s/SCM5p0_Toronto_BTSZ(_syst)?/SCM5p0_BurlingtonTorontoStructuralZone/;
        s/SCM5p0_Toronto_CMMBZ(_syst)?/SCM5p0_RougeBeach/;
        s/ACM5p5_Tsussie6(_syst)?/ACM5p5_SoutheyPoint/;' \
     -i initializations/*_ACM5p2_Abbotsford_*.ini \
        initializations/*_ACM5p0_Burnaby_*.ini \
        initializations/*_ACM5p7_Ladysmith_*.ini \
        initializations/*_ACM4p9_MatsquiMain2_*.ini \
        initializations/*_SCM5p9_Montreal_*.ini \
        initializations/*_SCM5p6_Ottawa_*.ini \
        initializations/*_ACM5p2_PortAlberni_*.ini \
        initializations/*_ACM7p7_QueenCharlotte_*.ini \
        initializations/*_ACM5p0_Richmond_*.ini \
        initializations/*_ACM8p0_SkeenaQueenCharlotteE_*.ini \
        initializations/*_SCM5p0_Toronto_BTSZ_*.ini
```

Copied name_change.py over to OpenDRR/earthquake-scenarios temporarily and ran:

```sh
./name_change.py ACM5p2_Abbotsford      ACM5p2_VedderFault
./name_change.py ACM5p0_Burnaby ACM5p0_MysteryLake
./name_change.py ACM5p7_Ladysmith       ACM5p7_SoutheyPoint
./name_change.py ACM4p9_MatsquiMain2    ACM4p9_VedderFault
./name_change.py SCM5p9_Montreal        SCM5p9_MillesIlesFault
./name_change.py SCM5p6_Ottawa  SCM5p6_GloucesterFault
./name_change.py ACM5p2_PortAlberni     ACM5p2_BeaufortFault
./name_change.py ACM7p7_QueenCharlotte  ACM7p7_QueenCharlotteFault
./name_change.py ACM5p0_Richmond        ACM5p0_GeorgiaStraitFault
./name_change.py ACM8p0_SkeenaQueenCharlotteE   ACM8p0_QueenCharlotteFault
./name_change.py SCM5p0_Toronto_BTSZ    SCM5p0_BurlingtonTorontoStructuralZone
./name_change.py SCM5p0_Toronto_CMMBZ   SCM5p0_RougeBeach
./name_change.py ACM5p5_Tsussie6        ACM5p5_SoutheyPoint
```

Probably better if I changed name_change.py to run `git mv` directly, but it is easily remedied by running `git add` to let `git` know of the deleted and added files, which `git` will figure out to be renames:

```sh
git add FINISHED/*.csv initializations/*.ini ruptures/*.xml
```
(wrong command, shouldn’t have used wildcards, and may need to add `-A` for `--no-ignore-removal` too, see below)

This took a while again as git-lfs is likely verifying all the large CSV files.  Fortunately, as the contents of the CSV files are unchanged, no extra disk space is needed.

Sorry, the command above was not entirely correctly; the deleted files were not accounted for.  Fixed with:

```sh
git add -A FINISHED initializations ruptures
```

I personally had to run the following command to unstage a random file that I had lying around:

```sh
git restore --staged FINISHED/geopackages/list.txt
```

Check number of changed files (should be 13×13=169):

```console
$ git status | egrep ':    (FINISHED|initializations|ruptures)/' | wc
    169     676   22492
```

```console
$ du -csh .git/lfs/objects/
31G	.git/lfs/objects/
31G	total
```

Sanity check:

```sh
ls -l ruptures/rupture_ACM5p0_GeorgiaStraitFault.xml
```

Uh oh, file not found!  Turns out there is instead ruptures/rupture_ACM5p0_GeorgiaStraitFault_syst.xml... (uh oh...)
I forgot to remove _syst from the path (because I either forgot or decided not to use process_scenarios.py).

Quick-and-dirty fix:

```sh
for i in $(find ruptures/ FINISHED/ initializations/ -name '*_syst*'); do
    git mv "$i" "${i/_syst/}"
done
```
which also took 7 minutes over NFS on my computer.



Next, we run process_scenarios.py (written by Damon).  This script:

1. Decompresses FINISHED/*.csv.xz files (if exists; I did it manually with `xz -dv FINISHED/*.xz`)
2. Cleans up file names:
    1. Removes `_syst` from file names.
    2. (For the next step) Populates `scenarios` array
3. Run “snapshot” on each scenario, see utilities/snapshot.py



```sh
yq -r '.[] | ( .EVENT | sub("rupture_"; "") | sub("_syst.xml"; "") ) + "\t" + ."Alternate Names"' output.yaml
```

gives:

```
ACM5p2_Abbotsford	ACM5p2_VedderFault
ACM5p0_Burnaby	ACM5p0_MysteryLake
ACM5p7_Ladysmith	ACM5p7_SoutheyPoint
ACM4p9_MatsquiMain2	ACM4p9_VedderFault
SCM5p9_Montreal	SCM5p9_MillesIlesFault
SCM5p6_Ottawa	SCM5p6_GloucesterFault
ACM5p2_PortAlberni	ACM5p2_BeaufortFault
ACM7p7_QueenCharlotte	ACM7p7_QueenCharlotteFault
ACM5p0_Richmond	ACM5p0_GeorgiaStraitFault
ACM8p0_SkeenaQueenCharlotteE	ACM8p0_QueenCharlotteFault
SCM5p0_Toronto_BTSZ	SCM5p0_BurlingtonTorontoStructuralZone
SCM5p0_Toronto_CMMBZ	SCM5p0_RougeBeach
ACM5p5_Tsussie6	ACM5p5_SoutheyPoint
```
