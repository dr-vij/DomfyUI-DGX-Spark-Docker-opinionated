# ComfyDocker

Docker container for running [ComfyUI](https://github.com/Comfy-Org/ComfyUI) with NVIDIA CUDA 13.0 and GPU acceleration.

## About

This project provides a ready-to-use Docker infrastructure for running ComfyUI — a powerful node-based interface for Stable Diffusion and other generative models.

**This Docker setup was specifically designed for NVIDIA DGX Spark and ComfyUI.**

### Features

- **CUDA 13.0** — base image `nvcr.io/nvidia/cuda:13.0.2-devel-ubuntu24.04`
- **Python 3.12** — modern Python version from Ubuntu 24.04
- **onnxruntime-gpu** — built from source for CUDA 13.0 compatibility
- **Automatic dependency management** — installation and updates on each startup
- **Custom nodes support** — automatic cloning and updating from a list
- **Persistent storage** — models, caches, and data are preserved between restarts

## Quick Start

### 1. Clone the repository

```bash
git clone --recursive https://github.com/your-username/ComfyDocker.git
cd ComfyDocker
```

If the repository was already cloned without `--recursive`:

```bash
git submodule update --init --recursive
```

### 2. Create .env file

Create a `.env` file in the project root:

```dotenv
UID=1000
GID=1000
UPDATE_DEPS=true
```

**Parameters:**
- `UID` — user ID (find with: `id -u`)
- `GID` — group ID (find with: `id -g`)
- `UPDATE_DEPS` — update ComfyUI and custom nodes on each startup (`true`/`false`)
- `COMFY_PORT` — web interface port (default: `8188`)

### 3. Create data directory

```bash
mkdir -p ../ComfyData/{models,user,input,output}
```

### 4. Build Docker image

```bash
docker compose build
```

> ⚠️ **Note:** The first build may take a significant amount of time (30+ minutes) as onnxruntime-gpu is compiled from source. Pre-built wheels for DGX Spark are planned for the future to speed up this process.

### 5. Start

```bash
docker compose up
```

After startup, ComfyUI will be available at: **http://localhost:8188**

### Stop

```bash
docker compose down
```

## Custom Nodes Management

### Adding custom nodes

Edit the `custom_nodes/custom_nodes.txt` file, adding the git repository URL:

```
https://github.com/author/ComfyUI-CustomNode.git
```

On the next container startup, the node will be automatically cloned and its dependencies installed.

### Pre-installed custom nodes

The default configuration includes:

- **ComfyUI-Manager** — GUI for managing custom nodes
- **ComfyUI-AdvancedLivePortrait** — advanced Live Portrait
- **ComfyUI-Chord** — by Ubisoft
- **ComfyUI-GGUF** — GGUF models support
- **ComfyUI-Inspire-Pack** — useful nodes collection
- **ComfyUI-KJNodes** — additional nodes by kijai
- **ComfyUI-segment-anything-2** — SAM2 segmentation
- **ComfyUI_IPAdapter_plus** — IP-Adapter
- **comfyui_controlnet_aux** — ControlNet preprocessors
- And more...

### Disabling custom nodes

Comment out the line in `custom_nodes.txt` with `#`:

```
# https://github.com/author/ComfyUI-CustomNode.git
```

## Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `UID` | User ID for running the container | — |
| `GID` | Group ID for running the container | — |
| `UPDATE_DEPS` | Update ComfyUI and custom nodes | `false` |
| `COMFY_PORT` | Web interface port | `8188` |

### Volumes

| Container Path | Local Path | Description |
|----------------|------------|-------------|
| `/workspace/venv` | `./venv` | Python virtual environment |
| `/workspace/pip_cache` | `./pip_cache` | pip cache |
| `/workspace/cache/huggingface` | `./hf_cache` | Hugging Face cache |
| `/workspace/cache/ultralytics` | `./ultralytics_cache` | YOLO cache |
| `/workspace/ComfyUI` | `./ComfyUI` | ComfyUI source code |
| `/workspace/ComfyUI/custom_nodes` | `./custom_nodes` | Custom nodes |
| `/workspace/ComfyUI/models` | `../ComfyData/models` | Models |
| `/workspace/ComfyUI/user` | `../ComfyData/user` | User data |
| `/workspace/ComfyUI/input` | `../ComfyData/input` | Input files |
| `/workspace/ComfyUI/output` | `../ComfyData/output` | Output files |

## Adding Models

Place models in the appropriate subdirectories of `../ComfyData/models/`:

```
ComfyData/models/
├── checkpoints/      # Main models (SD 1.5, SDXL, Flux, etc.)
├── loras/            # LoRA models
├── controlnet/       # ControlNet models
├── vae/              # VAE models
├── embeddings/       # Text embeddings
├── upscale_models/   # Upscale models
└── ...
```

### Clearing caches

```bash
# Clear pip cache
rm -rf pip_cache/*

# Clear HuggingFace cache
rm -rf hf_cache/*

# Full venv reinstall
rm -rf venv/*
```

## License

This project is distributed under the MIT License. ComfyUI has its own license — see [ComfyUI License](https://github.com/Comfy-Org/ComfyUI/blob/master/LICENSE).

## Author

dr-vij
