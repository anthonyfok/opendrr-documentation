# 1. Design a new DSRA scenario and run OpenQuake

This is usually done by our Seismic Risk Research Scientist [Tiegan E. Hobbs](http://tieganhobbs.com/) and helpers (e.g. Earthquake Scenarios Analyst [Jeremy Rimando](https://www.jeremyrimando.com/)).

1. Design new <abbr title="Deterministic Seismic Hazard Assessment">DSRA</abbr> scenarios.

2. Run OpenQuake (and related scripts) to generate scenario Hazard, Damage, Consequence, and Risk outputs using the Canadian datasets and functions produced by NRCan and partners.

    This is done by running [scripts/run_OQStandard.sh](https://github.com/OpenDRR/earthquake-scenarios/blob/master/scripts/run_OQStandard.sh) which calls OpenQuake (currently v3.11) and other post-processing Python scripts (consequences?  weighted average?) to complete calculations resulting in HDF5 DataStores (in oqdata) and CSV files.

    This can be run on any compatible machine, whether your local computer (e.g. personal MacBook, physical Linux installation, Linux in WSL2, Linux in VirtualBox, etc.) or in an AWS EC2 instance.

3. Upload (`git push`) the results to the [FINISHED](https://github.com/OpenDRR/earthquake-scenarios/tree/master/FINISHED) directory in the OpenDRR/earthquake-scenarios repository.

Please refer to [DraftOF_RunningOQCanada.pdf](https://github.com/OpenDRR/earthquake-scenarios/blob/master/DraftOF_RunningOQCanada.pdf) (Hobbs, T.E., Journeay, J.M., Rotheram, D., 2022?. A Mini Manual for Running OpenQuake Canada, Draft Open File) for more information.  The following table is extracted from page 5 of that manual, with minor modifications.

### Table 1: An example set of the files used to run OpenQuake.

| Repository| [OpenDRR/earthquake-scenarios](https://github.com/OpenDRR/earthquake-scenarios) _(public)_ |
| --- | --- |
| Run Script | [scripts/run_OQStandard.sh](https://github.com/OpenDRR/earthquake-scenarios/blob/master/scripts/run_OQStandard.sh) |
| Hazard Initialization | [initializations/s_Hazard_SIM9p0_CascadiaInterfaceBestFault.ini](https://github.com/OpenDRR/earthquake-scenarios/blob/master/initializations/s_Hazard_SIM9p0_CascadiaInterfaceBestFault.ini) |
| Damage Initialization | [initializations/s_Damage_SIM9p0_CascadiaInterfaceBestFault_b0_b.ini](https://github.com/OpenDRR/earthquake-scenarios/blob/master/initializations/s_Damage_SIM9p0_CascadiaInterfaceBestFault_b0_b.ini)<br>[initializations/s_Damage_SIM9p0_CascadiaInterfaceBestFault_r1_b.ini](https://github.com/OpenDRR/earthquake-scenarios/blob/master/initializations/s_Damage_SIM9p0_CascadiaInterfaceBestFault_r1_b.ini)
| Risk Initialization | [initializations/s_Risk_SIM9p0_CascadiaInterfaceBestFault_b0_b.ini](https://github.com/OpenDRR/earthquake-scenarios/blob/master/initializations/s_Risk_SIM9p0_CascadiaInterfaceBestFault_b0_b.ini)<br>[initializations/s_Risk_SIM9p0_CascadiaInterfaceBestFault_r1_b.ini](https://github.com/OpenDRR/earthquake-scenarios/blob/master/initializations/s_Risk_SIM9p0_CascadiaInterfaceBestFault_r1_b.ini) |
| Rupture | [ruptures/rupture_SIM9p0_CascadiaInterfaceBestFault.xml](https://github.com/OpenDRR/earthquake-scenarios/blob/master/ruptures/rupture_SIM9p0_CascadiaInterfaceBestFault.xml) |
| Consequence | [scripts/consequences-v3.10.0.py](https://github.com/OpenDRR/earthquake-scenarios/blob/master/scripts/consequences-v3.10.0.py)<br>[scripts/Hazus_Consequence_Parameters.xlsx](https://github.com/OpenDRR/earthquake-scenarios/blob/master/scripts/Hazus_Consequence_Parameters.xlsx) |

| Repository | [OpenDRR/openquake-inputs](https://github.com/OpenDRR/openquake-inputs) _(public)_ |
| --- | --- |
| Sites | [earthquake/sites/regions/site-vgrid_BC.csv](https://github.com/OpenDRR/openquake-inputs/blob/main/earthquake/sites/regions/site-vgrid_BC.csv) |
| Fragility | [earthquake/vulnerability/structural_fragility_CAN.xml](https://github.com/OpenDRR/openquake-inputs/blob/main/earthquake/vulnerability/structural_fragility_CAN.xml) |
| Vulnerability | [earthquake/vulnerability/vulnerability_contents_CAN.xml](https://github.com/OpenDRR/openquake-inputs/blob/main/earthquake/vulnerability/vulnerability_contents_CAN.xml)<br>[earthquake/vulnerability/vulnerability_nonstructural_CAN.xml](https://github.com/OpenDRR/openquake-inputs/blob/main/earthquake/vulnerability/vulnerability_nonstructural_CAN.xml)<br>[earthquake/vulnerability/vulnerability_occupants_CAN.xml](https://github.com/OpenDRR/openquake-inputs/blob/main/earthquake/vulnerability/vulnerability_occupants_CAN.xml)<br>[earthquake/vulnerability/vulnerability_structural_CAN.xml](https://github.com/OpenDRR/openquake-inputs/blob/main/earthquake/vulnerability/vulnerability_structural_CAN.xml) |
| Mapping | [earthquake/vulnerability/CanSRM1_TaxMap_b0.csv](earthquake/vulnerability/CanSRM1_TaxMap_b0.csv)<br>[earthquake/vulnerability/CanSRM1_TaxMap_r1.csv](earthquake/vulnerability/CanSRM1_TaxMap_b0.csv) |
| Exposure | [exposure/general-building-stock/oqBldgExp_BC.xml](https://github.com/OpenDRR/openquake-inputs/blob/main/exposure/general-building-stock/oqBldgExp_BC.xml)<br>[exposure/general-building-stock/oqBldgExp_BC.csv](https://github.com/OpenDRR/openquake-inputs/blob/main/exposure/general-building-stock/oqBldgExp_BC.csv) |

| Repository | [OpenDRR/CanadaSHM6](https://github.com/OpenDRR/CanadaSHM6) _(currently in private trial)_ |
| --- | --- |
| GPME | OpenQuake_model_files/gmms/LogicTree/OQ_classes_NGASa0p3weights_interface.xml |
