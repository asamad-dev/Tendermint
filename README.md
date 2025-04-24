# Tendermint ABCI Go App: Windows + WSL Setup Guide

This document outlines the exact steps followed to build a custom ABCI application using Tendermint and Go, based on the official guide: [docs.tendermint.com/master/tutorials/go-built-in.html](https://docs.tendermint.com/master/tutorials/go-built-in.html). It includes instructions tailored for Windows users running Linux via WSL and coding in VS Code.

---

## 1. Installing WSL on Windows

WSL (Windows Subsystem for Linux) allows you to run a Linux environment on Windows.

### Install WSL and Ubuntu

Open **PowerShell as Administrator** and run:

```powershell
wsl --install
```

This installs:
- WSL2
- Ubuntu (default distro)

Reboot if prompted.

### Launch Ubuntu

Once installed, launch “Ubuntu” from the Start Menu. It will prompt you to create a Linux username and password.

---

## 2. Set Up Go and Development Tools

### Update packages and install essential tools:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential git curl -y
```

### Install Go (recommended: Go 1.20+)

```bash
sudo apt install golang-go
go version
```

If you want the latest version, download it from [go.dev/dl](https://go.dev/dl/) and follow manual install steps.

---

##  3. Open Project in VS Code with WSL

### Install "Remote - WSL" Extension

In VS Code:
1. Go to Extensions tab.
2. Install **"WSL"** by Microsoft.

### Open your WSL project in VS Code

In the VS Code click on blue button at botton left corner:

![image](https://github.com/user-attachments/assets/86b75108-a95e-4605-a2d8-9cfe46d47383)

Select **Connect to WSL**

![image](https://github.com/user-attachments/assets/3d3b62cd-76ab-416d-a18f-78ac6f26fb16)


This opens the current folder inside VS Code (running on Linux backend).

---

## 4. Clone Project

Clone Project:

```bash
git clone https://github.com/asamad-dev/Tendermint.git
cd Tendermint
```

Check for all packages and then build

```bash
go mod tidy
go build
```

---

## 5. Running Tendermint with Your ABCI App

### Initialize Tendermint config

```bash
CMTHOME="Tendermint/" cometbft init validatorl"
```

If you got error in 1st command then do following

```bash
go install github.com/cometbft/cometbft/cmd/cometbft@latest
export PATH=$PATH:$(go env GOPATH)/bin
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> ~/.bashrc
source ~/.bashrc
```
then go to root and folder (`home/{user}`) and run the above two commands.


### Run your custom node



```bash
./Tendermint/Tendermint -config "Tendermint/config/config.toml"
```


## 6. Broadcasting Transactions

```bash
curl 'localhost:26657/broadcast_tx_commit?tx="tendermint=rocks"'
```

Query:

```bash
curl 'localhost:26657/abci_query?data="tendermint"'
```

Expected JSON response:

```json
{
  "log": "exists",
  "value": "cm9ja3M=" // base64 encoded "rocks"
}
```

If transaction already exists, error:
```json
"error": { "message": "tx already exists in cache" }
```

---

## 8. Common Errors

### Error 1:
```bash
abdul@TCE:~$ ./Tendermint/Tendermint -config "Tendermint/config/config.toml"
badger 2025/04/25 02:20:56 INFO: All 0 tables opened in 0s
badger 2025/04/25 02:20:56 INFO: Replaying file id: 0 at offset: 0
badger 2025/04/25 02:20:56 INFO: Replay took: 3.474µs
badger 2025/04/25 02:20:56 DEBUG: Value log discard stats empty
failed to create new Tendermint node: unknown db_backend pebbledb, expected one of goleveldb,memdbabdul@TCE:~$ 
abdul@TCE:~$ 
```
Go to `Tendermint/config/config.toml`
Change 
`db_backend = "pebbledb"`
to 
`db_backend = "goleveldb"`

### Error 2:
```bash
abdul@TCE:~$ ./Tendermint/Tendermint -config "Tendermint/config/config.toml"
badger 2025/04/25 02:21:39 INFO: All 0 tables opened in 0s
badger 2025/04/25 02:21:39 INFO: Replaying file id: 0 at offset: 0
badger 2025/04/25 02:21:39 INFO: Replay took: 4.688µs
badger 2025/04/25 02:21:39 DEBUG: Value log discard stats empty
failed to create new Tendermint node: error reading GenesisDoc at Tendermint/config/genesis.json: block.TimeIotaMs must be greater than 0. Got 0abdul@TCE:~$ 
abdul@TCE:~$ 
```
Go to `Tendermint/config/genesis.json`
add 
`"time_iota_ms": 1000`
inside `block`
```bash
"block": {
      "max_bytes": "4194304",
      "max_gas": "10000000",
```


---
