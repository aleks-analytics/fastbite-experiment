# FastBite: Factorial Experiment on Pricing & Checkout Optimization

A multivariate statistical experiment analysis in Python (`pingouin`, `pandas`, `seaborn`) evaluating the interaction effects between dynamic pricing models and checkout UX layouts on net margin per order.

[English Version](#english-version) | [Русская версия](#russian-version)

---

<a name="english-version"></a>
## English Version

### Business Context & Problem Statement
Food delivery platform **FastBite** tested combinations of pricing strategies and checkout interfaces across multiple city tiers to maximize net margin per order (`net_margin_usd`).

The experiment tested a **3 × 3 Factorial Design**:
1. **Pricing Models (`pricing_model`):**
   - `fixed_low_fee` (Standard low delivery fee)
   - `dynamic_surge_multiplier` (High-demand surge pricing)
   - `tiered_basket_subsidy` (Subsidized delivery above basket thresholds)
2. **Checkout Layouts (`checkout_layout`):**
   - `one_click_express` (Frictionless single-tap checkout)
   - `upsell_grid_modal` (Aggressive pop-up cross-sell grid)
   - `gamified_loyalty_bar` (Visual progress bar toward free delivery / tier rewards)

---

### Statistical Pipeline & Methodology

1. **Data Preprocessing & Cleaning:**
   - Filtered out anomalous delivery times (<= 0), negative basket values, and extreme net margin outliers (not in 0-100 range).
2. **Assumption Testing (Normality & Homoscedasticity):**
   - D'Agostino-Pearson normality test (`pg.normality`) showed group non-normality.
   - Levene's test (`pg.homoscedasticity`) rejected equal variances (p < 0.001).
   - Heteroscedasticity justified the use of **Welch's ANOVA** and **Games-Howell post-hoc pairwise tests**.
3. **Two-Way Factorial ANOVA:**
   - Evaluated main effects and the crucial cross-factor interaction term (`pricing_model * checkout_layout`).

---

### Experimental Findings

| Factor / Source | Sum of Squares (SS) | DF | Mean Square (MS) | F-value | p-value | Effect Size (np2) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `pricing_model` | 2,090.17 | 2 | 1,045.08 | 123.03 | < 0.001 | 6.8% |
| `checkout_layout` | 1,070.26 | 2 | 535.13 | 63.00 | < 0.001 | 3.6% |
| **`pricing_model * checkout_layout`** | **5,634.22** | **4** | **1,408.55** | **165.82** | **< 0.001** | **16.4%** |

#### Key Interaction Insights:
* **The Surge Friction Trap:** During high-demand surge pricing (`dynamic_surge_multiplier`), adding an upsell modal (`upsell_grid_modal`) crushes customer intent, dropping margin to $4.30. Conversely, `one_click_express` achieves a peak margin of **$8.50**.
* **The Gamified Subsidy Synergy:** When delivery subsidies are tied to basket thresholds (`tiered_basket_subsidy`), the `gamified_loyalty_bar` yields the highest overall margin (**$8.90**, a +41% uplift) by motivating customers to add extra items to hit free delivery.
* **Consistency:** The interaction effect remains uniform across all city tiers (`tier_1_megacity`, `tier_2_regional`, `tier_3_small`).

---

### Product Recommendations Matrix

| Context / Pricing State | Recommended Checkout UX | Business Rationale |
| :--- | :--- | :--- |
| **High Demand (Surge Multiplier)** | `one_click_express` | Remove all purchase friction; users already pay a premium. |
| **Threshold Discounts (Basket Subsidy)** | `gamified_loyalty_bar` | Visual progress nudges users to expand basket size. |
| **Standard Low Fee (Fixed Fee)** | `upsell_grid_modal` | Cross-sells compensate for low baseline delivery margins. |

---

### Quickstart
```bash
git clone https://github.com/aleks-analytics/fastbite-experiment.git
cd fastbite-experiment
pip install pandas seaborn pingouin matplotlib
jupyter notebook
