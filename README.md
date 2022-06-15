# g-formula-ELSA
Code by Katherine J. Ford for _Journals of Gerontology: Social Sciences_ doi:10.1093/geronb/gbac075

Please cite this article when using the code: 
Ford, K. J., Kobayashi, L., & Leist, A. K. (2022). Childhood socioeconomic disadvantage and pathways to memory performance in mid to late adulthood: What matters most? Journals of Gerontology: Social Sciences. doi:10.1093/geronb/gbac075

## Funding
This work was supported by the European Research Council (grant agreement no. 803239, 2019–2024, to AKL). KJF’s doctoral training was supported by the Luxembourg National Research Fund (grant number 10949242). LCK’s effort was supported by the National Institute on Aging at the United States National Institutes of Health (grant numbers R01AG070953, R01AG069128, P30AG012846).

## Data availability
This paper uses data from the English Longitudinal Study of Ageing (ELSA). Data are publicly available from the UK Data Service: https://beta.ukdataservice.ac.uk/datacatalogue/series/series?id=200011

## Main Code Details
We use data from ELSA’s Wave 3 main data file, Wave 3 life histories data file, and the Wave 3 derived variables file. The most recent data files available on November 5th, 2020, were downloaded that day. 

### Variable Descriptions
`memtest` Memory score from summing immediate and delayed recall scores

`deprivescale` 
Steps detailed below:

```
replace raroo=. if raroo<0
replace rapeo=. if rapeo<0
gen crowd = rapeo/raroo if rapeo!=. & raroo!=.
gen toocrowd=0  if crowd!=.
replace toocrowd=1 if crowd>2 & crowd!=.
gen fewbks=1 if rabks==1
replace fewbks=0 if rabks>1
gen nofac=0 if  rafac1==1 | rafac2==1 | rafac3==1 | rafac4==1 | rafac5==1
replace nofac=1 if rafac1==0 & rafac2==0 & rafac3==0 & rafac4==0 & rafac5==0
gen unskilldadjob=0 if difjobm==4 | difjobm==3 | difjobm==2
replace unskilldadjob=1 if difjobm>=5 & difjobm!=.
gen deprivescale = unskilldadjob + toocrowd + fewbks + nofac
tab deprivescale
replace deprivescale=3 if deprivescale==4`
```

`qed` Reverse coding of `edqual` after considering missing categories as described in the paper

`occ` Reverse coding of `w3nssec3`

`ace` Derived from `rsabuse`, `rsargue`, `rsdrink`, `rsattac`, `rsattacy`, `rssexas`, `rssexasy`, `ramot`, `diklivm`, `ralis1`, and `ralis2` as described in the paper

`cphealth` Derived from ` rhcia` and ` rhcib` as described in the paper

`depress` Depression from the variable `psceda`

`limcon` Derived from `heill` and `helim` as described in the paper

`cohort` Categories derived from `indobyr` according to historical periods described in the article

`age_c` Age in years and centered at the mean of the eligible sample

`age_csq` Squaring of age centered at the mean of the eligible sample

`male` Gender

### G-Formula code for main result with composite indicator

```
gformula memtest deprivescale qed male age_c age_csq cohort cphealth ace, ///
    mediation outcome(memtest) exposure(deprivescale) mediator(qed) ///
    base_confs(male age_c age_csq cohort) post_confs(cphealth ace) ///
    equations(qed: i.deprivescale male i.cohort cphealth ace, ///
    cphealth: i.deprivescale male i.cohort ace, ///
    ace: i.deprivescale male i.cohort, ///
    memtest: i.deprivescale i.qed male age_c age_csq i.cohort cphealth ace) ///
    commands(qed:ologit, cphealth:logit, ace:logit, memtest:regress) oce baseline(3) seed(515)
```
```
gformula memtest deprivescale qed occ male age_c age_csq cohort cphealth ace, ///
	mediation outcome(memtest) exposure(deprivescale) mediator (qed occ) ///
	base_confs(male age_c age_csq cohort) post_confs(cphealth ace) ///
	equations(qed: i.deprivescale male i.cohort cphealth ace, ///
	occ: i.deprivescale i.qed male i.cohort cphealth ace, ///
	cphealth: i.deprivescale male i.cohort ace, ///
	ace: i.deprivescale male i.cohort, ///
	memtest: i.deprivescale i.qed i.occ male age_c age_csq i.cohort cphealth ace) ///
  commands(qed:ologit, occ:ologit, cphealth:logit, ace:logit, memtest:regress) oce baseline(3) seed(515)
```
```
gformula memtest deprivescale qed occ male age_c age_csq cohort cphealth ace limcon depress , ///
	mediation outcome(memtest) exposure(deprivescale) mediator (qed occ) ///
	base_confs(male age_c age_csq cohort) post_confs(cphealth ace limcon depress) ///
	equations(qed: i.deprivescale male i.cohort cphealth ace, ///
	occ: i.deprivescale i.qed male i.cohort cphealth ace depress limcon, ///
	cphealth: i.deprivescale male i.cohort ace, ///
	ace: i.deprivescale male i.cohort, ///
	limcon: i.deprivescale i.qed male age_c i.cohort cphealth ace, ///
	depress: i.deprivescale i.qed male age_c i.cohort cphealth ace limcon, ///
	memtest: i.deprivescale ' i.qed i.occ male age_c age_csq i.cohort cphealth ace depress limcon) ///
  commands(qed:ologit, occ:ologit, cphealth:logit, ace:logit, limcon:logit, depress:logit, memtest:regress) oce baseline(3) seed(515)`  
```

For more `gformula` code instructions with Stata, see the following article: Daniel, R.M., De Stavola, B.L., and Cousens, S.N. (2011). gformula: Estimating causal effects in the presence of time-varying confounding or mediation using the g-computation formula. *The Stata Journal* 11(4):479–517.

