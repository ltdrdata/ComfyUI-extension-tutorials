## PK_HOOK (Pixel KSample Hook)

* PK_HOOK is used with PixelKSampleUpscaler for adjusting the settings according to the progress of the step during Iterative Upscale.
* Currently, the following three nodes are provided related to PK_HOOK.
    * DenoiseScheduleHookProvider - IterativeUpscale provides a hook that gradually changes the denoise to target_denoise as the step progresses.
    * CfgScheduleHookProvider - IterativeUpscale provides a hook that gradually changes the cfg to target_cfg as the step progresses.
    * PixelKSampleHookCombine - This is used to connect two PK_HOOKs. hook1 is executed first and then hook2 is executed.


* The workflow can be configured as follows:
    * One decreases denoise from 0.7 to 0.4 and increases cfg from 3 to 15 at the beginning.
    * The other increases denoise from 0.1 to 0.6 and decreases cfg from 25 to 3 at the beginning.
![workflow](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/PK_HOOK-workflow.png)


* The following is the result of applying the above workflow.
![compare](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/PK_HOOK-compare.png)


* The left one is the original, the middle one has denoise increased/cfg decreased applied, and the right one has denoise decreased/cfg increased applied.

<img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/PK_HOOK-original.png" width="300"/> <img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/PK_HOOK-IncDenoise-DecCFG.png" width="300"/> <img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/PK_HOOK-DecDenoise-IncCFG.png" width="300"/>


* If you decrease the strength of denoise from high level denoise, initially the image may become distorted, but gradually it can be reconstructed into a new image.
<img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/PK_HOOK2-steps.jpg"/>
<img src="https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/PK_HOOK2-result.png"/>