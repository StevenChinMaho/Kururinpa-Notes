### 1. 映像檔管理 (Images)

映像檔就像是虛擬機的安裝光碟（或藍圖），用來建立容器。

```bash
# 從 Docker Hub 下載映像檔 (例如下載最新的 nginx)
docker pull nginx:latest

# 列出本地端所有已下載的映像檔
docker images
# 或
docker image ls

# 刪除指定的映像檔 (需先確認沒有容器正在使用它)
docker rmi [映像檔ID 或 映像檔名稱]

# 根據目前目錄的 Dockerfile 建立新的映像檔 (-t 用來加上名稱與標籤)
docker build -t my-custom-app:1.0 .
```

### 2. 容器生命週期管理 (Containers)

容器是從映像檔跑起來的實體，您可以把它想像成輕量級的虛擬機。

```bash
# 建立並啟動一個容器 (最常用的指令)
docker run [參數] [映像檔名稱]

  # 常用參數說明：
  # -d : 在背景執行 (Detached mode)
  # -p 8080:80 : 將本機的 8080 Port 對應到容器內的 80 Port
  # --name my-nginx : 幫容器取一個好記的名字
  # -v /host/path:/container/path : 掛載本地目錄到容器內 (Volume)
  
  # 範例：在背景啟動 nginx，命名為 web-server，並綁定 8080 port
  docker run -d -p 8080:80 --name web-server nginx

# 列出「正在執行」的容器
docker ps

# 列出「所有」容器 (包含已停止的)
docker ps -a

# 啟動已存在的容器
docker start [容器ID 或 名稱]

# 停止正在執行的容器 (會給容器時間安全關閉)
docker stop [容器ID 或 名稱]

# 強制終止容器 (不建議，除非 stop 無效)
docker kill [容器ID 或 名稱]

# 重新啟動容器
docker restart [容器ID 或 名稱]

# 刪除已停止的容器
docker rm [容器ID 或 名稱]

# 強制刪除正在執行的容器 (加上 -f 參數)
docker rm -f [容器ID 或 名稱]
```

### 3. 容器互動與除錯

當容器跑起來後，您可能會需要看日誌或進去容器內下指令。

```bash
# 查看容器的輸出日誌 (Log)
docker logs [容器ID 或 名稱]
docker logs -f [容器ID 或 名稱]  # 加上 -f 可以持續追蹤最新的日誌 (類似 tail -f)

# 進入正在執行的容器內部 (開啟互動式終端機)
# 通常使用 /bin/bash 或 /bin/sh (依據映像檔內建的 shell 而定)
docker exec -it [容器ID 或 名稱] /bin/bash

# 查看容器內部的詳細設定與資訊 (輸出為 JSON 格式)
docker inspect [容器ID 或 名稱]

# 查看容器的資源使用狀態 (CPU、記憶體即時監控)
docker stats
```

### 4. 系統清理 (System Cleanup)

Docker 用久了會產生很多沒有在用的快取、停止的容器或懸空 (dangling) 的映像檔，定期清理可以釋放硬碟空間。

```bash
# 清除所有已停止的容器
docker container prune

# 清除所有未被容器使用的映像檔
docker image prune

# 一鍵大掃除 (非常實用！會清除所有停止的容器、未使用的網路、懸空的映像檔與快取)
docker system prune

# 更深度的清理 (包含所有未被「任何」容器使用的映像檔，請小心使用)
docker system prune -a
```

### 💡 實用小技巧

在使用 Docker 指令時，不需要輸入完整的「容器 ID」或「映像檔 ID」，通常只要輸入前 3 到 4 個字元，只要能讓系統辨識出唯一值即可執行指令（例如：`docker stop a1b2`）。