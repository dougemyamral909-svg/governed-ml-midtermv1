# BANA 690A Midterm: Written Answers

**Name: Dougem Yamral
**Last-name group (A to H, I to P, Q to Z): Q to Z
**GitHub username: dougemyamral909-svg


This file is half of your exam. The code and the pipeline grade the other half.
Write in full sentences. Cite evidence from THIS repository: a file name, a line,
a number from `artifacts/gate_report.json` or `artifacts/model_card.md`.
Open book. Individual. Keep each answer short and specific.

---

## Lab 1: Make the data compliant

**1.1** Which four tests failed when you first ran `python -m pytest -q tests/test_lab1_compliance.py`? For each, name the control type (list or detective).

The 4 tests that failed were test_no_zip_like_column, test_no_date_column, test_no_suspiciiously_predictive_column, and test_policy-agrees_with_data_dictionary column. They're all detective controls. 


**1.2** What did you add to `governance/policy.json`, and under which keys?

I added zip and date_of_birth under pii_columns and I added adjsuter_fraud_review under the leakage_columns. 


**1.3** Why is the fix in the policy and not in `src/prepare.py`? One or two sentences.

The code enforces the policy and since the policy is already incomplete, then we had to fix it.
---

## Lab 2: Build features from driving history

Record every feature you implemented. Four minimum, four families minimum, including both families assigned to your last-name group.

| Feature name | Family | Formula in one sentence | Why it should predict claim_free |
|---|---|---|---|
| claims_per_month | frequency | | |
| | | | |
| | | | |
| | | | |
| | | | |

Claims_per_month | Frequency | It adds up each driver's claims over the 24months/ by 24 | Drivers with more past claims might be more likely to get more

months_since_last_claim | Recency | How many motnhs have passed since the last claim | A gap period from the last claim could mean less of a chance of getting more claims


miles_volatility| Volatility | Compares how much a driver's monthly mileage changes compared to their average mileage | A big change in mileage could signal a change in claim risk

max_hard_brakes_per_1k | Extremes | It finds the month where the driver had the most hard brakes per 1000 miles | The more hard brake occurrences can likely lead to more accidents

night_share_mean | Ratio | It gets the averae percentage of how many miles the driver drivers during thr night | More night time miles being driven can lead to more accidents


**2.1** Explain the temporal fence test in your own words. What would have happened to your model in production if a feature had failed it?

The temporal fence test checks that the features only use information available to them before the application. If a feature failed, the model would be using future information it wouldn’t have in production. 


---

## Lab 3: Hunt the leakage

Three suspect columns. For each, give your ruling and the evidence.

| Column | Ruling (leakage or allowed) | Correlation with label | When recorded | Reasoning |
|---|---|---|---|---|


| prior_claims_3y | allowed | 0.243 | At application | We already know their past claims when they apply |
| renewal_offered | leakage | 0.549 | End of policy year | This is leakage because the renewal offer is recorded at the end of the policy year, so we wouldn’t know when the driver applied |
| annual_mileage_declared | allowed | 0.280 | At application | They tell us their yearly mileage when they apply |


**3.1** One of these columns is leakage even though it did not trip the correlation test. Why can a numeric test miss it, and what kind of test catches it?

A numeric test can miss it since its correlation was below the 0.60 cutoff. A timeline test catches it becasue the renewal offer was recored at the end of the polciy year so it wasn't known when the driver applied.

---

## Lab 4: Run v1 and v2, explain the block

**4.1** Paste the DECISION line and overall AUC for v1 and for v2.
    v1: 
    Overall AUC: 0.7046
    DECISION: PASS. This model may proceed to deployment review.

    v2:
    Overall AUC: 0.7224
    DECISION: BLOCKED. This model may NOT be deployed.  

**4.2** v2 has the higher AUC. Why is it blocked? Point to the exact line in `data/generate_data.py` that creates `territory_risk_index`, and the line that creates the label's rural penalty.

V2 was blocked since it failed the subgroup_auc, subgorup_tpr, approval_disparity_ratio, and tpr_ratio tests.

This creates territory_risk_index
territory_risk_index = np.clip(42 + 30 * rural + rng.normal(0, 8, n), 0, 100).round(1)

This adds 30 points for rural drivers, giving the model a clue about where they live. This makes rural drivers less likely to be labeled claim free just because they are rural

logit = 1.35 + z - 1.10 * rural + rng.normal(0, 1.05, n)

**4.3** v2 passed `no_prohibited_features`. Explain why a preventive list check cannot catch a proxy, and name the detective check that did.

The preventive check only checks banned names, so it misses the proxy. The detective approval_disparity_ratio check catches its effects by comparing rural and urban approval rates

---

## Lab 5: Change the governance

**5.1** What new policy key did you add, and what value did you choose?

I added min_fpr_ratio and chose a value of 0.80

**5.2** Defend the value in two or three sentences. What does a false positive mean for Sierra Mutual, and for the driver? Why is unequal FPR across urban and rural drivers a fairness problem, not only a profit problem?

I chose 0.80 to keep false-positive rate reasonably similar between urban and rural drivers. A false positive means the driver gets the cheaper rate but later files a claim, whihc benefits the driver but can cost Sierra Mutual money. It's unfair if the model is more forgiving toward urban drivers and stricter towards rural drivers. 



**5.3** Describe the test you wrote in `tests/test_lab5_gate.py`. What does it prove that running the pipeline alone does not?

The test checks that an FPR ratio below 0.80 is blocked and a ratio at 0.80 passes. It checks both sides of the cutoff to prove the new rule works correctly unlike one pipline run. 


---

## Lab 6: Beat the gate

**6.1** Paste your final gate table (from the terminal or `artifacts/gate_report.json`): overall AUC, disparity ratio, TPR ratio, FPR ratio, DECISION.

**6.2** Which engineered features did you use, and which one mattered most? How do you know?

**6.3** Did you try `territory_risk_index`? What happened, and what does that tell you about the difference between a better model and a deployable model?

---

## Lab 7: Request deployment

Fill in the pull request template on GitHub. Then answer here:

**7.1** The workflow paused with "awaiting approval" before it ran. Which control is that, in the language of owner, trigger and evidence?

**7.2** After approval, what did Stage A print about your governance change? Quote it.

---

## Lab 8 (bonus): Break it and watch it stop

**8.1** What did you change on your second branch, and what did `python run_pipeline.py --config configs/v2.json` print locally?

**8.2** What did Stage A print on GitHub? Quote the refusal line.

**8.3** Which control would NOT have stopped this change on its own, and why do we need both?

---

## Declaration

I completed this exam individually, using only the permitted materials.

**Signature (type your name):**
