---


# Module 01a — Data Preparation

## Input
Raw electrophysiology Excel sheets for Juvenile, Adolescent, Adult, Aged.  
Each sheet: raw fEPSP traces (animals in columns) + metadata rows.

## Purpose
Standardize raw inputs into clean numeric matrices for smoothing.

### Data cleaning
- Remove unused rows/columns  
- Fix acquisition headers  
- Convert signals to numeric  
- Filter time window (10–2400 sec)  
- Remove incomplete rows  
- Treat NA via linear/spline interpolation (preserve temporal uniformity)

### Data organization
- Extract shared time vector  
- Create matrices per dataset  
- Split into biologically meaningful groups:  
  *WT/HRM × Male/Female for all age stages*  
- Store in `all_groups`

### Metadata
- `groups_names`, `colors`, `lty`

## Output
- **Time** (shared)
- **all_groups** (list of matrices: n_timepoints × n_animals)
- Metadata objects


# Module 01b — Functional Smoothing (Common Across Groups)

## Input
`Time`, `all_groups`, `groups_names`, `colors`, `lty`

## Purpose
Apply **common B-spline basis**, **global λ**, **shared knot placement** to all curves.

### Procedure
#### 1. Global shape estimation
- Combine groups  
- Raw global mean  
- Preliminary smoothing  
- 2nd derivative → curvature peaks

#### 2. Adaptive knot placement
- Use |second derivative| as weights  
- Weighted quantiles define knots

#### 3. Global λ selection
Test λ ∈ {10⁻¹ … 10³} → choose via GCV.

#### 4. Final smoothing
- Interpolate curves on dense grid  
- Smooth with common basis + λ  
- Produce one **fd object per group**

## Output
- **all_fd_groups** (named list)
- Includes basis, λ grid, optimal λ, knots, GCV, preliminary λ


# Module 02 — Local Outlier Detection & Correction

## Inputs
- From Module 01: `Time`, `all_groups`  
- From Module 01b: `result_common` (basis, λ, fd_list)  
- Metadata

## Purpose
Detect & correct **local pointwise outliers** per curve.

### Logic
1. Smooth each curve using common basis + λ  
2. Compute predicted smooth and residuals  
3. Outlier rule:  
   \[
   |r| > t_{\alpha/2, df}\,\hat{\sigma}
   \]
4. Remove outlier points  
5. Re-smooth using clean points  
6. Replace outliers with smooth predictions

## Output
For each group:
- `fd` (final smoothed)
- `imputed_data`
- basis, λ, α  
Combined: `all_cleaned_fd_groups`


# Module 04 — Global Outlier Detection
(*Placeholder for your future text*)


# Module 04 — Mean and Variance Functions

## Inputs
- `all_cleaned_fd_groups`
- `result_common`
- `Time`

## Operations
1. Convert cleaned groups into `fd` objects  
2. Define evaluation grid  
3. Compute mean curves  
4. Compute variance = diag(covariance)  
5. Smooth variance curves

## Outputs
- `fd_groups`
- `mean_values_list`
- `var_values_list`


# Module 05 — Kinetics Derivatives
(*Placeholder*)


# Module 06 — SCB Bootstrap
(*Placeholder*)


# Module 06 — SCB Plots: Interpretation of p-values & Cohen's d

### Interpretation framework
1. **p-value (SCB test)**  
   - If p ≤ 0.05 → at least one significant difference over time.

2. **Effect size d(t)**  
   - Where high → strong effect  
   - Near 0 → no effect  
   - Sign change → inverted effect

3. **Simultaneous Confidence Bands (SCB)**  
   - Check if **0 is inside the band**:  
     - Outside → significant on that interval  
     - Inside → not significant

### Correct roles
- **SCB vs 0** → *local significance*  
- **d(t)** → *local effect size*  
- **p-value** → *global significance*

### Example
- p ≤ 0.05 → “some difference exists”  
- SCB excludes 0 on [t1, t2] → significant interval  
- d(t) large on [t1, t2] → strong effect

### Analogy
- p-value: *“Is something happening in the whole movie?”*  
- SCB: *“At what minute does it happen?”*  
- d(t): *“How intense is the scene?”*

Conclusion:  
You **never compare p-value to d(t)**.  
They answer *different statistical questions*.


# Module 07 — Functional ANOVA
(*Placeholder*)




