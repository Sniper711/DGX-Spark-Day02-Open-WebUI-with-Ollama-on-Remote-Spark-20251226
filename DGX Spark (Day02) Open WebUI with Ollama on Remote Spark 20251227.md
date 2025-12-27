<sub><sup>這是我前一篇文章 DGX Spark : [第01天A: 外網遠端操控 指南](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) 與 [第01天B: 同子網內網操控 指南](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md) 兩種 Server/Client 連線方式的延伸文章。以下，我將在不使用 NVIDIA SYNC 做連線的前提，修改 DGX Spark 建立 Open WebUI 的 NVIDIA官方步驟。希望能給你更多方式參考。</sup></sub>

<sub><sup>This is an extension of my previous article on the two Server/Client connection methods for DGX Spark: [Day01A: Remote Access from Internet Guide](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(Day01A)%20Remote%20Access%20Guide%2020251220A.md) and [Day01B: Local Access from Same Subnet Guide](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(Day01B)%EF%BC%9ALocal%20Access%20from%20Same%20Subnet%20Guide%2020251220B.md). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC connections. I hope this gives you more options for reference.</sup></sub>
# DGX Spark (第02天) 在遠端 DGX Spark 上使用 Ollama 運行 Open WebUI 20251227
# DGX Spark (Day02) Open WebUI with Ollama on Remote Spark 20251227
## 🟩 中文版
> ## 適用情境 與 優點
> **在 Mac/PC Client 開瀏覽器使用 Ollama 運行 Open WebUI → 但背後是遠端 DGX Spark Server 提供算力**
> 
> - **基於前一篇文章 [第01天A: 外網遠端操控 指南](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) 或 [第01天B: 同子網內網操控 指南](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md) 的連線方式**
>   - 100% 連線成功率與穩定度
>   - 自己掌握 Server/Client 連線的設定細節
>   - 不使用 NVIDIA SYNC 的連線方式
> - **修改 DGX Spark 建立 Open WebUI 的 NVIDIA官方步驟** 
>   - 官方步驟是基於 NVIDIA SYNC 連線的
>   - 這裡只修改一點點
> - **SHH 一行指令登入 DGX Spark**
>   - 重開機之後，只要 Mac/PC (Client) 執行一行SHH指令，超級簡單。

---

