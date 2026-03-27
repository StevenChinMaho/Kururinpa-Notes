### `grep` - 檔案內容搜尋

Linux 最強大的文字搜尋工具。

|參數|說明|
|---|---|
|`-r`|遞迴搜尋目錄下的所有檔案|
|`-i`|忽略大小寫|
|`-n`|顯示行號|
|`-v`|反向搜尋 (顯示**不**包含關鍵字的行)|

**常用情境：**

```bash
grep -r "error" /var/log/   # 在 log 資料夾中搜尋所有包含 error 的文字
grep "model name" /proc/cpuinfo # 查看 CPU 型號
```

### `find` - 檔案名稱/屬性搜尋

```bash
find /etc -name "host*"     # 在 /etc 下搜尋檔名開頭為 host 的檔案
find . -type f -size +100M  # 搜尋目前目錄下大於 100MB 的檔案
```

### `diff` - 檔案與目錄比對

用於比較兩個檔案或目錄的內容差異，在修改設定檔或程式開發時非常常用。

|參數|說明|
|---|---|
|`-u`|**統一格式 (Unified format)**，以 Git 風格顯示差異（最推薦、最好讀）|
|`-y`|左右並列顯示差異 (Side-by-side)|
|`-r`|遞迴比較兩個目錄下的所有檔案|
|`-w`|忽略所有的空白字元差異|
|`-i`|忽略大小寫差異|

**常用情境：**

```bash
diff old.txt new.txt           # 基本比對
diff -u old.txt new.txt        # 以 Git 風格的統一格式顯示差異 (推薦)
diff -y file1.txt file2.txt    # 左右並排檢視差異 (適合視窗夠寬的終端機)
diff -r dir1/ dir2/            # 比較兩個資料夾的內容差異
```