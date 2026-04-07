當您需要與遠端 Linux 伺服器傳輸檔案時，除了 `rsync` 之外，最常見且系統內建的工具就是 `scp` 與 `sftp`。兩者都是基於安全加密的 SSH 協定。

### `scp` - 終端機快速複製 (Secure Copy)

指令邏輯與本地端的 `cp` 幾乎一模一樣，只是路徑變成了 `使用者名稱@IP位置:路徑`。

|參數|說明|
|---|---|
|`-r`|遞迴複製 (傳輸整個資料夾時必加)|
|`-P`|指定 SSH Port (注意是大寫 P，與 ssh 指令的小寫 -p 不同)|
|`-i`|指定 SSH 私鑰檔案位置|

**常用情境：**

```
# 1. 上傳 (Push)：從本地傳到遠端
scp local_file.txt user@192.168.1.100:/home/user/dest_folder/

# 2. 下載 (Pull)：從遠端傳回本地
scp user@192.168.1.100:/home/user/remote_file.txt /local/folder/

# 3. 傳輸整個資料夾 (加上 -r)
scp -r local_folder/ user@192.168.1.100:/home/user/

# 4. 如果遠端伺服器更改了 SSH Port (例如 2222)
scp -P 2222 local_file.txt user@192.168.1.100:/home/user/
```

### `sftp` - 互動式檔案傳輸 (Secure FTP)

如果您需要像使用傳統 FTP 軟體 (如 FileZilla) 那樣，先連線進去看看目錄結構再決定要抓什麼檔案，`sftp` 會是更好的選擇。

**連線：**

```
sftp user@192.168.1.100
# 若有改 Port： sftp -P 2222 user@192.168.1.100
```

**進入 sftp 互動介面後的常用指令：**

```
# 遠端操作 (與平常 Linux 指令相同)
ls          # 列出遠端目錄檔案
pwd         # 顯示遠端目前路徑
cd folder/  # 切換遠端目錄

# 本地端操作 (指令前面加一個 l，代表 local)
lls         # 列出本地端目錄檔案
lpwd        # 顯示本地端目前路徑
lcd folder/ # 切換本地端目錄

# 上傳與下載
put file.txt        # 上傳檔案 (從本地 lpwd 上傳到遠端 pwd)
put -r folder/      # 上傳整個資料夾
get file.txt        # 下載檔案 (從遠端 pwd 下載到本地 lpwd)
get -r folder/      # 下載整個資料夾

exit        # 離開 sftp 模式
```