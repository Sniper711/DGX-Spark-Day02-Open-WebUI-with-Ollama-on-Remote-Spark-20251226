# DGX Spark (Day02) start Open WebUI container 2026-01-01
## 🟩 English



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

### 1. Spark 上 Docker 權限

確保使用者已在 `docker` 群組：

```bash
getent group docker
```

若沒有，請執行（只需一次）：

```bash
sudo usermod -aG docker davislin
newgrp docker
```

驗證：

```bash
docker ps
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

