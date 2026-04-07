在較新的 Ubuntu 版本中，為了避免弄壞系統的 Python 環境 (PEP 668)，系統會阻止你直接使用 `pip install` 全域安裝套件，並報錯 `externally-managed-environment`。

`pipx` 就是官方推薦的最佳解法，它會幫每一個 Python CLI 工具建立獨立的虛擬環境，互不干擾。

### 安裝與設定

```
sudo apt update
sudo apt install pipx
pipx ensurepath      # 將 pipx 加入環境變數 (執行後需重啟終端機或重新登入)
```

### 常用操作

```
pipx install [套件名]     # 安裝 Python CLI 工具 (例如: pipx install yt-dlp)
pipx list                # 列出所有透過 pipx 安裝的工具
pipx upgrade [套件名]     # 更新特定工具
pipx upgrade-all         # 一鍵更新所有 pipx 安裝的工具
pipx uninstall [套件名]   # 移除工具
pipx run [套件名]         # 不安裝直接在暫存環境執行 (類似 Node.js 的 npx)
```