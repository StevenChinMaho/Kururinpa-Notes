開發者必備工具。

### 初始化與設定 (Config & Init)

```bash
git config --global user.name "Your Name"    # 設定使用者名稱
git config --global user.email "mail@ex.com" # 設定 Email
git config --list                            # 查看目前的設定
git init                                     # 在目前目錄初始化一個 Git 儲存庫
git clone [repo_url]                         # 從遠端複製儲存庫下來
```

### 基礎操作 (Workflow)

```bash
git status                   # 查看目前檔案狀態 (最常用！)
git add .                    # 將所有變更加入暫存區 (Stage)
git add file.txt             # 僅加入特定檔案
git commit -m "commit訊息"   # 提交變更
git diff                     # 查看工作目錄與暫存區的差異
```

### 分支管理 (Branch)

```bash
git branch                   # 列出所有分支 (有 * 號代表當前所在)
git branch new-feature       # 建立一個名為 new-feature 的新分支
git checkout new-feature     # 切換到該分支
git switch new-feature       # (新版指令) 切換分支，比 checkout 更直觀
git checkout -b fix-bug      # 建立並同時切換到 fix-bug 分支
git merge new-feature        # 將 new-feature 分支合併到目前所在分支
```

### 遠端同步 (Remote)

```bash
git remote -v                # 查看遠端倉庫列表
git pull origin main         # 拉取遠端更新並合併 (Fetch + Merge)
git fetch origin             # 只拉取更新，不合併 (比較安全)
git push origin main         # 推送本地變更到遠端
```

### 合併衝突救星 (Merge Conflict)

當 `git merge` 或 `git pull` 出現 `CONFLICT` 時，Git 會暫停合併動作。

1. **查看衝突檔案：**
    
    ```bash
    git status                   # 紅色的檔案就是有衝突的
    ```
    
2. **手動解決：**
    
    - 打開檔案，搜尋 `<<<<<<<`，你會看到類似：
        
        ```bash
        <<<<<<< HEAD
        原本的程式碼
        =======
        新進來的程式碼
        >>>>>>> new-feature
        ```
        
    - 刪除標記，保留你想要的程式碼。
        
3. **完成合併：**
    
    ```bash
    git add file.txt             # 標記該檔案已解決
    git commit                   # 提交合併 (通常不加 -m，使用預設訊息)
    ```
    
4. **放棄治療 (終止合併)：**
    
    ```bash
    git merge --abort            # 回到合併前的狀態，當作沒發生過
    ```
    

### 後悔藥與暫存 (Undo & Stash)

```bash
git log --oneline --graph    # 以圖形化精簡顯示提交紀錄
git reset --soft HEAD~1      # 撤銷上一次 commit，但保留檔案變更 (常用於修錯 commit 訊息)
git reset --hard HEAD~1      # 強制撤銷上一次 commit，**檔案變更會全部消失** (慎用)
git checkout -- file.txt     # 捨棄某個檔案的修改 (還原成上次 commit 的狀態)
git restore file.txt         # (新版指令) 同上，還原檔案

# 臨時切換工作時超好用：
git stash                    # 暫存目前所有修改 (工作目錄變乾淨)
git stash pop                # 取出剛才暫存的修改
```