# AstroMBTI 星格預言家

結合今日星座運勢、MBTI 人格與隨機生活情境，由 LLM 生成第一人稱的內心獨白，模擬「今天的你遇到這件事，心裡會怎麼想」。

完整程式與說明見 [app.ipynb](app.ipynb)。

## 架構

```
ELLE 12 星座頁面 ──平行爬取──> 運勢快取 ─┐
MBTI 人格設定 ───────────────────────────┼─> 角色扮演 prompt ─> OpenAI gpt-6-luna ─> Gradio 介面
隨機生活情境 ────────────────────────────┘
```

- **資料層**：啟動時以 `ThreadPoolExecutor` 平行抓取 12 星座運勢並存入快取，使用者操作時直接查表。為降低對來源網站的負擔，同時連線數為 4。
- **AI 層**：組合運勢、人格與情境，透過 OpenAI API 呼叫 `gpt-6-luna`。
- **產品層**：Gradio 介面，選擇 MBTI 與星座後，顯示場景、運勢與內心獨白。

## 自行執行

需要 Python 3.12，以及有額度的 [OpenAI API key](https://platform.openai.com/api-keys)。以下指令適用 macOS／Linux；Windows 請將 `.venv/bin/` 改為 `.venv\Scripts\`，並自行建立 `.env` 檔。

```bash
python3.12 -m venv .venv
.venv/bin/pip install gradio==5.50.0 openai==3.57.0 requests beautifulsoup4 pandas python-dotenv notebook
echo 'OPENAI_API_KEY=你的金鑰' > .env    # 換成自己的金鑰，勿上傳
.venv/bin/jupyter notebook app.ipynb
```

依序執行所有儲存格，再開啟輸出中的本機網址。

模型可在 `.env` 用 `MODEL_ID` 切換。

## 已知限制

- 運勢來自第三方網頁，版面改動或機器人驗證都可能讓爬取失敗，此時會改用備用文字。
- 呼叫 OpenAI API 需要額度；ChatGPT 訂閱與 API 分開計費。
- 運勢快取只在啟動時建立，長時間執行不會自動更新。
