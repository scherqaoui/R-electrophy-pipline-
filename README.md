
---
### Full Analysis Pipeline 
---


# Module 01a — Data Preparation

**What:** Clean raw Excel fEPSP data => numeric matrices per group.  
**Inputs:** Raw sheets (From Juvenile to Aged).  
**Steps:**  
- Remove useless rows/columns, fix headers  
- Convert to numeric, filter time 10–2400 s  
- Interpolate NA (spline) (keep uniform time)  
- Split by WT/HRM * M/F * Juv/Ado/Adu/Aged 
**Outputs:** `Time`,`all_groups`, metadata (`groups_names`, `colors`, `lty`)

A named list of the discrete curves for all groups: `all_groups`

---
# Module 01a - plots raw data

***What:** Compute raw curves by groups
***Inputs:** ``
**Steps:**
-
-
-
**Outputs:**

***What:** Compute mean & SEM raw data
***Inputs:** `all_groups`
**Steps:**
-
-
-
**Outputs:**


---
# Module 01b — Common Functional Smoothing

**What:** Smooth all curves with *one* spline basis + *one* $\lambda$ + *one* common knots placement.  
**Inputs:** `Time`, `all_groups`, metadata  
**Steps:**  
1. Global mean => preliminary smoothing  
2. 2nd derivative -> adaptive knots  
3. Test $\lambda$ = 10⁻¹…10³ => pick best via GCV  
4. Smooth all individuals  
**Outputs:** `fd_list`, `basis`, `lambda_opt`, `breaks`, `gcv`

A named list of smoothed fd curves for all groups: `all_fd_groups`

---

#Module 01b - Plots smoothed fd and functional box plots
**What:** Plots smoothed curves 
**Inputs:** `all_fd_groups`...
**Steps:** 
-
-
-
**Outputs:**


**What:** Plots functional boxplot
**Inputs:** ` `..
**Steps:**
-
-
-
**Outputs:**



---

# Module 02 — Local Outlier Detection and Imputation

**What:** Detect & fix pointwise outliers inside each curve.  
**Inputs:** `all_groups`, `result_common`  
**Steps:**  
- Smooth → residuals  
- Outlier if \(|r| > t_{\alpha/2}\hat{\sigma}\)  
- Remove outliers -> re-smooth -> replace points  
**Outputs:** `fd, imputated_data, lambda, basis`
  
A named list of cleaned fd curves for all groups: `all_cleaned_fd_groups`
---
#Module 02 - Plots local Outliers

**What:** Plots Smoothed curves with supperposed smoothed curves with outlier treated locally (detection and correction with imputation)
**Inputs:** `òriginal_fd`, `cleaned_fd`
**Steps:** 
-
-
-
**Outputs:** ``

---

# Module 03 — Global Outlier Detection

**What:** Detect outlier curves among a group.  
**Inputs:** `fd_group`, `Time`  
**Steps:**  
- Two depth methods
- Outlier threholds  
- Outlier indices 
- Combine both depth methods
**Outputs:** `outliers_final`

A named list of detect outlier fd curves for all groups: `all_detected_fd_groups`
---
# Module 03 - Plots global outlier detection 

**What:** Detect outlier curves and flagged
**Inputs:** `fd_groups`, `Outlier__by_group`
**Steps:**
-
-
-
**Outputs:** 

---

# Module 04 — Mean & Variance Functions

**What:** Compute mean + variance of cleaned groups.  
**Inputs:** `all_cleaned_fd_groups`, basis, `Time`  
**Steps:**  
- Evaluate fd objects on grid  
- Mean per group  
- Variance = diag(covariance) -> smoothed  
**Outputs:** `mean_values_list`, `var_values_list`
  `fd_groups`: fd list for each group, used for the inference(SCBs, Fanova, Kinetics)
---
# Module 04 - Plots Mean and Variance curves 

**What:** Plots mean curves by sex or by genotype, same for variance curves
**Inputs:** `fd_groups`
**Steps:**
-
-
-
**Outputs:**

---

# Module 05 — Kinetic Derivatives
**What:** Compute first and second derivatives of cleaned groups.  
**Inputs:** `fd_groups`, `Time`  
**Steps:**  
- Evaluate fd objects on grid  
- 1st derivative per group  
- 2nd derivative per group  
**Outputs:** list for `all_1st_derivs`, `all_2nd_derivs`
---
# Module 05 - Plots Kinetics: st derivative (Velocity)
**What:** Plots 1st derivative , also correlation matrix for the derivative 
**Inputs:**
**Steps:**
-
-
-
**Outputs:**

---

# Module 06 — SCB Bootstrap (Amplitude inference via bootstrap SCB)

**What:** Amplitude difference between the two mean curves
**Inputs:** `fd_group1`, `fd_groups2`, `n_bootsrap`, ``Time`
**Steps:** 
- Observed mean curves + variance estimation via FPCA -> observed test statistic
- Bootsrapping : resampling curves with resplacement + recompute Boot means + restimate boot variances -> Boot root
- Crital value for SCB `z_alpha`, lower and upper bands
- functional cohen's d(t)
**Outputs:** `diff_mean`, `lower`, `upper`, `p_value`,`d_curve`,`d_mean`
---

# How to interpret SCBs Graphs and Stat

**What:** Global + local comparison between groups.  
**Key Interpretation:**  
- **p-value** -> *global*: “Is there *any* difference over time?”  
- **SCB(t)** -> *local*: “Where is the difference significant?” (0 outside band)  
- **d(t)** -> effect size: “How strong is the difference here?”

**Correct use:**  
- p-value tells *if*  
- SCB tells *where*  
- d(t) tells *how much*

---

# Module 07 — Functional ANOVA
**What:** Effects of three factors (here Genotype, Sex, Age)
**Inputs:** `fd_list`, `factorG`, `factorA`, `factorS`, `P`
**Steps:**
- 
-
-
**Outputs:** `F_stat, df, pvalue_undaj, pvalue_maxT, pvalue_holm, pvalue_bonf, pvalue_BH, perm, effect_size_eta, perm `



### Module - Plots





