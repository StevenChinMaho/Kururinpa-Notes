在 Linux 終端機用 `wget` 或 `curl` 下載 Google Drive 大檔案時，常會因為 Google 的「防毒掃描警告」頁面而導致下載失敗。`gdown` 專門用來解決這個問題，它是基於 Python 的工具，強烈建議使用 `pipx` 安裝。

### 安裝

```
pipx install gdown
```

### 常用指令

```
# 基本下載 (貼上分享連結即可)
gdown "[https://drive.google.com/file/d/檔案ID/view?usp=sharing](https://drive.google.com/file/d/檔案ID/view?usp=sharing)"

# 透過檔案 ID 下載 (比較簡潔)
gdown --id 1_abc123xyz456...

# 下載整個資料夾 (需加上 --folder 參數)
gdown --folder "[https://drive.google.com/drive/folders/資料夾ID](https://drive.google.com/drive/folders/資料夾ID)"

# 處理難搞的網址
# 若遇到權限問題或下載失敗，可嘗試加上 --fuzzy (模糊匹配) 來自動解析網址
gdown --fuzzy "網址"
```