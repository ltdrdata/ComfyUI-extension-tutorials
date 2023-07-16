# SAMDetection Application

![sam-workflow-example](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/sam.png)
![sam-example-original](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/sam-original.png) ![sam-example-masked](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/sam-masked.png) ![sam-example-result](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/sam-result.png) 

* By using the segmentation feature of SAM, it is possible to automatically generate the optimal mask and apply it to areas other than the face. The image on the left is the original image, the middle image is the result of applying a mask to the alpha channel, and the image on the right is the final result.

* The ```detection_hint``` in ```SAMDetector (Combined)``` is a specifier that indicates which points should be included in the segmentation when performing segmentation. ```center-1``` specifies one point in the center of the mask, ```horizontal-2``` specifies two points on the center horizontal line, ```vertical-2``` specifies two points on the center vertical line, ```rectangle-4``` specifies four points in a rectangular shape inside the mask, and ```diamond-4``` specifies four points in a diamond shape centered around the center point.

* Unlike in face detection, for non-rigid objects, the center point may not always be the segmentation area, so be careful not to assume that the center point is always the segmentation area.
