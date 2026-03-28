UFW (Uncomplicated Firewall) 是 Ubuntu 預設的防火牆工具，操作簡單但功能強大。

**基本操作：**

```bash
sudo ufw enable          # 啟用防火牆 (系統會自動開機啟動)
sudo ufw disable         # 停用防火牆
sudo ufw status          # 查看狀態
sudo ufw status numbered # 查看狀態並顯示規則編號 (刪除規則時好用)
```

**規則管理 (Allow/Deny)：**

```bash
sudo ufw allow 22        # 開放 SSH Port (非常重要，啟用前請先允許 SSH)
sudo ufw allow 80        # 開放 HTTP
sudo ufw allow 443       # 開放 HTTPS
sudo ufw allow from 192.168.1.100  # 僅允許特定 IP 連線

# 刪除規則 (搭配 numbered 狀態使用)
sudo ufw delete [規則編號] 
# 例如: sudo ufw delete 2
```