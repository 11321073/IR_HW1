# Information Retrieval Homework 1 - Positional Inverted Index Documentation

## 1. Team Members & Task Allocation (小組名單)
* [黃柏鈞] (學號: 115598078)
* [賴政宏] (學號: 115C57035)

---

## 2. Environment & Dependencies (執行環境與安裝說明)

### System Requirements
* **Language:** Python 3.13
* **Libraries/APIs Used:**
  * `bs4` (`beautifulsoup4`): 用於 HTML 標籤提取與內文腳本清理。
  * `nltk`: 用於英文停用詞過濾 (`stopwords`) 與詞幹提取 (`PorterStemmer`)。
  * `re`, `collections`, `os`: Python 內建標準庫，無額外依賴。

### Installation & Execution Instructions
1. **安裝套件 (Dependencies):**
   ```bash
   pip install beautifulsoup4 nltk
   ```
2. **執行程式建置倒排索引:**
   確認 `05.warc` 檔案與 `main.py` 及 `indexer.py` 置於同一目錄，執行主程式：
   ```bash
   python main.py
   ```
3. **輸出:**
   系統將於同目錄下自動產生 `dictionary.txt`（字典檔）與 `postings.txt`（倒排記錄表檔）。

---

## 3. Major Features (主要系統功能)

1. **模組化語言預處理與參數 Trigger 控制 (Bonus Feature):**
   * 支援藉由建構子參數彈性控制開關：`enable_case_folding`, `remove_punctuation`, `enable_stopwords`, `enable_stemming`。
   * **Case Folding & Punctuation Removal:** 統一轉小寫並使用正則表達式清除特殊符號，同時保留數字與型號資訊。
   * **Stopwords & Stemming (NLTK):** 可選擇性開啟停用詞過濾與 Porter Stemmer，壓縮索引體積。
2. **兩層帶位置倒排索引 (Positional Inverted Index):**
   * 使用記憶體內巢狀 `defaultdict` 結構：`index[term][doc_id] = [pos1, pos2, ...]`，支援後續短語查詢 (Phrase Query) 與鄰近搜尋 (Proximity Search)。
3. **標準化輸出:**
   * 輸出完全符合規範格式，字典與 DocID 皆嚴格按照字典順序與數字升冪排序。

---

## 4. Major Difficulties Encountered (遇到的主要困難與解決方案)

* **困難：ClueWeb09 檔案損毀與標頭不精確**
  ClueWeb09 資料集中存在大量 `Content-Length` 標頭計算錯誤或換行符號缺失的情形。若採用標準 `warcio` 等解析套件，會因長度偏移（Offset Drift）引發解析例外並導致程式中斷。
* **解決方案：二進位 Lookahead 正則分割 (Binary Regex Splitting)**
  改採二進位讀取 (`rb`) 並搭配前瞻正則表達式 `re.split(rb'\r?\n(?=WARC/\d+\.\d+)', content)` 直接切割 WARC Record 邊界，完全無視無效的 `Content-Length` 標籤，順利解析有效網頁紀錄。
