### `top` / `htop` - 系統即時監控

- `top`：內建的監控工具。
    
- `htop`：圖形化介面，更直觀 (推薦，需安裝 `sudo apt install htop`)。
    

### `ps` - 查看行程快照

```bash
ps aux              # 顯示系統所有使用者執行的行程
ps aux | grep nginx # 搜尋特定程式 (如 nginx)
```

### `kill` - 終止行程

```bash
kill 1234           # 終止 PID 為 1234 的行程
kill -9 1234        # 強制終止 (SIGKILL)
killall nginx       # 終止所有名為 nginx 的行程
```

### `free` - 查看記憶體使用量

```bash
free -h             # 以人類可讀格式顯示 RAM 與 Swap 使用量
```