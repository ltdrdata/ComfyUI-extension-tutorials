## Extreme Highresolution Upscale

![example](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/workflow/extreme-highscale.png)
![workflow](extreme-upscale-workflow.png)

* Using PixelTiledKSampleUpscalerProviderPipe can help avoid VRAM shortage issues. Here, we reduced the number of steps to quickly obtain results, but to achieve a more natural result, it is necessary to increase the number of steps.
* To use PixelTiledKSampleUpscalerProviderPipe, you need BlenderNeko's [ComfyUI_TiledKSampler](https://github.com/BlenderNeko/ComfyUI_TiledKSampler) extension.

### Original
![orignial](extreme-upscale-original.png)

### Upscale (x4)
![x4](extreme-upscale-x4.png)

### Upscale (x8)
![x8](extreme-upscale-x8.png)