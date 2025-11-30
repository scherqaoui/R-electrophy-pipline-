
### Full Analysis Pipeline 



# Module 01a — Data Preparation

**What:** Clean raw Excel fEPSP data → numeric matrices per group.  
**Inputs:** Raw sheets (Juvenile–Aged).  
**Steps:**  
- Remove useless rows/columns, fix headers  
- Convert to numeric, filter time 10–2400 s  
- Interpolate NA (keep uniform time)  
- Split by WT/HRM × M/F  
**Outputs:** `Time`, `all_groups`, metadata (`groups_names`, `colors`, `lty`)

---

# Module 01b — Common Functional Smoothing

**What:** Smooth all curves with *one* spline basis + *one* λ.  
**Inputs:** `Time`, `all_groups`, metadata  
**Steps:**  
1. Global mean → preliminary smoothing  
2. 2nd derivative → adaptive knots  
3. Test λ = 10⁻¹…10³ → pick best via GCV  
4. Smooth all individuals  
**Outputs:** `all_fd_groups`, basis, λ, knots, GCV

---

# Module 02 — Local Outlier Detection

**What:** Detect & fix pointwise outliers inside each curve.  
**Inputs:** `all_groups`, `result_common`  
**Steps:**  
- Smooth → residuals  
- Outlier if \(|r| > t_{\alpha/2}\hat{\sigma}\)  
- Remove outliers → re-smooth → replace points  
**Outputs:** `all_cleaned_fd_groups` (+ imputed data)

---

# Module 04 — Mean & Variance Functions

**What:** Compute mean + variance of cleaned groups.  
**Inputs:** `all_cleaned_fd_groups`, basis, `Time`  
**Steps:**  
- Evaluate fd objects on grid  
- Mean per group  
- Variance = diag(covariance) → smoothed  
**Outputs:** `fd_groups`, `mean_values_list`, `var_values_list`

---

# Module 05 — Kinetic Derivatives
(*Your derivatives computations: first, second derivative…*)

---

# Module 06 — SCB Bootstrap (Significance Testing)

**What:** Global + local comparison between groups.  
**Key Interpretation:**  
- **p-value** → *global*: “Is there *any* difference over time?”  
- **SCB(t)** → *local*: “Where is the difference significant?” (0 outside band)  
- **d(t)** → effect size: “How strong is the difference here?”

**Correct use:**  
- p-value tells *if*  
- SCB tells *where*  
- d(t) tells *how much*

---

# Module 07 — Functional ANOVA
(*Global test across multiple groups*)







