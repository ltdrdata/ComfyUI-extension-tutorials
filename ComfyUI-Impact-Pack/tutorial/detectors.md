# Detectors

The Impact Pack's Detector includes three main types: BBOX, SEGM, and SAM.
* The Detector detects specific regions based on the model and returns processed data in the form of SEGS.
  - `SEGS` is a comprehensive data format that includes information required for **Detailer operations**, such as `masks`, `bbox`, `crop regions`, `confidence`, `label`, and `controlnet` information.
  - Through SEGS, conditioning can be applied for Detailer[[ControlNet](https://www.youtube.com/watch?v=RoWzBo9I0MQ)], and SEGS can also be categorized using information such as labels or size within SEGS[[SEGSFilter](https://www.youtube.com/watch?v=4IjplfhDU60), [Crowd Control](https://www.youtube.com/watch?v=9GSQlxZFrLI)].
  - 

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