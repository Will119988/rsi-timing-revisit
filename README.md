# RSI(3)/RSI(5) 交叉訊號重驗

## 交易邏輯
2004 年一篇台灣碩士論文宣稱：用 RSI(3)/RSI(5) 交叉搭配均線，能在台灣選擇權市場抓到轉折、賺取優於現貨的報酬。但論文樣本只有 500 個交易日（2002/04–2004/03），幾乎全是單邊多頭，沒有樣本外測試。

本研究把策略拆成兩層驗證：均線濾網（判斷方向）與 RSI 擇時（決定進場時點），並用 A（買進持有）／B（完整策略）／C（只有濾網）三組對照，讓 B − C 直接反映 RSI 本身的貢獻。標的為台灣加權指數，2000-01-01 ~ 2026-01-20（26 年、6,391 個交易日），涵蓋網路泡沫與金融海嘯兩次腰斬，樣本內外切點為 2017/2018。均線濾網原始依據為 Faber(2007) 的 200 日設定；本版改用 50 日，並以 20–200 天的敏感度掃描驗證這個選擇不是恰好挑到好看的結果。

## 回測績效
50 個「可做多」區間，50 個都跌破過 RSI(3) 30、50 個都產生了進場訊號——**RSI 的篩選率是零**，它不是篩選器，是延遲器：平均讓每次進場延後 13 天，買得更貴、帳面更痛、報酬更低。

全期間（2000–2026）：

| | A 買進持有 | C 只有濾網 | B 完整策略 |
|---|---|---|---|
| 年化報酬 | 5.11% | **7.88%** | 4.49% |
| Sharpe | 0.25 | **0.59** | 0.37 |
| 最大回撤 | −66.22% | −30.38% | −34.24% |

用 8 個不同均線天數（20–200）重跑，RSI 的淨貢獻在每一個窗口下都是負值——這不是 50 天的特例。完整過程與逐年細節見：

- [`reports/rsi_study.html`](reports/rsi_study.html)
- 敏感度掃描數據：[`reports/appendix/ma_sensitivity.csv`](reports/appendix/ma_sensitivity.csv)
- 資料品質檢查：[`reports/appendix/data_quality_report.md`](reports/appendix/data_quality_report.md)

## 程式碼
六本 Jupyter Notebook，依序執行即為完整流程（`src/`）：

- [`01_data_check.ipynb`](src/01_data_check.ipynb)——下載原始資料、品質檢查
- [`02_indicators.ipynb`](src/02_indicators.ipynb)——計算均線與 RSI
- [`03_regime.ipynb`](src/03_regime.ipynb)——月底判斷多空狀態
- [`04_backtest_C.ipynb`](src/04_backtest_C.ipynb)——C 組（只有濾網）回測
- [`05_signals.ipynb`](src/05_signals.ipynb)——RSI 進場訊號產生
- [`06_backtest_B.ipynb`](src/06_backtest_B.ipynb)——B 組（完整策略）回測、三組彙總

Notebook 保留了執行輸出（圖表、表格），可以直接瀏覽結果，不需要重新執行。若要重新執行，需自備台灣加權指數原始資料並放進本機的 `data/` 資料夾（未包含在此 repo，原始資料量較大）。

## 技術棧
Python（pandas）、yfinance
