## Extreme Highresolution Upscale

![example](extreme-upscale-full.png)
![workflow](extreme-upscale-workflow.png)

* Using PixelTiledKSampleUpscalerProviderPipe can help avoid VRAM shortage issues. Here, we reduced the number of steps to quickly obtain results, but to achieve a more natural result, it is necessary to increase the number of steps.
* To use PixelTiledKSampleUpscalerProviderPipe, you need BlenderNeko's [ComfyUI_TiledKSampler](https://github.com/BlenderNeko/ComfyUI_TiledKSampler) extension.

![orignial](extreme-upscale-original.png)
![x8](extreme-upscale-x8.png)