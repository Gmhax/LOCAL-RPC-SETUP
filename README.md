## Local RPC Node Setup – Quick Start Guide

`This guide helps you set up your own local Sepolia RPC node quickly and reliably. Before starting, make sure you have a VPS that meets the minimum hardware requirements. This setup uses Docker to simplify installation and ensures your node runs smoothly with sufficient resources.`

 - Quick Checklist Before Starting
 - VPS with required CPU/RAM/disk ready (SSD ≥1 TB)
 - Ubuntu updated (use Ubuntu 24 if combining Aztec)
 - Docker installed and running
 - Port whitelist configured (or keep local only)
 - SSH access and ability to reattach screen


### 🧰 Preparations / Requirements

#### Minimum (RPC lang)
- 6 CPU Cores  
- 8 GB RAM  
- 1.5 TB+ SSD (must)

#### Recommended (Aztec + RPC)
- 8+ CPU Cores  
- 16+ GB RAM  
- 1.5 TB+ SSD (must)  
- Ubuntu 24 (kung mag-combine Aztec + RPC)


## INSTALLATION

1) Update OS:
```
sudo apt update && sudo apt upgrade -y
```

2) Install prerequisites
```
sudo apt install -y screen curl
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

- (Opsyonal: install docker-compose plugin)
```
sudo apt install -y docker-compose-plugin
```

3) Run the automation installer (one-line the script you shared)
```
clear && if ! command -v screen >/dev/null 2>&1; then sudo apt install -y screen >/dev/null 2>&1 || true; fi
if ! command -v curl >/dev/null 2>&1; then sudo apt install -y curl >/dev/null 2>&1 || true; fi
screen -ls | grep 'rpc' | awk '{print $1}' | xargs -r -n1 -I {} screen -S {} -X quit >/dev/null 2>&1 || true
sleep 1
screen -ls | grep 'rpc' | awk '{print $1}' | cut -d. -f1 | xargs -r kill >/dev/null 2>&1 || true
screen -dmS rpc bash -c 'clear; bash <(curl -sLS https://raw.githubusercontent.com/DeepPatel2412/Sepolia-RPC-Setup/refs/heads/main/Reth-Prysm%20%3A%20Sepolia); exec bash' >/dev/null 2>&1 && screen -r rpc || echo "Failed to start 'rpc' screen session."
```
- To detach: press Ctrl-A then D.

4) After Installation Starts
- The script will pull Docker images, set up Ethereum Docker Compose, and start syncing using a snapshot.
- Sync time: approx 6–8 hours (depends on VPS performance).
- Do not interrupt; just leave it running.

5) Monitor Progress / Logs
- Using screen: `screen -r rpc`
- Ctrl-A D -> to detach

6) RPC Endpoints

- If Aztec + RPC on the same VPS:
- Sepolia RPC: http://localhost:8545
- Beacon: http://localhost:3500
- If RPC is on a separate VPS:
- Sepolia RPC: http://<RPC_VPS_IP>:8545
- Beacon: http://<RPC_VPS_IP>:3500

7) UFW / Whitelist: if you want to add folks to access your RPC
```
bash <(curl -Ls https://raw.githubusercontent.com/DeepPatel2412/Sepolia-RPC-Setup/main/ufwWhitelistTool)
```
- Choose Add IP / Remove IP, then enter IPs and ports (e.g., 8545,3500).



# DONE

## Troubleshooting Tips

- If sync stalls: check disk space (df -h), container status (docker ps), and logs (docker compose logs -f reth).
- If a container exits: docker logs <containerName>
- “Datadir does not exist” error: verify paths and volumes in your docker-compose.yml.
- High CPU/memory usage: check top or htop; consider upgrading VPS resources.
- RPC not reachable from remote: check UFW rules and provider firewall (cloud providers may block ports).


# DELETE RPC SETUP ON YOUR VPS

## Cleanup / Full Delete (Rare Case)
- Interactive cleanup script:
```
bash <(curl -fsSL https://raw.githubusercontent.com/DeepPatel2412/Sepolia-RPC-Setup/main/sepolia-RPC-cleanup)
```
- Manual cleanup:
```
cd ~/Ethereum
docker compose down -v
docker system prune -af
docker volume prune -f
# Remove Ethereum directory if desired (BE CAREFUL)
sudo rm -rf ~/Ethereum
```








































