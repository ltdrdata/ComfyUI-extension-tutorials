# Detectors

The Impact Pack's Detector includes three main types: BBOX, SEGM, and SAM.
* The Detector detects specific regions based on the model and returns processed data in the form of SEGS.
  - `SEGS` is a comprehensive data format that includes information required for **Detailer operations**, such as `masks`, `bbox`, `crop regions`, `confidence`, `label`, and `controlnet` information.
  - Through SEGS, conditioning can be applied for Detailer[[ControlNet](https://www.youtube.com/watch?v=RoWzBo9I0MQ)], and SEGS can also be categorized using information such as labels or size within SEGS[[SEGSFilter](https://www.youtube.com/watch?v=4IjplfhDU60), [Crowd Control](https://www.youtube.com/watch?v=9GSQlxZFrLI)].
  - bbox: Detected regions are represented by rectangular bounding boxes consisting of left, top, right, and bottom coordinates.
  - mask: Represents the silhouette of the object within the bbox as a mask, providing a more precise delineation of the object's area. In the case of BBOX detector, the mask area covers the entire bbox region.
  - crop region: Determines the size of the region to be cropped based on the bbox.
    - When the bbox is formed near the border, the area on the opposite side is expanded, resulting in the bbox being off-centered within the crop region.
    - Having a larger crop region provides more context for a more natural inpaint, but it also increases the time required for inpainting.
    

* BBOX stands for Bounding Box, which captures detection areas as rectangular regions.
  - For example, using the `bbox/face_yolov8m.pt` model, you can obtain masks for the rectangular regions of faces.
  - This can be obtained using the `BBOX_DETECTOR` obtained through `UltralyticsDetectorProvider` or `ONNXDetectorProvider`.

* SEGM stands for Segmentation, which captures detection areas in the form of silhouettes.
  - For instance, when using the `segm/person_yolov8n-seg.pt` model, you can obtain silhouette masks for human shapes.
  - This can be obtained using the `SEGM_DETECTOR` obtained through `UltralyticsDetectorProvider`.

* SAM generates silhouette masks using the [Segment Anything](https://segment-anything.com/) technique.
  - It cannot be used independently but, when used in conjunction with a `BBOX` model to specify the target for detection, it can create finely detailed silhouette masks for the detected objects.


## Ultralytics Detector

The `UltralyticsDetectorProvider` node loads Ultralytics' detection models and returns either a `BBOX_DETECTOR` or `SEGM_DETECTOR`.
* When using a model that starts with `bbox/`, only `BBOX_DETECTOR` is valid, and `SEGM_DETECTOR` cannot be used.
* If using a model that starts with `segm/`, both `BBOX_DETECTOR` and `SEGM_DETECTOR` can be used.
* `BBOX_DETECTOR` and `SEGM_DETECTOR` can be used with the `BBOX Detector` node and `SEGM Detector` node, respectively. 

The workflow below is an example that utilizes `BBOX_DETECTOR` and `SEGM_DETECTOR` for detection.
![workflow](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/detectors.png)


## SAM Detector

The `SAMDetector` node loads the `SAM` model through the `SAMLoader` node for use. 
* Please note that it is not compatible with `WAS-NS's SAMLoader`.

* The following workflow demonstrates the use of the `BBOX Detector` to generate `SEGS`, and then using the `SAMDetector` for creating more precise segments.
![workflow2](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/detectors-sam.png)


## Simple Detector

The `Simple Detector` first performs primary detection using `BBOX_DETECTOR` and then generates `SEGS` using the intersected mask from the detection results of `SEGM_DETECTOR` or `SAMDetector`.

* `sam_model_opt` and `segm_detector_opt` are both optional inputs, and if neither of them is provided, the detection results from BBOX are returned as is. When both inputs are provided, `sam_model_opt` takes precedence, and the `segm_detector_opt` input is ignored.

* `SAM` has the disadvantage of requiring direct specification of the target for segmentation, but it generates more precise silhouettes compared to `SEGM`. The workflow below is an example of compensate `BBOX` with `SAM` and `SEGM`. In the case of `person SEGM`, it creates a silhouette that includes the entire `face BBOX`, resulting in a silhouette that is too rough and doesn't show significant improvement.

![workflow3](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/detectors-simple.png)


## Simple Detector For AnimateDiff

`Simple Detector For AnimateDiff` is a detector designed for video processing, such as AnimateDiff, based on the Simple Detector.

* The basic configuration is similar to the Simple Detector, but additional features such as `masking_mode` and `segs_pivot` are provided.
* `masking_mode` configures how masks are composed:
  * `Pivot SEGS`: Utilizes only one mask from the segs specified by `segs_pivot`. When used in conjunction with the Combined mask, it employs a unified mask that encompasses all frames.
  * `Combine neighboring frames`: Constructs each frame's mask by combining three masks, including the current frame and its adjacent frames. This method occasionally compensates for detection failures in specific frames.
  * `Don't combine`: Uses the original mask for each frame without combining them.
* `segs_pivot` sets the mask image that serves as the basis for identifying SEGS.
  * `Combined mask`: Combines the masks of all frames into one frame before identifying separate masks for each SEGS.
  * `1st frame mask`: Identifies separate masks for each SEGS based on the mask of the first frame.
  * Each option has its advantages and disadvantages. For instance, when `1st frame mask` is selected, if the target object moves rapidly, the SEGS created based on the mask of the first frame might extend beyond its initial boundaries in later frames. Conversely, when using the `Combined mask`, if moving objects overlap, they may be recognized as a single object.

![node](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/simple-detector-for-ad.png)