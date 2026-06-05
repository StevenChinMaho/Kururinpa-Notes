# Ubuntu 桌面環境 (GUI) 與運行目標 (Target) 管理詳解

在現代的 Ubuntu (使用 systemd) 中，系統的運行狀態被定義為不同的「目標 (Target)」。

- `multi-user.target`：多使用者純文字模式（適合伺服器，等同於傳統的 Runlevel 3）。
    
- `graphical.target`：圖形化介面模式（適合一般桌面操作，等同於傳統的 Runlevel 5）。
    

## 1. 查詢與設定開機預設狀態 (永久生效)

_這些指令會建立或修改系統的啟動連結 (symlink)，決定下次開機時的預設狀態。_

- **查詢目前的開機預設目標**
    
```bash
systemctl get-default
```

- **設定開機進入純文字模式 (關閉桌面)**

```bash
sudo systemctl set-default multi-user.target
```


> **說明**：執行後，下次開機會停在 TTY 終端機登入畫面。Nginx、Docker 等系統服務依然會在背景正常啟動。


- **設定開機進入圖形介面 (開啟桌面)**

```bash
sudo systemctl set-default graphical.target
```
    
---

## 2. 立即完整切換系統狀態 (Isolate - 單次生效)
*`isolate` 指令會立即啟動目標層級需要的所有服務，並**強制關閉**不屬於該層級的服務。這比單純關閉顯示管理器更徹底。*

*   **立即切換至純文字模式**
```bash
sudo systemctl isolate multi-user.target
```

> **說明**：如果你目前在桌面上，執行此指令會立刻關閉所有圖形應用程式，將畫面切回 TTY 純文字介面。

- **立即切換至圖形介面**
    
```bash
sudo systemctl isolate graphical.target
```

    > **說明**：如果你目前在純文字介面，且系統有安裝桌面環境，執行此指令會立刻載入所有 GUI 服務並進入圖形登入畫面。

---

## 3. 單純控制顯示管理器 (Display Manager)
*如果你不想改變整個系統的 Target，只想單純地把「負責畫面的服務」開起來或關掉，可以直接控制顯示管理器 (Ubuntu 預設為 `gdm3`)。*

*   **手動啟動桌面服務**
  
```bash
sudo systemctl start gdm3
```

- **手動停止桌面服務**
    
```bash
sudo systemctl stop gdm3
```

*   **重新啟動桌面服務 (當 GUI 卡死但 TTY 還能用時)**
```bash
sudo systemctl restart gdm3
```

- **查看桌面服務的詳細運行狀態**
    
```bash
systemctl status gdm3
```

> **💡 顯示管理器對照表：**
> 如果你安裝了不同的桌面環境，請將上方指令的 `gdm3` 替換為：
> *   **GNOME (Ubuntu 預設)**：`gdm3`
> *   **KDE Plasma**：`sddm`
> *   **XFCE (Xubuntu)**：`lightdm`
> *   **MATE / Cinnamon**：`lightdm` 或 `slick-greeter`

---

## 4. 實用快捷鍵：虛擬終端機 (TTY) 切換
當桌面完全卡死，或是你在純文字模式下需要多個視窗時，可以使用實體鍵盤的快捷鍵切換不同的虛擬終端機。

*   **切換到純文字 TTY 終端機 (1~6)**
    按下 `Ctrl` + `Alt` + `F3` (F3 到 F6 皆可，會對應到 tty3 ~ tty6)。
*   **切回圖形介面 (GUI)**
    按下 `Ctrl` + `Alt` + `F1` 或 `F2` (通常 gdm3 會運行在 tty1 或 tty2，視 Ubuntu 版本而定)。