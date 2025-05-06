---
name: mcp-server-webcrawl
digest: 搜尋和檢索網絡爬蟲內容。連接到具有高級過濾和內容檢索功能的爬蟲。
author: pragmar
repository: https://github.com/pragmar/mcp_server_webcrawl
homepage: https://pragmar.com/mcp-server-webcrawl/
capabilities:
  prompts: false
  resources: false
  tools: true
tags:
  - crawler
  - search
  - indexing
icon: https://pragmar.com/media/static/images/home/mcp-server-webcrawl.png
createTime: 2025-03-26
---

這是一個提供網絡爬蟲搜尋和檢索功能的模型上下文協議(MCP)服務器。該服務器允許 MCP 客戶端使用高級過濾功能搜尋和訪問已爬取站點的網絡內容。

## 功能

🔍 **全文搜尋**。通過關鍵詞、標籤、CSS 類等過濾網絡內容。

🔬 **高級搜尋**。按狀態、內容類型和/或站點進行搜尋。

🕸️ **多爬蟲支持**。支持諸如 WARC、wget、InterroBot、Katana、SiteOne 等爬蟲。

✂️ **API 上下文形成**。欄位選項決定 API 返回的內容，在 LLM 互動中保持上下文輕量。

## 安裝

需要 Python 3.10 或更高版本。

### 使用 pip 安裝

使用 pip 安裝包：

```bash
pip install mcp-server-webcrawl
```

## 配置

配置因爬蟲而異。你需要將--datasource 示例替換為目標路徑。

### wget 配置

```json
{
  "mcpServers": {
    "webcrawl": {
      "command": "mcp-server-webcrawl",
      "args": ["--crawler", "wget", "--datasrc", "/path/to/wget/archives/"]
    }
  }
}
```

**測試過的 wget 命令：**

```bash
# --adjust-extension用於文件擴展名，例如：*.html
wget --mirror https://example.com
wget --mirror https://example.com --adjust-extension
```

### WARC 配置

```json
{
  "mcpServers": {
    "webcrawl": {
      "command": "mcp-server-webcrawl",
      "args": ["--crawler", "warc", "--datasrc", "/path/to/warc/archives/"]
    }
  }
}
```

**用於 WARC 的測試過的 wget 命令：**

```bash
wget --warc-file=example --recursive https://example.com
wget --warc-file=example --recursive --page-requisites https://example.com
```

### InterroBot 配置

```json
{
  "mcpServers": {
    "webcrawl": {
      "command": "mcp-server-webcrawl",
      "args": [
        "--crawler",
        "interrobot",
        "--datasrc",
        "/home/user/Documents/InterroBot/interrobot.v2.db"
      ]
    }
  }
}
```

**注意：**

- 爬蟲必須在 InterroBot 內部運行（窗口模式）
- macOS/Windows：--datasource 路徑在 InterroBot 選項頁面中提供

### Katana 配置

```json
{
  "mcpServers": {
    "webcrawl": {
      "command": "mcp-server-webcrawl",
      "args": ["--crawler", "katana", "--datasrc", "/path/to/katana/crawls/"]
    }
  }
}
```

**測試過的 Katana 命令：**

```bash
# -store-response用於存儲爬取內容
# -store-response-dir允許在單個目錄中爬取多個站點
katana -u https://example.com -store-response -store-response-dir crawls/
```

### SiteOne 配置

```json
{
  "mcpServers": {
    "webcrawl": {
      "command": "mcp-server-webcrawl",
      "args": [
        "--crawler",
        "siteone",
        "--datasrc",
        "/path/to/siteone/archives/"
      ]
    }
  }
}
```

**注意：**

- 爬蟲必須在 SiteOne 內部運行（窗口模式）
- 必須選擇"Generate offline website"選項

## 可用工具

### `webcrawl_sites`

獲取站點列表（項目站點或爬取目錄）。

**可選參數**

- `fields`（字符串數組，可選）：除了基本欄位（id、url）外，要包含在響應中的附加欄位。選項包括：
- `ids`（整數數組，可選）：按項目 ID 過濾的列表。為空表示所有項目。

**可選欄位**

- `modified` 最後修改的 ISO 8601 時間戳
- `created` 創建的 ISO 8601 時間戳
- `robots` Robots.txt 信息（有限支持）

**使用示例**

列出所有爬取的站點："你能列出網絡爬蟲嗎？"

獲取特定站點的基本爬取信息："你能獲取 example.com 的網絡爬蟲信息嗎？"

### `webcrawl_search`

搜尋項目範圍內的資源（網頁、CSS、PDF 等）並檢索指定欄位。

**可選參數：**

- `query`（字符串，可選）：全文搜尋查詢字符串。支持全文和布爾運算符，語法匹配 SQLite FTS5 的布爾模式（AND, OR, NOT, 引號短語, 後綴通配符）。
- `sites`（整數數組，可選）：項目 ID 列表，用於將搜尋結果過濾到特定站點。
- `limit`（整數，可選）：要返回的最大結果數。默認為 20，最大為 100。
- `offset`（整數，可選）：為分頁而跳過的結果數。默認為 0。
- `sort`（字符串，可選）：結果的排序順序。`+`前綴表示升序，`-`表示降序。
- `statuses`（整數數組，可選）：按 HTTP 狀態碼過濾（例如[200]表示成功響應，[404, 500]表示錯誤）。
- `types`（字符串數組，可選）：過濾到特定資源類型。
- `thumbnails`（布爾值，可選）：啟用圖像縮略圖的 base64 編碼數據。默認為 false。
- `fields`（字符串數組，可選）：除了基本欄位（id、URL、status）外，要包含在響應中的附加欄位。空列表表示僅基本欄位。內容欄位可能會很大，應謹慎與 LIMIT 一起使用：
- `ids`（整數數組，可選）：通過 ID 直接查找特定資源。

**可選欄位**

- `created`：創建的 ISO 8601 時間戳
- `modified`：最後修改的 ISO 8601 時間戳
- `content`：如果是 text/\*，則為資源的實際內容（HTML/CSS/JS/純文本）
- `name`：資源名稱或標題信息
- `size`：文件大小信息
- `time`：與資源相關的時間指標（支持因爬蟲類型而異）
- `headers`：與資源關聯的 HTTP 頭（支持因爬蟲類型而異）

**排序選項**

- `+id`, `-id`：按資源 ID 排序
- `+url`, `-url`：按資源 URL 排序
- `+status`, `-status`：按 HTTP 狀態碼排序
- `?`：隨機排序（對統計抽樣有用）

**使用示例：**

搜尋站點關鍵詞："你能在 example.com 爬蟲中搜尋關鍵詞嗎？"

搜尋和過濾內容摘要："你能在網絡爬蟲中搜尋關鍵詞，收集並總結內容嗎？"

獲取圖像信息："你能列出 example.com 網絡爬蟲中的圖片嗎？"

查找帶有關鍵詞的 404 錯誤（WARC/Katana/InterroBot）："你能在 example.com 爬蟲中搜尋 404 錯誤嗎？"

## 許可證

本項目採用 MPL 2.0 許可證。有關詳細信息，請參閱存儲庫中的 LICENSE 文件。
