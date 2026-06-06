# 🛡️ Ubuntu 隨身硬碟隔離與防毒掃描實戰筆記

## 一、環境準備與套件安裝

在插入可能有風險的硬碟前，確保系統的防毒軟體與病毒特徵庫處於最新且就緒的狀態。

**1. 安裝 ClamAV 與背景服務套件**

```bash
sudo apt update
sudo apt install clamav clamav-daemon
```

**2. 手動更新病毒庫** 為避免檔案鎖定衝突，更新前先暫停自動更新服務：

```bash
sudo systemctl stop clamav-freshclam 
sudo freshclam                       
sudo systemctl start clamav-freshclam
```

## 二、安全隔離掛載 (最高限制級別)

在插入隨身碟之前，必須確保圖形桌面（如 Gnome）不會自動掛載隨身碟：

### 修改 GNOME 桌面設定

打開桌面端的終端機，以**一般使用者**（不需要 sudo）輸入：

```bash
# 1. 禁止隨身碟插入時自動掛載
gsettings set org.gnome.desktop.media-handling automount false

# 2. 禁止掛載後自動開啟檔案管理員視窗
gsettings set org.gnome.desktop.media-handling automount-open false
```

設定完畢後，你插入任何有隨身硬碟，系統都不會有任何反應。

掃描完畢後，想恢復一般隨插即用的方便性，只要把 `false` 改回 `true` 即可：

```bash
gsettings set org.gnome.desktop.media-handling automount true
gsettings set org.gnome.desktop.media-handling automount-open true
```

### 插入隨身碟

核心精神：**唯讀、禁止執行、禁止提權**。

**1. 找出裝置代號** 

```bash
lsblk
```

**2. 建立專屬掛載點** 為每個需要掃描的分割區建立獨立目錄：

```bash
sudo mkdir -p /mnt/sus_usb
```

**3. 套用嚴格安全參數掛載** 將分割區掛載至目錄，切斷任何惡意程式自動執行的可能：

```bash
sudo mount -o ro,noexec,nodev,nosuid /dev/sdb1 /mnt/sus_usb
```

- **`ro`**：唯讀模式 (Read-Only)。
    
- **`noexec`**：禁止執行任何二進制檔案或腳本。
    
- **`nodev`**：禁止解析特殊裝置檔。
    
- **`nosuid`**：忽略權限提升位元。
    

## 三、一般掃描模式 (單執行緒 `clamscan`)

適合掃描**小容量硬碟**或**單一資料夾**。此模式每次執行都會重新載入病毒庫，且僅使用單一 CPU 核心，速度較慢。

**掃描指令：**

```bash
clamscan -r -i /mnt/sus_usb
```

- **`-r`**：遞迴掃描目錄下的所有檔案。
    
- **`-i`**：僅輸出受感染的檔案 (Infected)，保持終端機畫面整潔。
    

## 四、高效能多執行緒掃描 (背景服務 `clamdscan`)

針對大容量硬碟，可以改用常駐服務進行平行掃描。

**1. 最佳化執行緒設定** 編輯 ClamAV 背景服務設定檔，釋放多核心效能：

```bash
sudo vim /etc/clamav/clamd.conf
```

確保或新增以下參數（依據硬體條件調整）：

```text
MaxThreads 16
MaxQueue 200
```

**2. 啟動並確認背景服務 (Daemon)** 重新載入設定並啟動服務：

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now clamav-daemon
```

> **💡 關鍵提示：** 啟動後，使用 `htop` 觀察名為 `clamd` 的程序。它需要約 30 秒至 1 分鐘將龐大的病毒庫完整載入記憶體（約佔用 1.5GB ~ 2GB）。必須等其 CPU 與 RAM 使用量穩定後，才能進行下一步，否則會報錯 `Connection refused`。

**3. 執行多執行緒掃描**

```bash
sudo clamdscan --fdpass --multiscan /mnt/sus_usb
```

- **`--multiscan`**：啟用多執行緒平行處理，大幅縮短掃描時間。
    
- **`--fdpass`**：傳遞檔案描述符。因為掛載點權限極度嚴格，必須透過此參數讓 `sudo` 權限協助背景的 `clamav` 使用者讀取檔案。
    

## 五、安全卸載與系統收尾

看到掃描報告顯示 `Infected files: 0` 且硬碟讀取燈號停止閃爍後，即可安全撤除隔離環境。

**1. 卸載所有分割區** 從最內層的掛載點開始解除：

```bash
sudo umount /mnt/sus_usb
# 若有多個分割區，請依序 umount
```

**2. 刪除掛載點目錄** 移除我們建立的暫時性資料夾，保持系統乾淨：

```bash
sudo rmdir /mnt/sus_usb
```

此時，隨身硬碟已可安全拔除。