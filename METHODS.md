# METHODS — METAi system 統計方法與文獻對照

本文件說明 `metai-web.html` 所實作之統合分析（meta-analysis）統計方法，並將**每一條公式對應到其原始文獻**，以供同儕稽核與再現。所有記號與公式均與程式碼實作一致；文末附有「函式 → 方法 → 文獻」對照表。

> **定位聲明**：本工具為教學與原型展示用途，非臨床決策軟體。統計採大樣本常態近似，隨機效應僅提供 DerSimonian–Laird 動差估計。詳見文末〈限制與未實作項目〉。

---

## 1. 記號 (Notation)

單一研究 $i$ 的效應量估計記為 $\hat\theta_i$，其（大樣本）變異數為 $v_i$，標準誤 $\mathrm{SE}_i=\sqrt{v_i}$，逆變異數權重 $w_i = 1/v_i$。研究數為 $k$。

二分類資料以 $2\times2$ 表示：介入組事件 $a$、非事件 $b$；對照組事件 $c$、非事件 $d$；$n_1=a+b$、$n_2=c+d$、$N=n_1+n_2$。

---

## 2. 各研究效應量與變異數 (Per-study effect sizes)

### 2.1 連續型資料 — 平均差 (Mean Difference, MD)

$$
\hat\theta_i = m_{1i}-m_{2i},\qquad
v_i = \frac{s_{1i}^2}{n_{1i}} + \frac{s_{2i}^2}{n_{2i}}
$$

> Cochrane Handbook §10.5；Borenstein et al. (2009) ch. 4。
> *（本版本僅實作 MD；未實作標準化平均差 SMD / Hedges' g。）*

### 2.2 二分類資料 — 連續性校正與雙零研究

對**比值型指標（RR、OR）**：當任一格為 0 時，對四格各加上校正值 $0.5$；**雙零研究**（$a=c=0$ 或 $b=d=0$）自四格分析中排除。此為 RevMan 之預設行為。

> 連續性校正 0.5：Cochrane Handbook §10.4.4.1；Sweeting et al. (2004)。

以下 $a',b',c',d'$ 表校正後之格數。

**風險比 (Risk Ratio, RR)** — 對數尺度：

$$
\hat\theta_i=\ln\!\frac{a_i'/n_{1i}'}{c_i'/n_{2i}'},\qquad
v_i=\frac{1}{a_i'}-\frac{1}{n_{1i}'}+\frac{1}{c_i'}-\frac{1}{n_{2i}'}
$$

**勝算比 (Odds Ratio, OR)** — 對數尺度：

$$
\hat\theta_i=\ln\!\frac{a_i'\,d_i'}{b_i'\,c_i'},\qquad
v_i=\frac{1}{a_i'}+\frac{1}{b_i'}+\frac{1}{c_i'}+\frac{1}{d_i'}
$$

**風險差 (Risk Difference, RD)** — 原始尺度：

$$
\hat\theta_i=\frac{a_i}{n_{1i}}-\frac{c_i}{n_{2i}},\qquad
v_i=\frac{a_i b_i}{n_{1i}^3}+\frac{c_i d_i}{n_{2i}^3}
$$

> 各研究對數 RR / OR 之變異數：Cochrane Handbook §10.4.2；Deeks & Higgins (2010)。RD 變異數為二項比例差之大樣本變異數。RD 於變異數恰為 0 時，改用 $0.5$ 校正之替代式以避免退化。

---

## 3. 固定效應模型 (Fixed-effect models)

### 3.1 逆變異數法 (Inverse Variance, IV)

連續型資料，以及二分類指標無法以 M-H 計算時，採逆變異數加權：

$$
\hat\theta_{\mathrm{IV}}=\frac{\sum_i w_i\hat\theta_i}{\sum_i w_i},\qquad
\mathrm{SE}=\sqrt{\frac{1}{\sum_i w_i}},\qquad w_i=\frac{1}{v_i}
$$

> Borenstein et al. (2009) ch. 11；Cochrane Handbook §10.3。

### 3.2 Mantel–Haenszel 法 (二分類固定效應)

