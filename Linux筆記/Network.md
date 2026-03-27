Ubuntu 新版推薦使用 `ip` 指令取代舊的 `ifconfig`，用 `ss` 取代 `netstat`。

### `ip` - 網路介面與路由

|指令|說明|舊指令對照|
|---|---|---|
|`ip a` (或 `ip addr`)|顯示所有網卡 IP 資訊|`ifconfig`|
|`ip link`|顯示網卡 MAC 與狀態 (UP/DOWN)|`ifconfig -a`|
|`ip route`|顯示路由表 (Default Gateway)|`route -n`|
|`ip -s link`|顯示網卡傳輸統計 (RX/TX 封包數)|`netstat -i`|

### `ss` - Socket 統計 (查看 Port 佔用)

|參數|說明|
|---|---|
|`-t`|TCP|
|`-u`|UDP|
|`-l`|Listening (正在監聽的 Port)|
|`-p`|顯示佔用該 Port 的 Process 名稱 (需 sudo)|
|`-n`|不解析服務名稱，直接顯示 Port 號碼|

**常用組合：**

```bash
sudo ss -tulpn       # 查詢本機所有正在監聽的 TCP/UDP Port 及其對應程式
sudo ss -tulpn | grep 8080  # 查詢 Port 8080 是誰在用
```

### 其他常用網路工具

```bash
ping google.com -c 4      # Ping 4 次後停止
curl ifconfig.me          # 查詢本機對外的 Public IP
wget [url]                # 下載檔案
```