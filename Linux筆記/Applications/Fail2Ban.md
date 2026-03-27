Fail2Ban 用於監控 log 檔，當發現惡意登入行為 (如 SSH 暴力破解) 時自動 Ban 掉對方 IP。

**服務管理：**

```bash
sudo systemctl start fail2ban    # 啟動服務
sudo systemctl enable fail2ban   # 設定開機自動啟動
sudo systemctl status fail2ban   # 查看服務運作狀態
```

**Client 指令 (fail2ban-client)：**

```bash
# 查看整體狀態 (有哪些 Jail 正在運作)
sudo fail2ban-client status

# 查看特定 Jail 的詳細狀態 (例如 sshd)
# 可以看到目前 Ban 了多少 IP，以及被 Ban 的 IP 列表
sudo fail2ban-client status sshd

# 手動解鎖 (Unban) 被誤鎖的 IP
sudo fail2ban-client set sshd unbanip 192.168.1.50
```