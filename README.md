# NVIDIA Dynamo: Containerised with vLLM Backend

## Overview

This repository includes a Docker environment for running **NVIDIA Dynamo** inference framework with **vLLM**. Includes pre-installed dependencies, service setup (etcd, nats-server), and example scripts for running prompts via batch commands. 

---

## Container Contents

The container is built with all required components to execute inference using `dynamo run` with a vLLM backend:

- **Environment and tools**:

  - Python 3, `pip`, and `venv`
  - Git

- **Installed software**:

  - NVIDIA's `ai-dynamo` (version 0.3.2)
  - Required dependencies
  - `etcd` and `nats-server` (JetStream mode)

- **Cloned Repositories**:

  - The official `dynamo` GitHub repository

The container replicates a complete environment for inference (no local installation of dependencies is needed).

---

## How to Get the Container

You can pull the Docker image from Docker Hub:
```bash
docker pull belalyahouni/dynamo-vllm:latest
```

---

## Requirements

To use the container, you will need:

1. A **Python script** to be mounted into the container (e.g. `vllm.py`)
2. (Optional) An **extra arguments JSON file** (e.g. `args.json`) for configuration
3. Linux

---

## Script Details (`vllm.py`)

The script should:

### 1. Parse input arguments

- The **first argument** is the prompt (as a string)

It saves the prompt in a file called `prompt.json1` in JSON Lines format:

```json
{"text": "your prompt here"}
```

- Any following arguments are passed as flags to `dynamo run`

### 2. Activate the virtual environment

```bash
source /venv/bin/activate
```

### 3. Start required services

```bash
etcd &
nats-server -js &
```

### 4. Construct and run the inference command

- Include your prompt.json1 and any extra flags as follows:

```bash
dynamo run in=batch:prompt.json1 out=vllm Qwen/Qwen3-0.6B {extra_flags}
```

If `--extra-engine-args args.json` is provided, the script will read that file and pass its content to the backend.

### 5. Output

Results are saved to `output.jsonl`, which the script should read and print.

---

## Example Usage

```bash
docker run --gpus all \
  -v $(pwd)/vllm.py:/script.py \
  belalyahouni/dynamo-vllm:latest \
  "What is the capital of Spain?"
```

```bash
docker run --gpus all \
  -v $(pwd)/vllm.py:/script.py \
  -v $(pwd)/args.json:/args.json \
  belalyahouni/dynamo-vllm:latest \
  "What is the capital of Spain?" \
  --extra-engine-args args.json
```

---

## Extra Arguments File (`args.json`)

This optional JSON file defines backend-specific configuration.

---


## Notes



