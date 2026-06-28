# NVdia Jetson Orin Nano &  Llama.cpp Setup Guide

## Install Flatpak and Chromium

```bash
sudo apt update
sudo apt install flatpak -y

flatpak remote-add --if-not-exists flathub \
https://flathub.org/repo/flathub.flatpakrepo

flatpak install flathub org.chromium.Chromium
```

---

# Installing JetPack 6.2.2

Edit NVIDIA apt sources:

```bash
sudo gedit /etc/apt/sources.list.d/nvidia-l4t-apt-source.list
```

Change all repository versions to:

```text
r36.5
```

Then run:

```bash
sudo apt update
sudo apt dist-upgrade
sudo apt install --fix-broken -o Dpkg::Options::="--force-overwrite"
```

---

# Install llama.cpp Locally

Clone and build:

```bash
git clone https://github.com/ggerganov/llama.cpp.git

cd llama.cpp

cmake -B build
cmake --build build -j --target llama-server llama-cli
```

---

# Run llama.cpp Locally

## Start OpenAI-Compatible Server with Web UI

```bash
./build/bin/llama-server \
-hf unsloth/gemma-4-E4B-it-GGUF:Q4_K_M
```

## Run Inference in Terminal

```bash
./build/bin/llama-cli \
-hf unsloth/gemma-4-E4B-it-GGUF:Q4_K_M
```

---

# Docker-Based llama-server Commands

## Gemma 4 E2B Q3_K_M (Thinking Disabled)

```bash
sudo docker run -it \
--runtime=nvidia \
--network host \
-v $HOME/.cache/huggingface:/root/.cache/huggingface \
ghcr.io/nvidia-ai-iot/llama_cpp:latest-jetson-orin \
llama-server \
-hf unsloth/gemma-4-E2B-it-GGUF:Q3_K_M \
-t 8 \
-c 9024 \
-n 5024 \
--keep 2056 \
-ngl 999 \
--chat-template-kwargs '{"enable_thinking":false}'
```

---

## Gemma 4 E2B Q3_K_M (Thinking Enabled)

```bash
sudo docker run -it \
--runtime=nvidia \
--network host \
-v $HOME/.cache/huggingface:/root/.cache/huggingface \
ghcr.io/nvidia-ai-iot/llama_cpp:latest-jetson-orin \
llama-server \
-hf unsloth/gemma-4-E2B-it-GGUF:Q3_K_M \
-t 8 \
-c 19024 \
-n 5024 \
--keep 2056 \
-ngl 999 \
--chat-template-kwargs '{"enable_thinking":true}'
```

---

# Gemma 4 E2B Q3_K_M  with Tool Call

```bash
sudo docker run -it \
--runtime=nvidia \
--network host \
-v $HOME/.cache/huggingface:/root/.cache/huggingface \
ghcr.io/nvidia-ai-iot/llama_cpp:latest-jetson-orin \
llama-server \
-hf unsloth/gemma-4-E2B-it-GGUF:Q4_K_M \
-t 6 \
-c 55024 \
-n 15024 \
--keep 2056 \
-ngl 70 \
--chat-template-kwargs '{"enable_thinking":true}' \
--jinja \
-fa 'on' \
--host 0.0.0.0 \
--port 8080:8080
```

---
