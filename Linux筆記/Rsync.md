`rsync` 是 Linux 下最強大的備份工具，比起 `cp`，它支援**增量備份**（只傳輸變更的部分）、斷點續傳，且可透過 SSH 進行遠端同步。

|參數|說明|
|---|---|
|`-a`|**歸檔模式** (Archive)，等於 `-rlptgoD`，保留所有權限、時間、連結等 (最常用)|
|`-v`|顯示詳細過程 (Verbose)|
|`-z`|傳輸時進行**壓縮** (Compress)，適合遠端傳輸以節省頻寬|
|`-P`|顯示**進度條** (Progress) 與支援斷點續傳|
|`--delete`|**同步刪除**：若來源端檔案刪除了，目的端也會跟著刪除 (請小心使用！)|

### 本地同步 (類似 cp 但更強)

```bash
rsync -av source_folder/ dest_folder/    # 將 source 內的內容同步到 dest
```

> **⚠️ 注意路徑結尾斜線 `/` 的差異：**
> 
> - `source/` (有斜線)：複製目錄**裡面**的內容。
>     
> - `source` (無斜線)：連同目錄本身一起複製過去。
>     

### 遠端同步 (透過 SSH)

```bash
# 推送 (Push)：從本地傳到遠端
rsync -avzP local_folder/ user@remote_ip:/path/to/dest/

# 拉取 (Pull)：從遠端傳回本地
rsync -avzP user@remote_ip:/path/to/source/ local_folder/
```

### 完整備份網站範例 (Mirror)

```bash
# 將 /var/www/html 完整鏡像備份到 /backup，並刪除備份端多餘的檔案
rsync -av --delete /var/www/html/ /backup/html_mirror/
```

### 實戰補充：備份到 Windows/外接硬碟 (WSL 適用)

備份到 NTFS 或 exFAT 格式的硬碟時，Linux 的權限系統無法直接對應，這時必須加上 `--no-owner --no-group` 參數，否則會出現大量權限錯誤。

```bash
# 將 WSL 中的 Minecraft 伺服器備份到 Windows 的 E 槽
# 注意：來源端沒有斜線 (備份整個資料夾)，目的端有斜線 (放到 E 槽裡面)
rsync -av --no-owner --no-group /mnt/wsl/disk/minecraft_server /mnt/e/
```