## Advanced Iterative Upscale (TwoSamplersUpscaleProvider, KSampleProvider, TiledKSampleProvider)

* To apply a sample to the masked and non-masked areas separately using the [TwoSamplersForMask](TwoSamplers.md) node in Upscale, you need to use the **TwoSamplersForMask Upscaler Provider** node.

* The workflow can be set up as below.

![workflow](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSamplersUpscale-workflow.png)

* You can see that TwoSamplersForMask has been extended with full_sample_opt and pk_hook_full options. These options are provided to alleviate artifacts by applying a unified sample instead of separated sample operations during the iterative upscale process. full_sample_opt provides the KSAMPLE to use for the unified sample, or base_sample if it is omitted. pk_hook_full is a hook applied to the unified sample.


![schedule](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSamplersUpscale-schedule.png)

* The full_sample_schedule sets the schedule for applying the unified sample during iterative upscale.
  * none - do not apply
  * interleave1 - start with separate samples and alternate with full sample
  * interleave2 - start with separate samples and repeat separate sample twice, then apply full sample once, and repeat
  * interleave3 - start with separate samples and repeat separate sample three times, then apply full sample once, and repeat
  * last1 - apply full sample on the last iteration
  * last2 - apply full sample on the last two iterations
  * interleave1+last1 - apply interleave1 and last1 schedules simultaneously
  * interleave2+last1 - apply interleave2 and last1 schedules simultaneously
  * interleave3+last1 - apply interleave3 and last1 schedules simultaneously

* The original image on the left has been masked as shown on the right. And separate samplers have been applied to the masked area and the area outside the mask.

<img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSamplersUpscale-original.png" width="350"/> <img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSamplersUpscale-masked.png" width="350"/>

* The image on the left has a single sampler applied instead of TwoSamplers. The image in the middle has **full_sample_schedule** set to **none** in TwoSamplers. The image on the right has **full_sample_schedule** set to **interleave2+last1**.
* It can be observed that the results differ slightly for the masked area.

<img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSamplersUpscale-single.png" width="350"/> <img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSamplersUpscale-none-schedule.png" width="350"/> <img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/TwoSamplersUpscale-i2l1-schedule.png" width="350"/>