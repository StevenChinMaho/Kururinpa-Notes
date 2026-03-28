Linux 上的壓縮工具繁多，但 `tar` 是最核心的萬用工具，其他如 `zip`、`rar` 則需要額外安裝。

### `tar` - 萬用打包工具

`tar` 本身只是將檔案打包成一個大檔，壓縮通常是搭配 gzip、bzip2 或 xz 演算法。

**參數速查表：**

|功能|參數|說明|
|---|---|---|
|**動作**|`-c`|建立壓縮檔 (Create)|
||`-x`|解開壓縮檔 (Extract)|
||`-t`|**預覽內容** (List)，不解壓縮|
|**格式**|`-z`|**gzip** (.tar.gz) - 最常用，速度快|
||`-j`|**bzip2** (.tar.bz2) - 壓縮率比 gzip 好，但稍慢|
||`-J`|**xz** (.tar.xz) - 壓縮率最高，壓縮/解壓最慢|
|**通用**|`-v`|顯示詳細過程 (Verbose)|
||`-f`|指定檔案 (File) - **務必放在參數最後面**|

#### 1. 基礎操作 (壓縮/解壓)

```bash
# 壓縮資料夾 (gzip 格式，最常用)
tar -czvf archive.tar.gz folder_name/

# 解壓縮 (現代 tar 夠聰明，不加 z/j/J 也能自動辨識格式)
tar -xvf archive.tar.gz
tar -xvf archive.tar.xz -C /target/path  # 解壓到指定目錄
```

#### 2. 預覽壓縮檔 (不解壓縮)

不想解開幾 GB 的大檔，只想確認裡面有沒有某個檔案時使用。

```bash
tar -tvf archive.tar.gz         # 列出所有檔案詳細資訊
tar -tf archive.tar.gz          # 只列出檔名
```

#### 3. 僅預覽第一層目錄 (進階技巧)

當壓縮檔內有成千上萬個檔案，`tar -tf` 會洗版。使用 `awk` 過濾只看第一層結構：

```bash
# 顯示壓縮檔根目錄下的資料夾與檔案
tar -tf archive.tar.gz | awk -F/ '{print $1}' | sort -u
```

#### 4. 分卷壓縮 (Split)

適用情境：檔案太大 (如 10GB)，受到 FAT32 (4GB 限制) 或雲端上傳限制，需切割成多個小檔。

**壓縮並切割：**

```bash
# 將 folder 打包，並切割成每個 100MB 的檔案 (檔名會是 backup.tar.gz.partaa, partab...)
tar -czvf - folder/ | split -b 100M - backup.tar.gz.part
```

**合併並解壓縮：**

```bash
# 使用萬用字元 * 讀取所有分卷，合併後交給 tar 解壓
cat backup.tar.gz.part* | tar -xzvf -
```

### 其他常見格式

#### `.zip` (跨平台通用)

需安裝：`sudo apt install zip unzip`

```bash
zip -r output.zip folder/       # 壓縮資料夾
unzip file.zip                  # 解壓縮
unzip -l file.zip               # 預覽內容 (List)
unzip file.zip -d /target/path  # 解壓到指定目錄
```

#### `.gz` / `.xz` (單一檔案壓縮)

注意：gzip 和 xz 預設只能壓縮「單一個檔案」，不能壓縮資料夾。若要壓縮資料夾，請使用 tar。

- **gzip (.gz):** 速度快，最普及。
    
    ```bash
    gzip file.txt        # 產生 file.txt.gz (原檔會消失)
    gunzip file.txt.gz   # 解壓縮
    zcat file.txt.gz     # 直接預覽純文字內容 (不解壓)
    ```
    
- **xz (.xz):** 壓縮率極高，常用於系統映像檔。
    
    ```bash
    xz file.txt          # 產生 file.txt.xz
    unxz file.txt.xz     # 解壓縮
    ```
    

#### `.7z` / `.rar` (Windows 常見)

需安裝額外套件才能使用。

- **7zip:** `sudo apt install p7zip-full`
    
    ```bash
    7z x file.7z         # 解壓縮 (包含目錄結構)
    ```
    
- **Rar:** `sudo apt install unrar`
    
    ```bash
    unrar x file.rar     # 解壓縮
    ```