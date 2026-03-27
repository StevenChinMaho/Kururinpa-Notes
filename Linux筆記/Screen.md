`screen` 允許你在 SSH 斷線後讓程式繼續在背景執行，或是在一個連線中開啟多個視窗。

**基礎操作：**

- **建立新工作階段：**
    
    ```bash
    screen -S session_name  # 建立一個名為 session_name 的視窗
    ```
    
- **暫時離開 (Detach)：**
    
    - 按下 `Ctrl + A`，放開後再按 `d`。
        
    - (程式會繼續在背景跑，你會回到原本的 Shell)。
        
- **列出所有工作階段：**
    
    ```bash
    screen -ls
    ```
    
- **恢復工作階段 (Reattach)：**
    
    ```bash
    screen -r session_name
    # 若該 session 狀態是 Attached (被佔用)，可強制踢掉對方並登入：
    screen -d -r session_name
    ```
    
- **關閉工作階段：**
    
    - 進入 screen 後輸入 `exit`。
        
    - 或在外部執行：`screen -X -S session_name quit`。