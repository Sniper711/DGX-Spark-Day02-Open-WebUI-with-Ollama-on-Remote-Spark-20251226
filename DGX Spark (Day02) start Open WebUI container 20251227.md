<sub><sup>這是我前一篇文章 DGX Spark : [第01天A](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) 與 [第01天B](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md) 兩種 Server/Client 連線方式的延伸文章。以下，我將在不使用 NVIDIA SYNC 做連線的前提，修改 DGX Spark 建立 Open WebUI 的 NVIDIA官方步驟。希望能給你更多方式參考。</sup></sub>

<sub><sup>This is an extension of my previous article on the two Server/Client connection methods for DGX Spark: [Day01A](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) and [Day01B](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC connections. I hope this gives you more options for reference.</sup></sub>
# DGX Spark (Day02) start Open WebUI container 20251227
## 🟩 中文版
## 適用情境 與 優點
> **這是我前一篇文章 DGX Spark : [第01天A 外網遠端操控 指南](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) 與 [第01天B 同子網內網操控 指南](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md) 兩種 Server/Client 連線方式的延伸文章。**
> **以下，我將在不使用 NVIDIA SYNC 做連線的前提，修改 DGX Spark 建立 Open WebUI 的 NVIDIA官方步驟 (官方步驟是基於 NVIDIA SYNC 連線的)。**
> 
> <sub><sup>這是我前一篇文章 DGX Spark : [第01天A 外網遠端操控 指南](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) 與 [第01天B 同子網內網操控 指南](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md) 兩種 Server/Client 連線方式的延伸文章。以下，我將在不使用 NVIDIA SYNC 做連線的前提，修改 DGX Spark 建立 Open WebUI 的 NVIDIA官方步驟。</sup></sub>
> - **用 WireGuard VPN**
>   - 以 DGX Spark 為 VPN Server. (Mac/PC = Client)
>   - VPN 穿透率極高，行動網路開熱點上網幾乎不被行動網路阻擋.
>   - WireGuard 設定 UDP 51820 Port 搭配 keepalive 是正解.
>   - 90% 在台灣行動網路，WireGuard 能過，OpenVPN 過不了.
> - **不用 Tunnelblick 與 OpenVPN，不用昂貴 Router 的內建 VPN Server** 
>   - 行動網路開熱點連VPN失敗主因之一：電信商刻意阻擋行動網路的 UDP/TCP 1194 Ports VPN 流量.
>     - 改用 TCP 443 Port 能增強 VPN 穿透率，相對穩定但速度慢，在行動網路可能有TCP-over-TCP隊頭阻塞(HOL Blocking)導致熔斷(Meltdown)問題.
>     - 某些昂貴的 Router 無法改 TCP Port# 也是問題 (僅有 TCP 1194 Port).
>   - 行動網路開熱點連VPN失敗主因之二：行動網路NAT導致UDP丟包.
>     - Tunnelblick 與 OpenVPN 雖然使用UDP，但內部實作卻類似TCP與SSL/TLS，步驟多，在行動網路容易中途斷掉.
>   - 有鑑於此，不用 Tunnelblick 與 OpenVPN
> - **用低階的 Router**
>   - Router：需有 固定 Public IP (x.x.x.x), 需支援 Port Forward.
>   - 因為不使用 Tunnerblick 與 OpenVPN，所以 Router 並不需要 VPN 高階功能 (有則關閉之)，只需便宜的 Router.
> - **SHH 一行指令登入 DGX Spark**
>   - 重開機之後，只要 Mac/PC (Client) 執行步驟9.1這行SHH指令，超級簡單。

本文件說明在 **DGX Spark（Linux）** 上部署並日常使用 **Open WebUI（Docker）**，以及在 **Mac** 端透過 **SSH port forwarding** 存取 WebUI 的標準流程。本 SOP 已避開 NVIDIA Sync 的不穩定 App Proxy，採用工程上最穩定、可預期的做法。

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

