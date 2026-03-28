### `chmod` - 修改權限

```bash
chmod 777 file.sh    # 所有人可讀可寫可執行 (危險，慎用)
chmod 755 file.sh    # 擁有者全開，其他人唯讀+執行 (常用於腳本/網頁)
chmod +x file.sh     # 賦予執行權限
```

### `chown` - 修改擁有者

```bash
sudo chown user:group file.txt        # 修改檔案擁有者與群組
sudo chown -R www-data:www-data /var/www/html  # 遞迴修改網站目錄權限
```