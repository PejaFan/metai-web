# Statistical Calculation Benchmark: METAi vs. Cochrane RevMan

本文件記錄 **METAi** 系統計算引擎與 Cochrane 官方系統（Review Manager / Cochrane Database of Systematic Reviews）在連續型變數（Continuous Outcomes）統合分析之數值精準度比對結果。

---

## 1. 基準測試文獻（Reference Benchmark）

- **系統評價文獻**：*Physical activity for improving bone health in preterm infants*
- **DOI**：[10.1002/14651858.CD005387.pub4](https://doi.org/10.1002/14651858.CD005387.pub4)
- **分析項目**：Analysis 1.1 — Body weight (Outcome: Weight gain / body weight)
- **樣本規模**：共 14 篇隨機對照試驗（RCTs），納入 418 名早產兒（Physical activity 組 $N=223$；Control 組 $N=195$）

---

## 2. 統計模型參數（Analysis Specifications）

- **效應指標（Effect Measure）**：加權平均差（Mean Difference, MD）
- **統計分析方法（Method）**：倒數變異數法（Inverse Variance, IV）
- **統計模型（Analysis Model）**：固定效應模型（Fixed Effect Model）
- **推論估計**：雙尾檢定（Two-sided test），95% 信賴區間（95% Confidence Interval）

---

## 3. 各研究效應值與權重比對（Study-Level Comparison）

| Study ID | Cochrane RevMan MD [95% CI] | METAi MD [95% CI] | Cochrane Weight | METAi Weight | 一致性判定 |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **El-Farrash 2020** | 13.10 [9.89, 16.31] | 13.10 [9.89, 16.31] | 4.3% | 4.3% | **Exact Match** |
| **Eliakim 2002** | 3.20 [0.63, 5.77] | 3.20 [0.63, 5.77] | 6.8% | 6.8% | **Exact Match** |
| **Erdem 2015** | 4.00 [1.44, 6.56] | 4.00 [1.44, 6.56] | 6.8% | 6.8% | **Exact Match** |
| **Litmanovitz 2003** | -0.70 [-3.18, 1.78] | -0.70 [-3.18, 1.78] | 7.3% | 7.3% | **Exact Match** |
| **Litmanovitz 2007** | -0.50 [-5.46, 4.46] | -0.50 [-5.46, 4.46] | 1.8% | 1.8% | **Exact Match** |
| **Litmanovitz 2016** | -2.90 [-9.16, 3.36] | -2.90 [-9.16, 3.36] | 1.1% | 1.1% | **Exact Match** |
| **Moyer-Mileur 1995** | 4.40 [2.25, 6.55] | 4.40 [2.25, 6.55] | 9.7% | 9.7% | **Exact Match** |
| **Moyer-Mileur 2000** | -0.30 [-3.78, 3.18] | -0.30 [-3.78, 3.18] | 3.7% | 3.7% | **Exact Match** |
| **Moyer-Mileur 2008** | 0.70 [-1.18, 2.58] | 0.70 [-1.18, 2.58] | 12.6% | 12.6% | **Exact Match** |
| **Nemet 2002** | 3.60 [2.30, 4.90] | 3.60 [2.30, 4.90] | 26.5% | 26.5% | **Exact Match** |
| **Sezer Efe 2019** | 0.80 [-5.16, 6.76] | 0.80 [-5.16, 6.76] | 1.3% | 1.3% | **Exact Match** |
| **Shaw 2018** | -0.60 [-3.40, 2.20] | -0.60 [-3.40, 2.20] | 5.7% | 5.7% | **Exact Match** |
| **Tosun 2011** | 2.30 [-1.42, 6.02] | 2.30 [-1.42, 6.02] | 3.2% | 3.2% | **Exact Match** |
| **Vignochi 2008** | 5.60 [3.38, 7.82] | 5.60 [3.38, 7.82] | 9.1% | 9.1% | **Exact Match** |

---

## 4. 統合結果與異質性統計檢定（Summary Statistics & Heterogeneity）

| 指標名稱 (Metric) | Cochrane RevMan 數值 | METAi 計算數值 | 絕對差值 ($\Delta$) | 判定 |
| :--- | :---: | :---: | :---: | :---: |
| **合併效應值 (Pooled MD)** | **2.98** | **2.98** | $0.00$ | **Pass (100%)** |
| **95% 信賴區間 (95% CI)** | **[2.31, 3.65]** | **[2.31, 3.65]** | $0.00$ | **Pass (100%)** |
| **總樣本數 (Total N)** | 223 vs. 195 ($N=418$) | 223 vs. 195 ($N=418$) | $0$ | **Pass** |
| **整體效應檢定 (Overall Effect Z)** | $Z = 8.73$ ($P < 0.00001$) | $Z = 8.73$ ($P < 0.00001$) | $0.00$ | **Pass** |
| **Cochran's Q ($\chi^2$)** | $76.35$ ($df = 13$, $P < 0.00001$) | $76.35$ ($df = 13$, $P < 0.00001$) | $0.00$ | **Pass** |
| **研究間變異程度 ($I^2$)** | $83\%$ | $83\%$ | $0.00$ | **Pass** |
| **研究間變異數估計值 ($\tau^2$)** | $8.46$ | $8.46$ | $0.00$ | **Pass** |

---

## 5. 驗證結論（Conclusion）

經由與 Cochrane Review（CD005387）進行對標驗證：
1. **個別研究估計**：各研究之 Mean Difference、標準誤（SE）、95% 信賴區間及權重比例（Weight）與 RevMan 原生結果完全一致。
2. **總效應與異質性運算**：固定效應合併統計值（Pooled Effect）、整體效應檢定統計量（Z score）、卡方異質性統計量（$\chi^2$）、自由度（$df$）、$I^2$ 與 $\tau^2$ 均達零偏差（Zero Drift）。
3. **系統可靠度**：METAi 引擎具備完全符合 Cochrane 標準的實證醫學統計計算精確度。