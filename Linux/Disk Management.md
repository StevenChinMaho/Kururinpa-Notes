### `df` - 檢視檔案系統磁碟空間 (Disk Free)

用於查看整體硬碟掛載點的剩餘空間。

|參數|說明|
|---|---|
|`-h`|Human-readable，以 GB/MB 顯示|
|`-T`|顯示檔案系統類型 (ext4, xfs, tmpfs 等)|

**常用組合：**

```bash
df -hT
```

### `du` - 檢視檔案/目錄佔用空間 (Disk Usage)

用於找出是哪個資料夾佔滿了硬碟。

|參數|說明|
|---|---|
|`-s`|Summary，僅顯示總計，不列出子目錄細節|
|`-h`|Human-readable|
|`-d 1`|指定目錄深度 (例如只看第一層子目錄)|

**常用情境：**

```bash
du -sh folder_name/      # 查看某個資料夾總共佔多大
du -h -d 1 /var/log/     # 查看 /var/log 下第一層目錄各自的大小
```

### `lsblk` - 列出區塊裝置 (List Block Devices)

以樹狀圖顯示硬碟與分割區的關係，非常直觀。

```bash
lsblk        # 顯示所有儲存裝置
lsblk -f     # 額外顯示檔案系統類型 (FSTYPE) 與 UUID (掛載時很有用)
```

### `fdisk` - 磁碟分割工具

⚠️ 此操作需 root 權限，且操作不當可能導致資料遺失。

```bash
sudo fdisk -l        # 列出所有磁碟分割表詳細資訊
sudo fdisk /dev/sdb  # 進入 /dev/sdb 的互動式分割介面
```

### `mount` / `umount` - 掛載與卸載

```bash
# 掛載 (暫時性，重開機後失效)
sudo mount /dev/sdb1 /mnt/usb    # 將 sdb1 掛載到 /mnt/usb

# 卸載 (注意：不能在該目錄內執行卸載)
sudo umount /dev/sdb1            # 或是 sudo umount /mnt/usb
```