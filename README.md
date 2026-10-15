# RTX 5090 ComfyUI Upscale Setup

Best-quality local upscaling stack. Pure ESRGAN for max sharpness, no diffusion hallucination.

## 1. Install ComfyUI Portable (NVIDIA)

- Download: https://github.com/Comfy-Org/ComfyUI/releases — grab the latest `ComfyUI_windows_portable_nvidia.7z`
- Extract with 7-Zip to a short path: `C:\AI\ComfyUI`
- Double-click `run_nvidia_gpu.bat`
- Browser opens at http://127.0.0.1:8188

Your 5090 is fully supported by the standard NVIDIA build. No cu126 needed.

## 2. Install ComfyUI Manager

Open a terminal inside `ComfyUI\custom_nodes` and run:

```
git clone https://github.com/ltdr/ComfyUI-Manager
```

Restart ComfyUI. Manager gives you one-click custom node installs and auto-fixes missing nodes.

## 3. Install Ultimate SD Upscale (optional, for 8K texture pass)

In ComfyUI Manager: Custom Nodes Manager → search `UltimateSDUpscale` → install latest → restart.

Or manually:

```
git clone https://github.com/ssitu/ComfyUI_UltimateSDUpscale custom_nodes/ComfyUI_UltimateSDUpscale
```

## 4. Download Models

Drop both into `ComfyUI\models\upscale_models\`:

- **4x-UltraSharp.safetensors** (general photoreal, sharpest overall)
  https://huggingface.co/Kim2091/UltraSharp/resolve/main/4x-UltraSharp.safetensors

- **4x_foolhardy_Remacri.safetensors** (skin/faces, avoids waxy plastic look)
  https://huggingface.co/Kenway/ESRGAN-4x/resolve/main/4x_foolhardy_Remacri.safetensors
  (mirror: https://huggingface.co/OzzyGT/4xRemacri)

## 5. Load the Workflow

Drag `ultrasharp_4x_upscale.json` from this repo straight into the ComfyUI window.

### What it does

Load Image → Upscale Model Loader (4x-UltraSharp) → Image Upscale With Model → Save Image + Preview

Pure ESRGAN. No SD. Maximum sharpness. On a 5090 this is near-instant even at full 4x.

### Face fix

If faces look plasticky, open the Upscale Model Loader node and switch the dropdown to `4x_foolhardy_Remacri.safetensors`, then re-queue.

### 8K texture pass (optional)

Swap Image Upscale With Model for Ultimate SD Upscale:
- upscale_by: 2
- denoise: 0.25–0.35 (above 0.4 hallucinates faces into texture)
- tile width/height: 512
- seam fix mode: Half Tile
- mask blur: 8

### Double upscale trick

Run 4x UltraSharp first, save, then feed that output through again at 2x with Remacri. Stacking two different models beats any single model on tough images.

## Notes

- Portable and Desktop versions of ComfyUI are identical in quality. This setup works in either.
- These are raw PyTorch .safetensors files — ComfyUI reads them natively, no chaiNNer conversion needed (that's only for Upscayl).
- SUPIR is the diffusion-based alternative if you ever want it to invent detail instead of just sharpening, but it releases ~16GB VRAM and trades sharpness for realism.