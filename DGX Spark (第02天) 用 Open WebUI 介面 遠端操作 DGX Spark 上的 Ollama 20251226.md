<sub><sup>這是我前兩篇文章 DGX Spark : [第01天A: 外網遠端操控 指南](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) 與 [第01天B: 同子網內網操控 指南](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md) 兩種 Server/Client 連線方式的延伸文章。以下，我將在不使用 NVIDIA SYNC app 做連線的前提，修改 DGX Spark 建立 Open WebUI 的 NVIDIA官方步驟。希望能給你更多方式參考。</sup></sub>

# DGX Spark (第02天) 用 Open WebUI 介面 遠端操作 DGX Spark 上的 Ollama 20251226
## 🟩 中文版
> ## 適用情境 與 優點
> **Mac/PC Client 開瀏覽器在 Open WebUI 介面上 → 透過自己建立的遠端連線 → 用 DGX Spark Server 的算力跑 Ollama**
> - **基於前一篇文章 [第01天A: 外網遠端操控 指南](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) 或 [第01天B: 同子網內網操控 指南](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md) 的連線方式**
>   - **100% 連線成功率與穩定度，自己掌握 Server/Client 連線的設定細節**
>   - 不使用 NVIDIA SYNC app 的連線方式
> - **小修改 NVIDIA官方步驟** 
>   - 官方步驟是基於 NVIDIA SYNC app 連線的，只修改三個步驟就能匹配 自己建立的遠端連線
> - **SHH 一行指令登入 DGX Spark**
>   - 重開機之後，只要 Mac/PC (Client) 執行 `Step 4-3` 與 `Step 5`，超級簡單。

---

## 打開 NVIDIA DGX Spark 網頁 [Open WebUI with Ollama : Set up WebUI on Remote Spark with NVIDIA Sync](https://build.nvidia.com/spark/open-webui/sync)
網頁中
## Step 1. 配置 Docker 權限
(步驟照做)

## Step 2. 驗證 Docker 設定，並拉取容器
(步驟照做)

---

## Step 3. 打開 NVIDIA SYNC 軟體的設定畫面
(不要做)(改成以下步驟)
## 改為 Step 3. Mac/PC Client 暫時登入 DGX Spark Server (Client端 未指定 Open WebUI 的通信 port number)
在 Mac/PC Client 上執行命令
###### 執行命令後，會看到終端機的命令提示字元變化，從 Mac/PC Client機的 <本機用戶>@<本機名稱>%，變成 DGX Server 機的 <server機用戶>@Spark-xxxx:$，表示已登入。
```
# 把 <DGX Spark username> 包含括弧刪掉, 置換成 DGX Spark 開機後登入的 username
# 把 <192.168.x.x> 包含括弧刪掉, 置換成 DGX Spark 內網 IP 位址 (192.168.x.x) 的值
ssh <DGX Spark username>@<192.168.x.x>
```

---

## Step 4. 新增 Open WebUI 自訂埠配置
(不要做)(改成以下步驟)
## 改為 Step 4. 新增 Open WebUI 自訂埠配置 
### 改為 Step 4-1. 新增 Open WebUI 自訂埠配置
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
  - **--restart unless-stopped** 預設開機後自動啟動 container。但若關機前刻意下 docker stop 指令停止 container，則下次開機後不再自動啟動 container。(*註：亦可改成--restart always. 永遠自動啟動)
  - **ghcr.io/open-webui/open-webui:ollama** 使用 Docker image ghcr.io/open-webui/open-webui:ollama 來運行容器

### 改為 Step 4-2. 退出 Step 3 的那次暫時登入 DGX Spark Server (未指定 Open WebUI 的通信 port number)
在 Mac/PC Client 上執行
###### 在 Mac/PC Client 上執行命令 執行後，會**看到終端機的命令提示字元變化**，從 DGX Server 機的 <server機用戶>@Spark-xxxx:$，變成 Mac/PC Client機的 <本機用戶>@<本機名稱>%，表示已退出。
```
exit
```

### 改為 Step 4-3. Mac/PC Client 重新登入 DGX Spark Server (這次 Client端 有指定 Open WebUI 的通信 port number 12000)
在 Mac/PC Client 上執行
```
# 把 <DGX Spark username> 包含括弧刪掉, 置換成 DGX Spark 開機後登入的 username
# 把 <192.168.x.x> 包含括弧刪掉, 置換成 DGX Spark 內網 IP 位址 (192.168.x.x) 的值
ssh -4 -N -L 12000:0.0.0.0:3000 <DGX Spark username>@<192.168.x.x>
```
<sub><sup>＊重開機之後，只要 Mac/PC (Client) 執行 `Step 4-3` 與 `Step 5`，超級簡單。</sup></sub>

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
在 Step 4-3 的終端機機畫面按 `Ctrl+C` 退出

*這將終止 SSH 隧道，停止本地埠轉發，並關閉對 DGX Spark 伺服器上 Open WebUI 埠的存取。

--

## Step 10. 下一步
(步驟照做)

---

## Step 11. 清除與還原
(步驟照做) (**警告：不可**隨意清除與還原，且 Step 11 是在 DGX Spark Server 上執行的命令.)

---

# **恭喜你！從此你能在 Mac/PC，用 DGX Spark 的 GPU 算力，開網頁跑 Ollama 了！**
<sub><sup>＊重開機之後，只要 Mac/PC (Client) 執行 `Step 4-3` 與 `Step 5`，超級簡單。</sup></sub>

---
