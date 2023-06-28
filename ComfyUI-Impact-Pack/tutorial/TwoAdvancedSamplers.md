## TwoAdvancedSamplersForMask

* Similar to the existing TwoSamplersForMask, you can apply separate KSamplers to the masked area and the area outside the mask. However, TwoSamplersForMask applies the sampling process for the mask area only after the sampling process for the background is complete. On the other hand, TwoAdvancedSamplersForMask applies alternately at each step, allowing for smooth blending even if the denoise is set to 1.0. With this node, you can apply separate Lora to each area.

![workflow](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoAdvancedSamplers-workflow.png)

* If the mask is not used, you can see that only the base_sampler is applied.

![orignal](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoAdvancedSampler-original.png)
![result](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoAdvancedSampler-result.png)

* If you watch this video, you can see the difference in the operation methods between TwoSamplersForMask and TwoAdvancedSamplersForMask.
![video](https://www.youtube.com/watch?v=S1ksTNs5VX0)