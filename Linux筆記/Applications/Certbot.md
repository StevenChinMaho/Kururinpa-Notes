## 1. Certbot 核心指令列表

這些是 Certbot 最基礎的操作指令，決定了你要對憑證執行什麼動作：

|**指令 (Command)**|**功能說明**|**適用情境**|
|---|---|---|
|`certbot`|啟動互動模式，申請憑證並**自動修改**伺服器設定。|新手或希望全自動處理的使用者。|
|`certbot certonly`|**僅申請憑證**，絕對不會更動任何網頁伺服器設定檔。|像你一樣，希望完全掌握並手動設定 Nginx/Apache 的開發者。|
|`certbot renew`|檢查本機所有憑證，並自動展延效期少於 30 天的憑證。|例行性更新（通常會寫在系統排程 Cron job 中）。|
|`certbot certificates`|列出目前 Certbot 管理的所有憑證名稱、包含的網域、存放路徑以及**剩餘有效天數**。|想確認憑證狀態或是否快過期時。|
|`certbot delete`|刪除指定的憑證，Certbot 未來將不再管理或更新它。|網站下線或不再需要該網域的 SSL 時。|
|`certbot revoke`|向 Let's Encrypt 官方宣告撤銷該憑證。|發生資安事件（如私鑰外洩）必須立即使憑證失效時。|

---

## 2. 常用附加參數 (Flags)

這些參數可以接在上述的主指令後面，用來精準控制 Certbot 的行為：

- **指定環境與網域**
    
    - `--nginx`：使用 Nginx 驗證模式（如果你用 `certonly`，它會短暫借用 Nginx 來驗證網域，但不會改設定）。
        
    - `--apache`：使用 Apache 驗證模式。
        
    - `-d 你的網域`：指定要申請的網域，可以串接多個。例如：`-d example.com -d www.example.com`。
        
- **測試與強制執行**
    
    - `--dry-run`：**模擬測試**。這非常實用！它會完整跑一次申請或更新的流程，但不會真的儲存憑證或發送次數限制到官方伺服器。例如：`sudo certbot renew --dry-run`。
        
    - `--force-renewal`：**強制更新**。即使憑證還有很久才過期，也強制重新申請一張新的。
        
- **自動化 Hook (鉤子) - 搭配 `certonly` 必學**
    
    - `--pre-hook "指令"`：在 Certbot 開始申請/更新前執行的系統指令（例如先暫停某個服務）。
        
    - `--post-hook "指令"`：在 Certbot 成功更新憑證後執行的指令。
        
    - **(重要)** `--deploy-hook "指令"`：只有在憑證「真的成功展延」時才會執行。

---

## 給手動設定派 (certonly) 的進階建議

使用 `certonly` 時，系統未來透過 `certbot renew` 自動更新憑證檔案後，Nginx 並不會自己知道要重新讀取新檔案。

為了避免憑證檔案更新了，但網站還是吃到舊憑證，可以在測試更新時加入 `--deploy-hook` 參數：

```bash
sudo certbot renew --dry-run --deploy-hook "systemctl reload nginx"
```

這樣設定後，未來 Certbot 只要成功換發新憑證，就會自動幫你 reload Nginx，做到既不亂改設定檔，又能全自動更新的完美狀態。