二分類指標之固定效應合併採 Mantel–Haenszel (1959)；點估計以**原始格數**計算（不套用連續性校正），與 RevMan 一致。

**RR：** 令 $R_i=\dfrac{a_i n_{2i}}{N_i}$、$S_i=\dfrac{c_i n_{1i}}{N_i}$，

$$
\hat\theta_{\mathrm{MH}}=\ln\!\frac{\sum_i R_i}{\sum_i S_i},\qquad
\mathrm{Var}(\ln \mathrm{RR}_{\mathrm{MH}})=\frac{\sum_i P_i}{\left(\sum_i R_i\right)\left(\sum_i S_i\right)},\quad
P_i=\frac{n_{1i}n_{2i}(a_i+c_i)-a_i c_i N_i}{N_i^2}
$$

> Greenland & Robins (1985)。

**OR：** 令 $R_i=\dfrac{a_i d_i}{N_i}$、$S_i=\dfrac{b_i c_i}{N_i}$、$P_i=\dfrac{a_i+d_i}{N_i}$、$Q_i=\dfrac{b_i+c_i}{N_i}$，

$$
\hat\theta_{\mathrm{MH}}=\ln\!\frac{\sum_i R_i}{\sum_i S_i}
$$

$$
\mathrm{Var}(\ln \mathrm{OR}_{\mathrm{MH}})=
\frac{\sum_i P_iR_i}{2\left(\sum_i R_i\right)^2}
+\frac{\sum_i \left(P_iS_i+Q_iR_i\right)}{2\left(\sum_i R_i\right)\left(\sum_i S_i\right)}
+\frac{\sum_i Q_iS_i}{2\left(\sum_i S_i\right)^2}
$$

> Robins, Breslow & Greenland (1986)（即 RBG 變異數）。

**RD：** 令 $w_i=\dfrac{n_{1i}n_{2i}}{N_i}$，

$$
\hat\theta_{\mathrm{MH}}=\frac{\sum_i w_i\left(\frac{a_i}{n_{1i}}-\frac{c_i}{n_{2i}}\right)}{\sum_i w_i},\qquad
\mathrm{Var}=\frac{\sum_i J_i}{\left(\sum_i w_i\right)^2},\quad
J_i=\frac{a_i b_i\, n_{2i}^3 + c_i d_i\, n_{1i}^3}{n_{1i}n_{2i}N_i^2}
$$

> Greenland & Robins (1985)。

森林圖顯示之各研究權重百分比，於固定效應二分類模型即為 M-H 權重（$S_i$ 或 $w_i$）佔總和之比例；IV / 隨機效應模型則為 $w_i$ 佔總和之比例。

---

## 4. 隨機效應模型 (Random-effects model)

採 DerSimonian & Laird (1986) 動差估計。以逆變異數權重 $w_i=1/v_i$ 計算研究間變異數 $\tau^2$：

$$
\tau^2=\max\!\left(0,\ \frac{Q-\mathrm{df}}{C}\right),\qquad
C=\sum_i w_i-\frac{\sum_i w_i^2}{\sum_i w_i},\qquad \mathrm{df}=k-1
$$

其中 $Q$ 為 Cochran's Q（見 §5）。隨機效應權重與合併估計：

$$
w_i^{*}=\frac{1}{v_i+\tau^2},\qquad
\hat\theta_{\mathrm{RE}}=\frac{\sum_i w_i^{*}\hat\theta_i}{\sum_i w_i^{*}},\qquad
\mathrm{SE}=\sqrt{\frac{1}{\sum_i w_i^{*}}}
$$

> DerSimonian & Laird (1986)；Cochrane Handbook §10.10.4。
> 二分類指標之隨機效應採**逆變異數 DL**（以各研究連續性校正後之對數效應量為輸入），與 RevMan 行為一致。

---

## 5. 異質性統計量 (Heterogeneity)

**Cochran's Q**（以逆變異數估計計算）：

$$
Q=\sum_i w_i\left(\hat\theta_i-\hat\theta_{\mathrm{IV}}\right)^2,\qquad \mathrm{df}=k-1
$$

