<sub><sup>This is an extension of my previous article on the two Server/Client connection methods for DGX Spark: [Day01A](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/tree/main) and [Day01B](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC connections. I hope this gives you more options for reference.</sup></sub>
# DGX Spark (Day02) Open WebUI with Ollama on Remote Spark 20251226 🟩 [English]
# DGX Spark (第02天) 用 Open WebUI 介面 遠端操作 DGX Spark 上的 Ollama 20251226 🟩 [中文版]


> ## Scenarios & Advantages
> **Mac/PC browser uses the Open WebUI interface → through the self-established remote connections → to run Ollama on DGX Spark Server**
> - **Based on the interconnection methods on previous repositories of [DGX Spark (Day01A) Remote Access from Internet Guide](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(Day01A)%20Remote%20Access%20Guide%2020251220A.md) and [DGX Spark (Day01B) Local Access from Same Subnet Guide](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(Day01B)%EF%BC%9ALocal%20Access%20from%20Same%20Subnet%20Guide%2020251220B.md)**. 
>   - Guaranteed stability through the self-estabilished remote connections
>   - No reliance on NVIDIA SYNC
> - **Minor modifications to the NVIDIA official steps** 
>   - The official steps are built around NVIDIA SYNC connections; only three steps need to be changed to match the self-established remote connections.
> - Simple one-line **SSH** command **login to DGX Spark**
>   - After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.

        

> ## 適用情境 與 優點
> **Mac/PC Client 開瀏覽器在 Open WebUI 介面上 → 透過自己建立的遠端連線 → 用 DGX Spark Server 的算力跑 Ollama**
> - **基於前一篇文章 [第01天A: 外網遠端操控 指南](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9A)%20%E5%A4%96%E7%B6%B2%E9%81%A0%E7%AB%AF%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220A.md) 或 [第01天B: 同子網內網操控 指南](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(%E7%AC%AC01%E5%A4%A9B)%EF%BC%9A%E5%90%8C%E5%AD%90%E7%B6%B2%E5%85%A7%E7%B6%B2%E6%93%8D%E6%8E%A7%20%E6%8C%87%E5%8D%97%2020251220B.md) 的連線方式**
> - **100% 連線成功率與穩定度，自己掌握 Server/Client 連線的設定細節**
>   - 不使用 NVIDIA SYNC app 的連線方式
> - **小修改 NVIDIA官方步驟** 
>   - 官方步驟是基於 NVIDIA SYNC app 連線的，只修改三個步驟就能匹配 自己建立的遠端連線
> - **SHH 一行指令登入 DGX Spark**
>   - 重開機之後，只要 Mac/PC (Client) 執行 `Step 4-3` 與 `Step 5`，超級簡單。

---

## 喜歡這個專案嗎？ 如果對您有幫助，請給一個 ★ Star 吧！這對我非常重要！

## If you find this project helpful, please give it a Star ★! Your support means a lot to me!


---
Davis Lin (Sniper711) .
Unauthorized article copying, distribution, or modification is prohibited.