**打開 NVIDIA [Open WebUI with Ollama : Set up WebUI on Remote Spark with NVIDIA Sync](https://build.nvidia.com/spark/open-webui/sync) 網頁**

## 1. 先設定Router (必做) 

### 1.1 確認網路拓樸
- 確認在 DGX Spark 的前端，只能有唯一的一台 Router：
  - 此 Router 直接接源頭 光纖Modem
  - 此 Router 用 PPPoE 登入電信公司，需有 固定 Public IP (x.x.x.x)
- 在 DGX Spark 的前端，家裡沒有其他的 Router (若有, 把其他 Router 都設在 AP Mode 當 Switch 用)
  - 這樣 DGX Spark 無論接在哪裡，都等於接在唯一的一台 Router 後面
- DGX Spark 的同一層與更後層的網路，可以有其他的 Router

### 1.2 確認已關閉 Router VPN
- 登入 Router
- VPN Server / VPN Client：全部關閉
- 不保留任何帳號、設定

### 1.3 設定 Port Forward (Router)
先找出 DGX Spark 內網IP位址
- 登入 Router 設定主畫面，看到裝置 spark-xxxx 內網 IP 位址 (192.168.x.x) 的值，記錄起來。

用 PPPoE 登入電信公司，取得事先已申請的固定 Public IP (通常在 Router 設定 `主畫面` -> `WAN` -> `WAN Setting` )
- WAN Connection Type：選 PPPoE
- PPPoE Setting：
  - Address Mode = Dynamic IP
  - User Name = 電信公司給你的固定 Public IP 登入帳號
  - Password = 電信公司給你的固定 Public IP 登入密碼
  - Operation Mode = Keep Alive
- Save 存檔 


依此設定 Port Forward (通常在 Router 設定 `主畫面` -> `WAN` -> `Port Forwarding` )
| 欄位 | 值 |
|------|------|
| Rule Name | wireguard |
| Protocal | UDP |
| Public Port | 51820 |
| Private Port | 51820 |
| Private IP | DGX Spark 內網IP (填入一個 192.168.x.x 的 DGX Spark 內網IP值) |
| Inbound Filter | Allow All |
| 啟用 | Yes |

原理說明
- Router 將外網 `UDP:51820`
- 轉送到 DGX Spark `UDP:51820`
- 對應 `ListenPort = 51820`

快速自我檢查
- Port Forward 啟用
- 無其他 VPN 佔用該 Port
- Public / Private Port 一致

---

## 2. DGX Spark (Server) 確認實體網卡名稱 (非常重要)，與 安裝 WireGuard

### 2.1 確認實體網卡名稱
```
ip link
```

記下實體網卡名稱 `(exxxxx 六碼英數字)`，例如：
```
exxxxx (我這台DGX Spark的實體網卡名稱範例，是6碼英數字)
```
- 以下指令基於此 `exxxxx` 六碼英數字的實體網卡名稱範例，設定 WineGuard Server 設定檔
    - 若改硬體，則本篇文章的指令需跟著改
    - 例，若加購第二台 DGX Spark，這名稱可能不同，必須重新確認。

### 2.2 安裝 WireGuard VPN
```
sudo apt update
sudo apt install -y wireguard
```

驗證版本（必做）
```
wg --version
```

---

## 3. DGX Spark (Server) 建立 WireGuard 目錄與金鑰















---

## 架構概念（簡述）

- **服務端**：DGX Spark
  - Open WebUI 以 Docker container 方式執行
  - WebUI 對外監聽 `3000` port
- **用戶端**：Mac
  - 透過 SSH 將 `localhost:12000` 轉發到 Spark 的 `3000`

---

## 先決條件（一次性）

### 1. DGX Spark 上 Docker 權限

確保使用者已在 `docker` 群組, 以後此使用者執行docker指令不用sudo：

```bash
getent group docker
```

若沒有，請執行（只需一次）：

```bash
sudo usermod -aG docker davislin
newgrp docker
```

驗證此使用者執行docker指令不用sudo：

```bash
docker ps -a
```

---

## 情境一：第一次部署 / 尚未有 container

僅在 **第一次部署** 或 **想重建 container** 時使用。

```bash
docker run -d \
  --name open-webui \
  -p 3000:8080 \
  -e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
  -e WEBUI_AUTH=False \
  ghcr.io/open-webui/open-webui:ollama
```

驗證：

```bash
docker ps
curl http://localhost:3000
```

看到 HTML 輸出即代表 Spark 端正常。

---

## 情境二：Spark 重開機後的日常啟動（最常見）

### Step 1：檢查 container 狀態（Spark）

```bash
docker ps
```

若沒有看到 `open-webui`，再檢查：

```bash
docker ps -a
```

### Step 2：啟動已存在的 container（推薦做法）

當看到狀態為 `Exited`：

```bash
docker start open-webui
```

驗證：

```bash
docker ps
```

---

## 情境三：設定自動啟動（強烈建議，僅一次）

若希望 **Spark reboot 後自動啟動 Open WebUI**，請改用 restart policy。

### Step A：刪除舊 container

```bash
docker stop open-webui
docker rm open-webui
```

### Step B：重新建立（含 restart policy）

```bash
docker run -d \
  --name open-webui \
  --restart unless-stopped \
  -p 3000:8080 \
  -e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
  -e WEBUI_AUTH=False \
  ghcr.io/open-webui/open-webui:ollama
```

之後 Spark reboot → container 會自動啟動。

---

## Mac 端：每次使用時的操作

### Step 1：建立 SSH tunnel（Mac Terminal）

```bash
ssh -4 -N -L 12000:0.0.0.0:3000 davislin@192.168.1.4
```

- `-4`：強制 IPv4（避免 IPv6 / localhost 問題）
- 視窗需保持開啟

### Step 2：開啟瀏覽器

```text
http://localhost:12000
```

即可使用 Open WebUI。

---

##（可選）Mac 端便利化設定

### Alias（建議）

在 Mac：

```bash
nano ~/.zshrc
```

加入：

```bash
alias webui='ssh -4 -N -L 12000:0.0.0.0:3000 davislin@192.168.1.4'
```

套用：

```bash
source ~/.zshrc
```

之後只需：

```bash
webui
```

---

## 指令使用時機總表

| 狀態 | 指令 |
|------|------|
| container 不存在 | `docker run -d ...` |
| container 存在但 Exited | `docker start open-webui` |
| container 已 Up | 不需任何操作 |
| Spark reboot 後自動啟動 | `--restart unless-stopped` |

---

## 結語

- Spark 負責跑服務
- Mac 只負責 SSH 明確轉 port
- 不依賴 NVIDIA Sync 的 App Proxy

此 SOP 為 **穩定、可預期、可長期使用** 的標準做法。

