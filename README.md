# MARS Web — Meta Automated Review System

致敬 Cochrane RevMan 的網頁版 **統合分析（Meta-analysis）** 原型，純前端單一 HTML 檔（React + Tailwind CDN），雙擊即可在瀏覽器執行。

線上展示：https://reliable-brioche-6513c4.netlify.app

## 功能

- **三層架構**：主題 (Topic) → 結果指標 (Outcome，每主題上限 10 個) → 各自的數據輸入與森林圖分析
- **連續型數據**：Mean Difference（逆變異數法）
- **二分類數據**：Risk Ratio / Odds Ratio / Risk Difference（固定效應 Mantel-Haenszel、隨機效應 DerSimonian-Laird）
- **森林圖 (Forest Plot)**：RevMan 樣式，逐研究顯示原始數據欄位（Mean/SD/Total 或 Events/Total）、權重方塊、合併鑽石；可下載 SVG / PNG
- **次組別分析 (Subgroup)**：可新增多組，各組合併菱形 + 組間差異檢驗（Q_between）
- **統計指標**：Pooled 效應量、95% CI、Z、p、Cochran's Q、I²、τ²（DerSimonian-Laird）

## 使用方式

直接用瀏覽器開啟 [`mars-web.html`](mars-web.html) 即可（首次載入需連網以取得 React / Tailwind CDN）。

## 檔案

| 檔案 | 說明 |
| --- | --- |
| `mars-web.html` | 完整應用程式（單一檔） |
| `_redirects` | Netlify 設定：根網址改寫為 `mars-web.html` |

## 免責聲明

僅供教學與原型展示用途。統計方法採常態近似（95% CI 使用 z = 1.96），τ² 採 DerSimonian-Laird 動差估計，二分類固定效應採 Mantel-Haenszel。資料儲存在瀏覽器記憶體，重新整理會回復預設示範資料。