> Cochran (1954)。Q 之 $p$ 值取自自由度 $\mathrm{df}$ 之卡方分布。

**$I^2$：**

$$
I^2=\max\!\left(0,\ \frac{Q-\mathrm{df}}{Q}\right)\times 100\%
$$

> Higgins & Thompson (2002)；Higgins, Thompson, Deeks & Altman (2003)。

**$\tau^2$：** 見 §4（DerSimonian–Laird 動差估計）。

---

## 6. 信賴區間與顯著性檢定 (CI & significance)

採大樣本常態近似，臨界值使用標準常態 97.5 百分位之精確值 $z_{0.975}=1.959963985$（程式常數 `Z95`）：

$$
\text{95\% CI}:\quad \hat\theta \pm z_{0.975}\cdot \mathrm{SE}
$$

整體效應檢定：

$$
Z=\frac{\hat\theta}{\mathrm{SE}},\qquad p=2\left[1-\Phi(|Z|)\right]
$$

比值型指標（RR、OR）之點估計與區間，於報表與森林圖中以 $\exp(\cdot)$ 還原至原始尺度。

> Cochrane Handbook §10.3–10.4。標準常態尾機率 $\Phi$ 之數值計算見 §8。

---

## 7. 次組別分析 (Subgroup analysis)

各次組別以相同模型分別合併。組間差異採固定效應類比之檢定：令次組別 $j$ 之合併估計為 $\hat\theta_j$、標準誤 $\mathrm{SE}_j$，權重 $W_j=1/\mathrm{SE}_j^2$，

$$
\bar\theta=\frac{\sum_j W_j\hat\theta_j}{\sum_j W_j},\qquad
Q_{\text{between}}=\sum_j W_j\left(\hat\theta_j-\bar\theta\right)^2,\qquad \mathrm{df}=G-1
$$

$$
I^2_{\text{between}}=\max\!\left(0,\ \frac{Q_{\text{between}}-\mathrm{df}}{Q_{\text{between}}}\right)\times100\%
$$

其中 $G$ 為有效次組別數（需 $\ge 2$）；$p$ 值取自卡方分布。

> Cochrane Handbook §10.11.3.1；Borenstein et al. (2009) ch. 19；Deeks, Higgins & Altman。

---

## 8. 數值方法 (Numerical methods)

| 需求 | 方法 | 文獻 |
|---|---|---|
| 標準常態尾機率 $\Phi$ | 有理多項式近似（$t=1/(1+0.2316419\,x)$） | Abramowitz & Stegun (1964), eq. 26.2.17 |
| 卡方上尾機率 | $p=1-P\!\left(\tfrac{\mathrm{df}}{2},\tfrac{Q}{2}\right)$，$P$ 為正規化不完全 Gamma 函數；以級數（$x<a+1$）與連分數（否則）計算 | Press et al., *Numerical Recipes* (2007), `gammp` |
| 對數 Gamma $\ln\Gamma(x)$ | Lanczos 近似 | Press et al., *Numerical Recipes* (2007), `gammln` |

---

## 9. 與 RevMan 之一致性及交叉驗證

本工具之預設選擇刻意對齊 Cochrane RevMan：二分類固定效應採 M-H、隨機效應採 IV-DL、連續性校正 $0.5$、排除雙零研究、CI 採常態近似。輸出已對 4 篇已發表 Cochrane 統合分析之公開數據進行交叉驗證。建議再以 R 之 `meta` / `metafor` 套件進行第二重驗證；比對時須將估計器設定對齊（$\tau^2$ 用 DL、CI 用常態 z、固定效應用 M-H、$0.5$ 校正、排除雙零），否則預設差異（如 REML、Hartung–Knapp）會造成假性不一致。

---

## 10. 限制與未實作項目 (Limitations)

