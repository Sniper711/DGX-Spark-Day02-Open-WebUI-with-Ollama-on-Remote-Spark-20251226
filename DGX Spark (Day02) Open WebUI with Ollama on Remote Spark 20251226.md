<sub><sup>This is an extension of my previous articles on the two Server/Client connection methods for DGX Spark: [Day01A: Remote Access from Internet Guide](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(Day01A)%20Remote%20Access%20from%20Internet%20Guide%2020251220A.md) and [Day01B: Local Access from Same Subnet Guide](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(Day01B)%EF%BC%9ALocal%20Access%20from%20Same%20Subnet%20Guide%2020251220B.md). Here, I'll adapt the official NVIDIA steps (which rely on NVIDIA SYNC app) for setting up Open WebUI on an NVIDIA DGX Spark, without using NVIDIA SYNC app connections. I hope this gives you more options for reference.</sup></sub>
# DGX Spark (Day02) Open WebUI with Ollama on Remote Spark 20251226
## 🟩 English
> ## Scenarios & Advantages
> **Mac/PC Client browser uses the Open WebUI interface → through the self-established remote connections → to run Ollama on DGX Spark Server**
> - **Based on the interconnection methods of DGX Spark: [Day01A: Remote Access from Internet Guide](https://github.com/Sniper711/DGX-Spark-Day01A-Remote-Access-from-Internet-Guide-20251220A/blob/main/DGX%20Spark%20(Day01A)%20Remote%20Access%20from%20Internet%20Guide%2020251220A.md) and [Day01B: Local Access from Same Subnet Guide](https://github.com/Sniper711/DGX-Spark-Day01B-Local-Access-from-Same-Subnet-Guide-20251220B/blob/main/DGX%20Spark%20(Day01B)%EF%BC%9ALocal%20Access%20from%20Same%20Subnet%20Guide%2020251220B.md)**. 
>   - Guaranteed stability through the self-estabilished remote connections
>   - No reliance on NVIDIA SYNC
> - **Minor modifications to the NVIDIA official steps** 
>   - The official steps are built around NVIDIA SYNC connections; only three steps need to be changed to match the self-established remote connections.
> - Simple one-line **SSH** command **login to DGX Spark**
>   - After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.

---

## Based on NVIDIA DGX Spark Official Steps of [Open WebUI with Ollama : Set up WebUI on Remote Spark with NVIDIA Sync](https://build.nvidia.com/spark/open-webui/sync)
On this page,
## Step 1. Configure Docker permissions
(no change)

## Step 2. Verify Docker setup and pull container
(no change)

---

## Step 3. Open NVIDIA SYNC Settings
(Don't do it - follow the modified step below instead)
## Modified Step 3. On Mac/PC Client, temporarily login to DGX Spark Server (The port number for Open WebUI has not been assigned yet)
On Mac/PC Client, run the following command:
###### After executing the following command, you will notice the terminal prompt changes — from your <client_user>@<client_Mac/PC_name>% on the Mac/PC Client to <server_user>@Spark-xxxx:$ on the DGX Spark Server. This indicates a successful login.
```
# Remove `<DGX Spark username>`, and replace it with the username used to log in after DGX Spark boots
# Remove `<192.168.x.x>`, and replace it with DGX Spark intranet IP address (192.168.x.x)
ssh <DGX Spark username>@<192.168.x.x>
```

---

## Step 4. Add Open WebUI custom port configuration
(Don't do it - follow the modified step below instead)
## Modified Step 4. Add Open WebUI custom port configuration 
### Modified Step 4-1. Add Open WebUI custom port configuration
On Mac/PC Client, run the following command:
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
- Command explainations
  - **docker** Docker command
  - **run -d** Run the container in detached mode (in the background, without showing logs in the terminal).
  - **--gpus all** Grant the container access to all NVIDIA GPUs on the DGX Spark host.
  - **-p 3000:8080** Publish (map) port 8080 inside the container to port 3000 on the DGX Spark host.
(*Note: The 3000 port on DGX Spark can be changed.)
  - **-v ollama:/root/.ollama** Mount the Docker named volume `ollama` stored on the DGX Spark host (typically under /var/lib/docker/volumes/ollama/_data) to `/root/.ollama` inside the container.
  - **-v open-webui:/app/backend/data** Mount the Docker named volume `open-webui` stored on the DGX Spark host (typically under /var/lib/docker/volumes/open-webui/_data) to `/app/backend/data` inside the container.
  - **--name open-webui** Name the container open-webui.
  - **--restart unless-stopped** Automatically restart the container on system boot (or after crashes), unless it was explicitly stopped with docker stop.
(*Note: Can be changed to --restart always to always restart regardless of how it was stopped.)
  - **ghcr.io/open-webui/open-webui:ollama** Use the Docker image ghcr.io/open-webui/open-webui:ollama to create the container.

### Modified Step 4-2. On Mac/PC Client, log out from the temporary DGX Spark Server session established in Step 3 (no Open WebUI communication port was specified yet)
On Mac/PC Client, run the following command:
###### After executing the command, you will notice the terminal prompt changes — from the DGX Spark Server's format (e.g., <server-user>@Spark-xxxx:$) back to your local Mac/PC Client's prompt (e.g., <local-user>@<local-machine>%). This indicates a successful logout.
```
exit
```

### Modified Step 4-3. On Mac/PC Client, re-login to DGX Spark Server (this time specifying the Open WebUI communication port 12000)
On Mac/PC Client, run the following command:

<sub><sup>＊After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.</sup></sub>
```
ssh -4 -N -L 12000:0.0.0.0:3000 davislin@192.168.10.119
```

---

## Step 5. Launch Open WebUI
(no change)

<sub><sup>＊After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.</sup></sub>

---

## Step 6. Create administrator account
(no change)

---

## Step 7. Download and configure a model
(no change)

---

## Step 8. Test the model
(no change)

---

## Step 9. Stop the Open WebUI
(Don't do it - follow the modified step below instead)
## Modified Step 9. Stop the Open WebUI
In the terminal window where you ran Step 4-3 (the SSH tunnel command), press `Ctrl+C` to exit.

*This will terminate the SSH tunnel, stopping the local port forwarding and closing access to the Open WebUI port on the DGX Spark server.

---

## Step 10. Next steps
(no change)

---

## Step 11. Cleanup and rollback
(no change) 

**Important: The following commands should be run directly on the DGX Spark Server (not from your Mac/PC client).**

---

# Congratulations - Now you can run Ollama via your Mac/PC browser — powered by DGX Spark's GPU!
<sub><sup>* After rebooting, simply have the Mac/PC Client run `Step 4-3` and `Step 5` - it's super easy.</sup></sub>

---
