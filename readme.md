# AUTOMATIC1111 Stable Diffusion WebUI for TrueNAS

Docker setup for running AUTOMATIC1111 Stable Diffusion WebUI with NVIDIA GPU support.

## Quick Start

### Build and Run

```bash
docker compose up -d
```

### Test the Image

```bash
docker run --rm sd-auto:latest python --version
```

## CLI Arguments Explained

The WebUI is configured with the following arguments (see `compose.yaml`):

### `--allow-code`

- **Purpose**: Allows custom scripts to execute arbitrary Python code
- **Risk**: ⚠️ Security risk - enables extensions to run any code on your system
- **Use case**: Required for some advanced extensions that need to execute custom Python
- **Recommendation**: Only use if you trust all your extensions. Since you're running on TrueNAS (likely local network), this is probably fine

### `--xformers`

- **Purpose**: Enables memory-efficient attention using the xformers library
- **Benefits**:
  - ✅ Significantly reduces VRAM usage (20-50% less memory)
  - ✅ Faster generation in many cases
  - ✅ Allows higher resolution images on the same GPU
- **Requirement**: xformers must be installed (PyTorch usually includes it or auto-installs)
- **Recommendation**: ✅ Keep this - it's essential for efficient GPU usage

### `--enable-insecure-extension-access`

- **Purpose**: Allows extensions to access files outside their designated directories
- **Risk**: ⚠️ Security risk - extensions can read/write anywhere the container has permissions
- **Use case**: Some extensions need to access models, outputs, or other directories
- **Note**: Inside a container this is less risky since it's sandboxed, but extensions could still modify your mounted volumes (`/data`, `/output`)
- **Recommendation**: Needed for many popular extensions to work properly

### `--api`

- **Purpose**: Enables the REST API server
- **Benefits**:
  - ✅ Allows external applications to control the WebUI programmatically
  - ✅ Useful for automation, mobile apps, or custom integrations
  - ✅ ComfyUI integration, API clients, etc.
- **Port**: Same port as WebUI (7860), adds `/sdapi/v1/` endpoints
- **Recommendation**: ✅ Keep this if you want API access, otherwise optional

## Additional Configuration

### For SDXL Models (High VRAM Usage)

Add `--medvram` to your CLI_ARGS in `compose.yaml`:

```yaml
CLI_ARGS=--allow-code --xformers --enable-insecure-extension-access --api --medvram
```

### For Low VRAM GPUs (4GB)

Add `--lowvram` instead:

```yaml
CLI_ARGS=--allow-code --xformers --enable-insecure-extension-access --api --lowvram
```

## Access

Once running, access the WebUI at:

- **Web Interface**: `http://localhost:7860`
- **API Docs**: `http://localhost:7860/docs`

## Volumes

- `./data` - Models, embeddings, LoRAs, etc.
- `./output` - Generated images and outputs
