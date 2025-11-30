
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


---
title: "Full Analysis Pipeline – Flowchart"
output: html_document
---

```{r bigflow, echo=FALSE}
library(DiagrammeR)

grViz("
digraph pipeline {

  graph [layout = dot, rankdir = TB, nodesep = 0.6, ranksep = 0.8]

  # -------------------------
  # Node styles by category
  # -------------------------
  node [shape = box, style = filled, fontsize = 12]

  # Data Prep (lightblue)
  DP1 [label = 'Load Raw Excel Sheets\n(Juvenile → Aged)', fillcolor = 'lightblue']
  DP2 [label = 'Clean & Filter Data\n(10–2400 s, fix headers)', fillcolor = 'lightblue']
  DP3 [label = 'Interpolate NA\n(preserve time)', fillcolor = 'lightblue']
  DP4 [label = 'Split by Age × Genotype × Sex', fillcolor = 'lightblue']
  DP5 [label = 'Outputs:\nTime, all_groups, metadata', fillcolor = 'lightblue']

  # Smoothing (lightgreen)
  SM1 [label = 'Compute Global Mean Curve', fillcolor = 'lightgreen']
  SM2 [label = 'Find Curvature Peaks\n(2nd derivative)', fillcolor = 'lightgreen']
  SM3 [label = 'Adaptive Knot Placement', fillcolor = 'lightgreen']
  SM4 [label = 'Global λ Selection via GCV', fillcolor = 'lightgreen']
  SM5 [label = 'Smooth All Curves\n(common basis + λ)', fillcolor = 'lightgreen']
  SM6 [label = 'Outputs:\nall_fd_groups, basis, λ', fillcolor = 'lightgreen']

  # Local Outlier (gold)
  LO1 [label = 'Smooth Curve → Residuals', fillcolor = 'gold']
  LO2 [label = 'Detect Local Outliers:\n|r| > t × σ̂', fillcolor = 'gold']
  LO3 [label = 'Remove Outliers & Re-smooth', fillcolor = 'gold']
  LO4 [label = 'Replace Points with Predictions', fillcolor = 'gold']
  LO5 [label = 'Outputs:\nall_cleaned_fd_groups', fillcolor = 'gold']

  # Mean & Variance (lightpink)
  MV1 [label = 'Evaluate fd Objects on Grid', fillcolor = 'lightpink']
  MV2 [label = 'Compute Mean(t)', fillcolor = 'lightpink']
  MV3 [label = 'Compute Variance(t)\nfrom diag(cov)', fillcolor = 'lightpink']
  MV4 [label = 'Smooth Variance Curves', fillcolor = 'lightpink']
  MV5 [label = 'Outputs:\nmean & variance functions', fillcolor = 'lightpink']

  # Derivatives (lightgray)
  KD1 [label = '1st Derivative\n(speed of change)', fillcolor = 'lightgray']
  KD2 [label = '2nd Derivative\n(curvature)', fillcolor = 'lightgray']

  # SCB Bootstrap (orange)
  SC1 [label = 'Bootstrap Curves', fillcolor = 'orange']
  SC2 [label = 'Build SCB(t)\n(upper/lower bands)', fillcolor = 'orange']
  SC3 [label = 'Global p-value', fillcolor = 'orange']
  SC4 [label = 'Effect Size d(t)', fillcolor = 'orange']
  SC5 [label = 'Where?\nSCB vs 0', fillcolor = 'orange']

  # ANOVA (salmon)
  FA1 [label = 'Functional ANOVA', fillcolor = 'salmon']
  FA2 [label = 'Group-Level Differences\nover Time', fillcolor = 'salmon']

  # -------------------------
  # Connections
  # -------------------------

  # Data Prep chain
  DP1 -> DP2 -> DP3 -> DP4 -> DP5

  # Smoothing chain
  DP5 -> SM1 -> SM2 -> SM3 -> SM4 -> SM5 -> SM6

  # Local outlier chain
  SM6 -> LO1 -> LO2 -> LO3 -> LO4 -> LO5

  # Mean & Variance chain
  LO5 -> MV1 -> MV2 -> MV3 -> MV4 -> MV5

  # Derivatives
  MV5 -> KD1
  MV5 -> KD2

  # SCB Bootstrap
  KD1 -> SC1
  KD2 -> SC1
  SC1 -> SC2 -> SC3
  SC2 -> SC5
  KD1 -> SC4
  KD2 -> SC4

  # ANOVA
  MV5 -> FA1 -> FA2
}
")




