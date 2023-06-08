# Calculation of Power Plant specific emission factors in Germany
This notebook is a step-by-step guide how power plant specific emission factors can be calculated using generation data and emission data. 

This work is based on the work from: Schubert, Tim Niclas; Avenmarg, Gabriel;  Unnewehr, Jan Frederick;  Schäfer, Mirko, _Generation and emission data for main power plants in Germany (2015 - 2021)_, https://zenodo.org/record/7316186

## Summary
To access the results the notebook isn't needed to run, all output's are already available for download. AS said before, the notebook gives an detailed insight on the processing of the data. 

### Inputs
Inputs used for the calculation are the following Tables/Datasets
__blocks.xlsx__: 
This file collects power plant information per unit from different data sources. The EIC is the identifier used by ENTSO-E for the per unit generation time series [1]. This identifier is matched with BNA-ID and MaStR-Nr used in the official power plant list published by the Bundesnetzagentur [2]. Information about the plant (location, name, block name, electrical and heat power capacity, fuel type) has been collected from the power plant lists from Bundesnetzagentur [2] and Umweltbundesamt [3]. Fuel-specific emission factors have been assigned from data published by Umweltbundesamt [4] according to plant type and location. The ETS ID is the identifier of the installation in the EU emissions trading system. Note that EIC, BNA-ID and MaStR-Nr differ for each generation unit, whereas the ETS ID relates to a stationary installation which might contain several generation unit

__Verified_emissions_2022.xlsx__:
This file is published by the EEA and contains data on all participants of the European Trading System (ETS). Each participant has a unique identifier called _ETS-ID_ and data on Emissions, Allocations for several years. It is used to get the Emissions for each power plant, as well as the free allocations to seperate Heat and Eletricity emissions.

__Processed SMARD Data__:
This Dataset contains hourly generation Data for each generation unit. The Data is published by the Bundesnetzagentur and was preprocessed in an earlier step. This is described in another repository LINK. Each generation unit has an EIC-ID as identifier, which was matched in the preprocessing to an ETS-ID. This had to be done by hand, as there was yet not matching available. In the Dataset, each ETS-ID has one CSV-File per year, containing the hourly generation data for each corresponding Unit. The CSV Files have the format __{Plant Name}/{ETS_ID}/{Year}__.

### Outputs
As outputs we get the following Tables/Plots:
__block-generation.xslx__:
This file contains yearly generation for each generation unit calculated from the per unit generation time series published by ENTSO-E. Generation units are identified by the EIC and the BNA-ID.

__installation-results-yearly__:
This file contains yearly results for generation and emissions for each installation identified by the ETS-ID. Each installation might contain several generation units.

__Plots__:
Plots are generated for:
- emission factors per ETS-ID sorted by increasing emission factors
- emission factors per ETS-ID sorted by fueltype
- utilization ratio per ETS-ID sorted by increasing emission factors
- utilization ratio per ETS-ID sorted by fueltype

## Processing
The processing is done in the following steps:
1. __blocks.xlsx__ file is loaded
1. An ETS and EIC List are generated, containing all unique ETS and EIC IDs in the blocks file
1. The SMARD Data is loaded and for each EIC aggregated to an yearly basis
1. the __Verified Emissions__ are loaded
1. Generation Data is aggregated to Plant Level, by adding all perUnit Generation Data belonging to the same ETS-ID
1. Allocations and Emissions are accessed for each ETS-ID
1. Seperation of Heat and Electricity Emissions by using the Allocations and the following Assumptions. Emissions contain heat and electricity emissions, where the emissions from heat can be calculated with the Heat provided _Ht_ and the Heat benchmark _hb_ with _Eth = Ht * hb_. Then the Heat can be calculated from the Allocations, where we assume the Allocations are calculated as following: _Ai(k)=Hi⋅hb⋅β(k)⋅[σ+(1−σ)γ(k)]_. _Ai(k)_ represents the allocations in the year _2013+k_ and _β(k)_ the linear reduction factor _β(k)=(1−k⋅0,0174)_. _γ(k)=(0.8−(0.5/7)⋅k)_ represents the carbon leakage exposure factor for provisioning to installations exposed to no significant carbon leakage risk. This factor is 1 for heat provisioning to installations which show a significant risk for carbon leakage. The parameter _σ_ denotes the share of heat provided to the latter sector (with risk of carbon leakage), whereas _(1−σ)_ gives the remaining share of heat provided to the sector without significant risk of carbon leakage. The parameter σ has been calculated by comparing the given data for free allocations for consecutive years _Ai(k+1)/Ai(k)_ under the assumption that _Hi(k+1)=Hi(k)_. Values for σ are given in the file "sigma.xlsx". Note that no free allocations are given for heat provided to installations taking part in the EU ETS, so these emissions are not covered by this approach. Not for all installation consistent information for free allocations was available, in these cases no entry in the file "installation-results-yearly.xslx" is given, respectively.
1. Calculation of annual emmision factors
1. Calculation of utiliation ratio by dividing the fuel-specific emission factor by the plant-specific emission factor.
1. Export of data

## Licensing 
Data provided from SMARD is Licensed under Creative Commons Attributional 4.0 International. It is provided by the Bundesnetzagentur under https://www.smard.de/home/datennutzung

EEA standard re-use policy: unless otherwise indicated, re-use of content on the EEA website for commercial or non-commercial purposes is permitted free of charge, provided that the source is acknowledged (http://www.eea.europa.eu/legal/copyright). Copyright holder: Directorate-General for Climate Action (DG-CLIMA).

  