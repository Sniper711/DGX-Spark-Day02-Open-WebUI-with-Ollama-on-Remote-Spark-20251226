<sub><sup>This is an extension of my previous article on the two Server/Client connection methods for DGX Spark: [Day01A](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/tree/main) and [Day01B](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC connections. I hope this gives you more options for reference.</sup></sub>
# DGX Spark (Day02) Open WebUI with Ollama on Remote Spark 20251226 🟩 [English]
# DGX Spark (第02天) 用 Open WebUI 介面 遠端操作 DGX Spark 上的 Ollama 20251226 🟩 [中文版]


## Scenarios & Advantages

Mac/PC browser uses the Open WebUI interface → through the self-established remote connections → to run Ollama on DGX Spark Server

    This is an extension of my previous articles on the two Server/Client connection methods for DGX Spark: 
        [Day01A: Remote Access from Internet Guide] and 
        [Day01B: Local Access from Same Subnet Guide]. 
        Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC connections.
    Guaranteed stability through the self-estabilished remote connections
        No reliance on NVIDIA SYNC
    Minor modifications to the NVIDIA official steps
        The official steps are built around NVIDIA SYNC connections; only three steps need to be changed to match the self-established remote connections.
    Simple one-line SSH command login to DGX Spark
        After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.
.....

.....

Congratulations — your Mac/PC can now reach your DGX Spark from anywhere.
        

## 適用情境 與 優點

人在外網用 Mac/PC → 透過 WireGuard VPN → SSH 登入家中 DGX Spark

    全面改用 WireGuard VPN
        以 DGX Spark 為 VPN Server. (Mac/PC = Client)
        VPN 穿透率極高，行動網路開熱點上網幾乎不被行動網路阻擋.
        WireGuard 設定 UDP 51820 Port 搭配 keepalive 是正解.
        90% 在台灣行動網路，WireGuard 能過，OpenVPN 過不了.
    不用 Tunnelblick 與 OpenVPN，不用昂貴 Router 的內建 VPN Server
        行動網路開熱點連VPN失敗主因之一：電信商刻意阻擋行動網路的 UDP/TCP 1194 Ports VPN 流量.
            改用 TCP 443 Port 能增強 VPN 穿透率，相對穩定但速度慢，在行動網路可能有TCP-over-TCP隊頭阻塞(HOL Blocking)導致熔斷(Meltdown)問題.
            某些昂貴的 Router 無法改 TCP Port# 也是問題 (僅有 TCP 1194 Port).
        行動網路開熱點連VPN失敗主因之二：行動網路NAT導致UDP丟包.
            Tunnelblick 與 OpenVPN 雖然使用UDP，但內部實作卻類似TCP與SSL/TLS，步驟多，在行動網路容易中途斷掉.
        有鑑於此，不用 Tunnelblick 與 OpenVPN
    用低階的 Router
        Router：需有 固定 Public IP (x.x.x.x), 需支援 Port Forward.
        因為不使用 Tunnerblick 與 OpenVPN，所以 Router 並不需要 VPN 高階功能 (有則關閉之)，只需便宜的 Router.
    SSH 一行指令登入 DGX Spark
        重開機之後，只要 Mac/PC (Client) 執行步驟9.1這行SHH指令，超級簡單。
.....

.....

恭喜你！從此你能從任何地方連回你心愛的 DGX Spark 了！

---

## 喜歡這個專案嗎？ 如果對您有幫助，請給一個 ★ Star 吧！這對我非常重要！

## If you find this project helpful, please give it a Star ★! Your support means a lot to me!


---
Davis Lin (Sniper711) .
Unauthorized article copying, distribution, or modification is prohibited.
