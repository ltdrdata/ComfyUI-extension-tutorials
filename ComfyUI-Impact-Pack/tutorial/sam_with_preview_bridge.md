# Interactive SAM + PreviewBridge

* By utilizing Interactive SAM Detector and PreviewBridge node together, you can perform inpainting much more easily.

![workflow](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/interactive-sam-workflow.png)

* After executing PreviewBridge, open Open in SAM Detector in PreviewBridge to generate a mask. Add positive points (blue) that should be detected by left-clicking and negative points (red) that should be excluded by right-clicking. Press the detect button to perform detection, and then save it to the node using the save to node option.

![select](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/interactive-sam-select.png)

* And then execute it. You will observe that a mask is generated for that specific area, and only that region will be inpainted. The original image now includes yellow pantyhose.
![result](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/interactive-sam-workflow-result.png)
![original](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/interactive-sam-original.png)
![result](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/interactive-sam-result.png)