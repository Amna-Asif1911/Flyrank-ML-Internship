# Capstone Report — Structured Content Archetype Clustering via Unsupervised Machine Learning

- **Author:** Amna Asif
- **Lane:** Structured Content Archetype Clustering
- **Repo:** https://github.com/Amna-Asif1911/Flyrank-ML-Internship
- **Date:** August 2026

---

## 0. Abstract
How can organic search strategy move from manual page audits to automated portfolio management? This study clusters historic organic search performance data across 205,605 unique content pages using K-Means unsupervised learning. By feature-engineering standardized position, click-through rate, and impression metrics, we segment pages into 5 distinct operational content archetypes. The K-Means model achieves a Silhouette Score of 0.2989 compared to a random baseline of -0.0035, demonstrating strong cluster separation. This framework provides an automated action playbook enabling content teams to systematically protect, rewrite, improve, or monitor content at scale.

---

## 1. Problem Framing
* **Decision Supported:** Automates content portfolio audits by assigning actionable business directives (**Protect**, **Rewrite**, **Improve**, **Monitor**) to pages based on performance profiles.
* **Unit of Analysis:** Individual page URL (`page_id`).
* **Output:** A predicted content archetype label and action directive for every page.
* **Human Action:** SEO editors act directly on prioritized recommendations (e.g., rewriting metadata for high-impression/low-CTR pages or refreshing striking distance content).
* **Cost of Wrong Call:** Medium. Pruning high-value long-tail pages by mistake risks losing organic search reach, while ignoring underperforming content wastes editorial budget on low-impact updates.

---

## 2. Data Safety
* **Data Sources Used:** Aggregated impressions (`gsc_impressions`), clicks (`gsc_clicks`), average position (`avg_position`), position volatility (`position_volatility`), and active weeks from `fact_content_daily_performance` parquet files in the FlyRank dataset.
* **Excluded Columns & Fields:** Raw search query strings, client domain names, explicit client URLs, personal identification metadata, and pre-computed target labels (`trend_direction`, `trend_pct`).
* **Leakage Control:** All metrics are historically aggregated at the page level across active weeks without using future engagement metrics or target-derived trends. No client-identifying information is exposed.

---

## 3. Baseline
* **Baseline Strategy:** A random cluster assignment model distributing pages evenly across K=5 groups on the standardized feature space.
* **Baseline Metric:** Silhouette Score of **-0.0035**, indicating near-zero cluster structure and overlapping random assignments.

---

## 4. Model / Analysis
* **Method:** K-Means clustering ($K=5$) trained on standardized features using `StandardScaler`.
* **Feature List:**
  * `log_impressions`: Log-transformed total impressions ($\log(1 + \text{gsc\_impressions})$).
  * `log_clicks`: Log-transformed total clicks ($\log(1 + \text{gsc\_clicks})$).
  * `avg_position`: Mean organic search position.
  * `position_volatility`: Standard deviation of position over active weeks.
  * `avg_ctr`: Average Click-Through Rate ($\text{Clicks} / \text{Impressions}$).

---

## 5. Evaluation
* **Validation Setup:** Feature standardization followed by cluster quality scoring across 205,605 unique pages (excluding low-volume pages with $<50$ impressions).
* **Metrics Comparison:**
  * **K-Means Model Silhouette Score:** `0.2989`
  * **Random Baseline Silhouette Score:** `-0.0035`
* **Error / Boundary Analysis:** Borderline pages falling near cluster centroids require confidence thresholds before triggering major content overhauls or redirects.

---

## 6. Interpretation
The model successfully isolates 5 operational archetypes across the portfolio:
1. **Pillars & Stars (3,770 pages):** High clicks, top positions, strong CTR.
2. **CTR Underperformers - Tier 1 (41,002 pages):** High impressions but below-average CTR ($<2\%$).
3. **CTR Underperformers - Tier 2 (55,970 pages):** Mid-tier positions with room for snippet optimization.
4. **Striking Distance Opportunities (56,732 pages):** Positions 11–20 with high impression volume, representing prime refresh opportunities.
5. **Stable Long-Tail (48,131 pages):** Moderate positions and consistent low-to-medium traffic.

---

## 7. Recommendation
SEO editors and content managers should prioritize work using the following ranked playbook:

| Archetype | Action Directive | Page Count | Operational Strategy |
| :--- | :--- | :--- | :--- |
| **Pillars & Stars** | `PROTECT` | 3,770 | Preserve ranking stability; optimize internal links to pass equity. |
| **CTR Underperformers** | `REWRITE` | 96,972 | A/B test metadata, title tags, and search result snippets to raise CTR. |
| **Striking Distance** | `IMPROVE` | 56,732 | Refresh content freshness, add key subtopics, and expand depth. |
| **Stable Long-Tail** | `MONITOR` | 48,131 | Maintain active status; log position trends for sudden shifts. |

---

## 8. Reproducibility
* **Environment Setup:** Python 3.10+, `duckdb`, `pandas`, `scikit-learn`, `numpy`, `huggingface_hub`.
* **Random Seed:** `random_state=42` across all models and baseline split runs.
* **Execution Steps:**
  1. Clone repository: `git clone https://github.com/Amna-Asif1911/Flyrank-ML-Internship.git`
  2. Install dependencies: `pip install -r requirements.txt`
  3. Run notebook: Execute all cells in `work/notebooks/capstone.ipynb`.

---

## 9. Acknowledgments & Data Credit
Built on the [FlyRank ML Internship dataset](https://flyrank.ai).
