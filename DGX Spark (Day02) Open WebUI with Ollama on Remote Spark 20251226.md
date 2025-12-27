<sub><sup>This is an extension of my previous articles on the two Server/Client connection methods for DGX Spark: [Day01A: Remote Access from Internet Guide](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(Day01A)%20Remote%20Access%20Guide%2020251220A.md) and [Day01B: Local Access from Same Subnet Guide](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(Day01B)%EF%BC%9ALocal%20Access%20from%20Same%20Subnet%20Guide%2020251220B.md). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC connections. I hope this gives you more options for reference.</sup></sub>
# DGX Spark (Day02) Open WebUI with Ollama on Remote Spark 20251226
## 🟩 English
> ## Scenarios & Advantages
> **Mac/PC browser uses the Open WebUI interface → through the self-established remote connections → to run Ollama on DGX Spark Server**
> - **This is an extension of my previous articles on the two Server/Client connection methods for DGX Spark: [Day01A: Remote Access from Internet Guide](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(Day01A)%20Remote%20Access%20Guide%2020251220A.md) and [Day01B: Local Access from Same Subnet Guide](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(Day01B)%EF%BC%9ALocal%20Access%20from%20Same%20Subnet%20Guide%2020251220B.md). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC connections.**
>   - Guaranteed stability through the self-estabilished remote connections
>   - No reliance on NVIDIA SYNC
> - **Minor modifications to the NVIDIA official steps** 
>   - The official steps are built around NVIDIA SYNC connections; only three steps need to be changed to match the self-established remote connections.
> - Simple one-line **SSH** command **login to DGX Spark**
>   - After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.

---

## 打開網頁 NVIDIA [Open WebUI with Ollama : Set up WebUI on Remote Spark with NVIDIA Sync](https://build.nvidia.com/spark/open-webui/sync)
網頁中
## Step 1. 配置 Docker 權限
(步驟照做)

## Step 2. 驗證 Docker 設定，並拉取容器
(步驟照做)

---

## Step 3. 打開 NVIDIA SYNC 軟體的設定畫面
(不要做)(改成以下步驟)
## 改為 Step 3. Mac/PC Client 暫時登入 DGX Spark Server (未指定 Open WebUI 的通信 port number)
在 Mac/PC Client 上執行命令
###### 在 Mac/PC Client 上執行命令 執行後，會**看到終端機的命令提示字元變化**，從 Mac/PC Client機的 <本機用戶>@<本機名稱>%，變成 DGX Server 機的 <server機用戶>@Spark-xxxx:$，表示已登入。
```
# 把 <DGX Spark username> 包含括弧刪掉, 置換成 DGX Spark 開機後登入的 username
# 把 <192.168.x.x> 包含括弧刪掉, 置換成 DGX Spark 內網 IP 位址 (192.168.x.x) 的值
ssh <DGX Spark username>@<192.168.x.x>
```

---

## Step 4. 新增 Open WebUI 自訂埠配置
(不要做)(改成以下步驟)
## 改為 Step 4. 新增 Open WebUI 自訂埠配置 
### Step 4-1. 新增 Open WebUI 自訂埠配置
在 Mac/PC Client 上執行
```
docker run -d \
  --gpus all \
  -p 3000:8080 \
  -v ollama:/root/.ollama \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart unless-stopped \
  ghcr.io/open-webui/open-webui:ollama
```
- 解釋指令
  - **docker** 用 docker 指令
  - **run -d** 跑 containner 但別在terminal上顯示
  - **--gpus all** 用 NVIDIA DGX Spark 的 GPU 高速運算
  - **-p 3000:8080** 把實體 DGX Spark 的 3000 port 對應到 虛擬容器 container 的 8080 port (*註：DGX Spark 的 3000 port 這數字可以修改)
  - **-v ollama:/root/.ollama** 把實體 DGX Spark 的 ollama 目錄 掛載到 虛擬容器 container 的 /root/.ollama 目錄 (*註：實體 DGX Spark 目錄通常在 /var/lib/docker/volumes/...之下)
  - **-v open-webui:/app/backend/data** 把實體 DGX Spark 的 open-webui 目錄 掛載到 虛擬容器 container 的 /app/backend/data 目錄 (*註：實體 DGX Spark 目錄通常在 /var/lib/docker/volumes/...之下)
  - **--name open-webui** 命名容器為 open-webui
  - **--restart unless-stopped** 預設開機後自動啟動 container。但若關機前刻意下 docker stop 指令停止 container，則下次開機後不再自動啟動 container。(*註：亦可改成--restart unless-stopped. 永遠自動啟動)
  - **--restart always** 開機後自動啟動 container (*註：亦可改成--restart unless-stopped. 若刻意下docker stop指令停止container，則下次開機後不再自動啟動 container)
  - **ghcr.io/open-webui/open-webui:ollama** 用這個預先下載的 docker image，來做容器 container