- 僅大樣本常態近似；未提供小樣本校正（如 Hartung–Knapp–Sidik–Jonkman）與預測區間。
- 隨機效應僅 DerSimonian–Laird；未提供 REML、Paule–Mandel、Empirical Bayes 等估計器。
- 連續型僅 MD；未實作 SMD（Hedges' g）、比例／發生率／單組資料、連續型 M-H 或 Peto OR。
- 未提供發表偏差評估（funnel plot、Egger's / Begg's 檢定、trim-and-fill）與敏感度／影響力分析。
- 連續性校正固定為 $0.5$；未提供其他校正方案或處理稀疏資料之精確／GLMM 方法。
- 資料僅存於瀏覽器記憶體，不持久化。

---

## 11. 參考文獻 (References)

1. Mantel N, Haenszel W. Statistical aspects of the analysis of data from retrospective studies of disease. *J Natl Cancer Inst.* 1959;22(4):719–748.
2. Cochran WG. The combination of estimates from different experiments. *Biometrics.* 1954;10(1):101–129.
3. Greenland S, Robins JM. Estimation of a common effect parameter from sparse follow-up data. *Biometrics.* 1985;41(1):55–68.
4. Robins J, Breslow N, Greenland S. Estimators of the Mantel–Haenszel variance consistent in both sparse data and large-strata limiting models. *Biometrics.* 1986;42(2):311–323.
5. DerSimonian R, Laird N. Meta-analysis in clinical trials. *Control Clin Trials.* 1986;7(3):177–188.
6. Higgins JPT, Thompson SG. Quantifying heterogeneity in a meta-analysis. *Stat Med.* 2002;21(11):1539–1558.
7. Higgins JPT, Thompson SG, Deeks JJ, Altman DG. Measuring inconsistency in meta-analyses. *BMJ.* 2003;327(7414):557–560.
8. Sweeting MJ, Sutton AJ, Lambert PC. What to add to nothing? Use and avoidance of continuity corrections in meta-analysis of sparse data. *Stat Med.* 2004;23(9):1351–1375.
9. Borenstein M, Hedges LV, Higgins JPT, Rothstein HR. *Introduction to Meta-Analysis.* Wiley; 2009.
10. Higgins JPT, Thomas J, Chandler J, Cumpston M, Li T, Page MJ, Welch VA (eds). *Cochrane Handbook for Systematic Reviews of Interventions* (v6.x), 特別是 §10。Cochrane.
11. Abramowitz M, Stegun IA. *Handbook of Mathematical Functions.* National Bureau of Standards; 1964（eq. 26.2.17）。
12. Press WH, Teukolsky SA, Vetterling WT, Flannery BP. *Numerical Recipes: The Art of Scientific Computing* (3rd ed.). Cambridge University Press; 2007（`gammln`, `gammp`）。

---

## 附錄：程式函式 → 方法 → 文獻 對照表

| 函式 (`metai-web.html`) | 實作方法 | 文獻 |
|---|---|---|
| `computeRowsCont` | 各研究 MD 與變異數 | Cochrane Handbook §10.5 |
| `computeRowsDich` | 各研究 log RR / log OR / RD 與變異數；$0.5$ 連續性校正；排除雙零 | §2.2；Sweeting et al. (2004) |
| `poolInvVar` | 逆變異數固定／隨機效應合併 | Borenstein (2009) ch. 11 |
| `poolDich` | M-H 二分類固定效應（RR/OR/RD） | Greenland & Robins (1985)；Robins, Breslow & Greenland (1986) |
| `heterogeneity` | Cochran's Q、$I^2$、$\tau^2$ (DL) | Cochran (1954)；Higgins et al. (2003)；DerSimonian & Laird (1986) |
| `analyzeGroups` | 次組別合併與組間 $Q_{\text{between}}$ | Cochrane Handbook §10.11.3.1 |
| `twoSidedP` | 標準常態雙尾 $p$ | Abramowitz & Stegun (1964) 26.2.17 |
| `chiSqP` / `gammp` | 卡方上尾機率（不完全 Gamma） | Numerical Recipes (2007) |
| `gammaln` | $\ln\Gamma(x)$（Lanczos） | Numerical Recipes (2007) |
| 常數 `Z95` | $z_{0.975}=1.959963985$ | 標準常態分布 |

---

*本文件對應 `metai-web.html` 之現行版本；若統計函式有更動，請同步更新本文件與對照表。*
