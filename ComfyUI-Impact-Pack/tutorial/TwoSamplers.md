## TwoSamplersForMask

* Using TwoSamplersForMask, it is possible to apply different levels of denoising or cfg to different parts of an image. Particularly, it can be applied to specific areas such as hands with low denoising and cfg using the mask of CLIPSeg.

* The workflow for utilizing TwoSamplersForMask is as follows:

![workflow](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSamplers-workflow.png)

* If the mask is not used, you can see that only the base_sampler is applied.

![orignal-no-mask](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/
ComfyUI-Impact-Pack/images/TwoSampler-original-no-mask.png)

![orignal-mask](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSampler-original-mask.png)

* If a mask is applied to the lower body, you can see that the base_sampler is applied to the upper body and the mask_sampler is applied to the lower body with a high cfg of 50.

![result-no-mask](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/
ComfyUI-Impact-Pack/images/TwoSampler-result-no-mask.png)

![result-mask](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSampler-result-mask.png)