### Step 4-2. 退出 Step 3 的那次暫時登入 DGX Spark Server (未指定 Open WebUI 的通信 port number)
在 Mac/PC Client 上執行
###### 在 Mac/PC Client 上執行命令 執行後，會**看到終端機的命令提示字元變化**，從 DGX Server 機的 <server機用戶>@Spark-xxxx:$，變成 Mac/PC Client機的 <本機用戶>@<本機名稱>%，表示已退出。
```
exit
```

### Step 4-3. Mac/PC Client 登入 DGX Spark Server (指定 Open WebUI 的通信 port number)
在 Mac/PC Client 上執行

<sub><sup>＊重開機之後，只要 Mac/PC (Client) 執行 `Step 4-3` 與 `Step 5`，超級簡單。</sup></sub>
```
ssh -4 -N -L 12000:0.0.0.0:3000 davislin@192.168.10.119
```

---

## Step 5. 啟動 Open WebUI
(步驟照做)

<sub><sup>＊重開機之後，只要 Mac/PC (Client) 執行 `Step 4-3` 與 `Step 5`，超級簡單。</sup></sub>

---

## Step 6. 創建管理員帳戶
(步驟照做)

---

## Step 7. 下載並配置模型
(步驟照做)

---

## Step 8. 測試模型
(步驟照做)

---

## Step 9. 停止 Open WebUI
(不要做)(改成以下步驟)
## 改為 Step 9. 停止 Open WebUI
在 Step 4-3 的終端機機畫面按 ```Ctrl+C``` 退出

---

## Step 10. 下一步
(步驟照做)

---

## Step 11. 清除與還原
(步驟照做) (**注意：Step 11 是在 DGX Spark Server 上執行的命令**)

---

##備註：

解釋 Step 4-2 指令

<sub><sup>**docker** 用 docker 指令</sup></sub>

**run -d** 跑 containner 但別在terminal上顯示

**--gpus all** 用 NVIDIA DGX Spark 的 GPU 高速運算

**-p 3000:8080** 把實體 DGX Spark 的 3000 port 對應到 虛擬容器 container 的 8080 port (*註：DGX Spark 的 3000 port 這數字可以修改)

**-v ollama:/root/.ollama** 把實體 DGX Spark 的 ollama 目錄 掛載到 虛擬容器 container 的 /root/.ollama 目錄 (*註：實體 DGX Spark 目錄通常在 /var/lib/docker/volumes/...之下)

**-v open-webui:/app/backend/data** 把實體 DGX Spark 的 open-webui 目錄 掛載到 虛擬容器 container 的 /app/backend/data 目錄 (*註：實體 DGX Spark 目錄通常在 /var/lib/docker/volumes/...之下)

**--name open-webui** 命名容器為 open-webui

**--restart unless-stopped** 預設開機後自動啟動 container。但若關機前刻意下 docker stop 指令停止 container，則下次開機後不再自動啟動 container。(*註：亦可改成--restart unless-stopped. 永遠自動啟動)

**--restart always** 開機後自動啟動 container (*註：亦可改成--restart unless-stopped. 若刻意下docker stop指令停止container，則下次開機後不再自動啟動 container)

**ghcr.io/open-webui/open-webui:ollama** 用這個預先下載的 docker image，來做容器 container
