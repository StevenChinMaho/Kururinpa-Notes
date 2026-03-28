常用的 Web Server 管理指令，適用於網站架設與維護。

**服務控制：**

```bash
sudo systemctl start nginx       # 啟動 Nginx
sudo systemctl stop nginx        # 停止 Nginx
sudo systemctl restart nginx     # 重啟 (會中斷連線)
sudo systemctl reload nginx      # 重新載入設定檔 (不會中斷連線，修改設定後常用)
```

**設定檔檢查 (最重要)：**

修改完 `/etc/nginx/` 下的設定檔後，**務必**先執行此指令檢查語法是否正確，避免重啟失敗導致網站掛掉。

```bash
sudo nginx -t
```

**Log 檔位置 (預設)：**

- **存取紀錄 (Access Log):** `/var/log/nginx/access.log`
    
- **錯誤紀錄 (Error Log):** `/var/log/nginx/error.log`
    

```bash
# 即時查看最新的存取紀錄
tail -f /var/log/nginx/access.log
```