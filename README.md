# VaccineTrials_analysis
# What Separates Terminated from Completed Phase 3 Vaccine Trials?

**An exploratory registry analysis with three case studies**
Anosh Haral | October 2026 | Independent project (not clinical or employment experience)
Code and outputs: [vaccine_trails_analysis.ipynb]

---

## Question

In ClinicalTrials.gov records of Phase 3 vaccine trials, how do terminated trials differ from completed ones in design complexity (sites, eligibility length, outcomes), and what should a study coordinator prepare for in three real trials?

## Methods

- **Source:** ClinicalTrials.gov API v2, pulled in October 2026 using Python (requests, pandas, SciPy). Registry records change over time, so a new pull returns slightly different numbers.
- **Filters:** intervention term "vaccine"; phase listed as Phase 3; overall status Completed or Terminated. Every record the filters returned was kept (no sampling).
- **Case studies:** NCT07860216 (RSV vaccine, BeneWill, recruiting), NCT00505453 (Fluviral, GSK, completed), and NCT05973006 (Novavax adolescent COVID-19 vaccine, completed). They were chosen for differences in sponsor, era, and population, not because they are representative. They were **excluded from group comparisons**. The RSV trial is recruiting, so it is a case study only.
- **Dataset:** 1,626 records: 1,559 completed and 64 terminated comparison trials, plus 3 case studies.
- **Variables:** enrollment, number of sites and countries, estimated inclusion and exclusion item counts, eligibility word count, number of primary and secondary outcomes, start year, and stated reason for termination.
- **Cleaning:** non-numeric enrollment set to missing (10 records). Start year, read from the registry's start date, was missing for 6 records and left missing. Nothing was imputed.
- **Statistics:** two-sided Mann-Whitney U tests with rank-biserial effect size (r; negative means terminated trials were higher). Four exploratory tests, so a stricter threshold of p < 0.0125 was used. A sensitivity analysis repeated the tests on trials starting in 2010 or later.

## Findings

**Table 1. Completed vs. terminated trials (case studies excluded)**

| Variable | Sample | Median completed | Median terminated | Effect size r | p | Passes p < 0.0125 |
|---|---|---|---|---|---|---|
| Eligibility words | All years (n = 1,559 / 64) | 326 | 492 | -0.26 | <0.001 | Yes |
| Eligibility words | 2010+ (n = 956 / 43) | 377 | 600 | -0.40 | <0.001 | Yes |
| Sites listed | All years | 4 | 11.5 | -0.24 | <0.001 | Yes |
| Sites listed | 2010+ | 5 | 16 | -0.29 | 0.001 | Yes |
| Primary outcomes | All years | 2 | 1 | 0.17 | 0.017 | No |
| Primary outcomes | 2010+ | 2 | 1 | 0.16 | 0.061 | No |
| Exclusion items (script-estimated) | All years | 12 | 13 | -0.04 | 0.557 | No |
| Exclusion items (script-estimated) | 2010+ | 13 | 15 | -0.13 | 0.135 | No |

1. **Terminated trials had longer eligibility text** (median 492 vs. 326 words), and the difference was larger in trials starting in 2010 or later (600 vs. 377 words).
2. **Terminated trials listed more sites** (median 11.5 vs. 4), and the difference held in the 2010+ subset (16 vs. 5).
3. **Primary outcome counts and exclusion item counts did not differ** after correcting for four tests.
4. **Terminated trials started later** (median start year 2016 vs. 2012), so era is a possible confounder. Restricting to 2010 or later did not remove the eligibility or site differences.
5. **Stated reasons for termination.** A keyword-based coding of the 64 stated reasons suggested low enrollment (19) and funding or sponsor decisions (11) were most common, followed by safety (9). Sixteen were uncategorized or not stated. This coding was not hand-reviewed and is indicative only.

**Table 2. Case studies compared with group medians**

| | Fluviral (GSK) | Novavax adolescent | RSV (BeneWill) |
|---|---|---|---|
| NCT ID | NCT00505453 | NCT05973006 | NCT07860216 |
| Status | Completed | Completed | Recruiting |
| Enrollment (type) | 110 (estimated) | 400 (actual) | 25,000 (estimated) |
| Sites / countries | 2 / 1 | 20 / 1 | 7 / 1 |
| Inclusion / exclusion items (hand-verified) | 2 / 9 | 6 / 18 | 5 / 17 |
| Eligibility words | 116 | 645 | 858 |
| Primary / secondary outcomes | 1 / 3 | 5 / 5 | 1 / 15 |

The Novavax trial is above both group medians for sites (20), eligibility length (645 words), and primary outcomes (5). The RSV trial has the longest eligibility text (858 words), 15 secondary outcomes, and a target of 25,000 participants. The 2007 GSK trial is much simpler (116 words, 2 sites), and its enrollment figure is still labeled an estimate, so its record has not been updated to an actual count.

## Recommendations for a study coordinator

1. **Test recruitment feasibility before activation.** Keyword coding of stated termination reasons suggests low enrollment was the most common (19 of 64, not hand-reviewed). Before a trial opens, a coordinator can estimate how many local patients meet the criteria and keep a pre-screening log. This matters most for a trial like the RSV study, which targets 25,000 participants.
2. **Turn long eligibility criteria into a screening checklist.** Terminated trials had longer eligibility text in both analyses. This is an association, not proof of cause, but long criteria plausibly make screening slower and error-prone. A coordinator can flag unclear criteria to the sponsor or IRB early and standardize how each one is checked and documented.
3. **Standardize multi-site processes and watch site performance early.** Terminated trials listed more sites in both analyses, and the Novavax trial lists 20. A coordinator at a site in a large network benefits from shared screening procedures, training, documentation templates, and early tracking of enrollment against target, so an underperforming site is caught in weeks, not months.

## Validation

Inclusion and exclusion item counts come from a script that counts bulleted lines. I hand-counted 5 trials from a random validation sample and the 3 case studies, using one rule (top-level list items only). For the 5 random trials, inclusion counts matched in 4 and exclusion counts in 3, and both matched exactly in 3 of 5. The mismatches were trials whose criteria contain nested sub-items, where the script overcounts (for example, 46 vs. 32 items). All 3 case studies matched exactly. Because of this limitation, item counts are reported descriptively and are not used for the main conclusions. Eligibility word count and site count are direct counts from the registry record.

## Limitations

- The data come from one registry, and termination reasons are often missing or brief.
- Terminated trials started later, and newer records tend to list more sites and longer criteria. The 2010+ check reduces this concern but cannot remove it. Site count also reflects how completely a record lists locations, not only trial size.
- Enrollment was not compared between groups. Terminated trials report the number enrolled when they stopped, and a small share of records use estimated rather than actual counts.
- The criteria counter was validated on a small sample (5 random trials) and overcounts nested lists. Site, enrollment, and outcome counts come directly from registry data and were not separately hand-verified. Termination reasons are keyword-coded and not hand-reviewed.
- Results are associations, not causes. Four tests were run, so findings are exploratory. The three case studies illustrate, and do not represent, vaccine trials generally.
