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


## BBOX Detector (SEGS) and SEGM Detector (SEGS)

`BBOX Detector (SEGS)` (`BboxDetectorSEGS`) takes a `BBOX_DETECTOR`, and `SEGM Detector (SEGS)` (`SegmDetectorSEGS`) takes a `SEGM_DETECTOR`. Both return the detections in an image as `SEGS`, and both have the same inputs.

* `threshold`: a detection that the model scores lower than this is left out. Lower it when an object is missed, raise it when other things are found as well.
* `dilation`: grows the mask of each detection, with a square kernel of this size in pixels. With a BBOX detector the mask is a box, and it stays a box, only larger.
* `crop_factor`: the crop region is this many times as wide and as high as the bbox, and at most the size of the image.
* `drop_size`: a detection is kept only when its bbox is wider and higher than this many pixels. Raise it to leave out small faces in the background.
* `labels`: the labels to keep, separated by commas, such as `person, handbag`. `all` (the default) keeps every detection.
  * The label of a detection comes from the model. `Decompose (SEGS)` and `From SEG_ELT` show the label of each SEG, as the workflow [detector-labels.png](../workflow/detector-labels.png) does.
* `detailer_hook` (optional): a hook that filters the detections, such as the SEGS filter hooks of [Detailer hooks](detailer_hooks.md#choose-which-detections-to-refine).
* The nodes take one image. A batch of several images stops with an error. Turn the batch into a list as [Batching with Detailer](batching-detailer.md) shows: the node then runs once for each image.


## BBOX Detector (combined) and SEGM Detector (combined)

`BBOX Detector (combined)` (`BboxDetectorCombined_v2`) and `SEGM Detector (combined)` (`SegmDetectorCombined_v2`) return a `MASK` in place of `SEGS`. It is one mask in the size of the image that holds every detection.

* Take them where a plain mask is wanted, such as for inpainting or a mask operation.
* `threshold` and `dilation` work as above. `dilation` is 4 by default for the BBOX node and 0 for the SEGM node.
* There is no `crop_factor`, `drop_size` or `labels`. The mask holds every detection above `threshold`, whatever its label.
* When nothing is found, the mask is empty.

[![The BBOX and SEGM detectors, as SEGS and as a mask](../workflow/detectors-segs-and-mask.png)](../workflow/detectors-segs-and-mask.png)

* `UltralyticsDetectorProvider` loads a face model and a person model. The SEGM nodes take the `SEGM_DETECTOR` output of the person model.
* `BBOX Detector (SEGS)` returns a SEG for each face. `SEGSPreview` shows the crop region of each SEG, and its box as the mask.
* `SEGM Detector (SEGS)` returns a SEG for each person, with the silhouette as the mask. Its `crop_factor` is 1.5, since a person already fills much of the picture.
* The two combined detectors return one mask with all faces and one mask with all people.
* The example image is [sam-two-people.png](../images/sam-two-people.png).


## ONNX Detector (SEGS/legacy)

`ONNX Detector (SEGS/legacy) - use BBOXDetector` (`ONNXDetectorSEGS`) is `BBOX Detector (SEGS)` under an old name. It has the same inputs and gives the same result, and it stays so that old workflows still load. Take `BBOX Detector (SEGS)` in a new workflow.

* `ONNXDetectorProvider` loads an `.onnx` model from `models/onnx` and returns a `BBOX_DETECTOR`. Give it to `BBOX Detector (SEGS)` like any other `BBOX_DETECTOR`.
* The ONNX detector was written for the NudeNet model `detector_v2_base_checkpoint.onnx`. [ONNX.md](ONNX.md) says where to get it and has a workflow.
* With another ONNX model the detection can fail. The log then shows `ONNXDetector: unable to execute.`, and the detector node stops with an error.
* The labels of its SEGS are the class numbers of the model, such as `0` or `2`. Write these numbers in `labels`.


## SAM Detector

The `SAMDetector` node loads the `SAM` model through the `SAMLoader` node for use. 
* Please note that it is not compatible with `WAS-NS's SAMLoader`.

* The following workflow demonstrates the use of the `BBOX Detector` to generate `SEGS`, and then using the `SAMDetector` for creating more precise segments.
![workflow2](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/detectors-sam.png)

### SAM, SAM2 and SAM3 models

`SAMLoader (Impact)` selects the kind of model by the file name, and the nodes that take a `SAM_MODEL` work with each of them.

| Model | Files | What it needs |
| --- | --- | --- |
| SAM | `.pth`, `.pt` or `.safetensors` files in `models/sams`. A name with `vit_h` or `vit_l` selects that model, any other name is ViT-B. | Nothing more. The `.safetensors` form, such as the files of [kornia/sam](https://huggingface.co/kornia/sam), loads since V9.0.2. |
| SAM2 | `sam2_hiera_*.pt` and `sam2.1_hiera_*.pt` in `models/sams`, under their original names. | The `sam2` package. Since V9.1 it is not installed with `requirements.txt`: run `pip install -r requirements-sam2.txt` in the Impact Pack directory with the Python of ComfyUI. |
| SAM3 | A file whose name starts with `sam3`, in `models/sams` or `models/checkpoints`, such as [`sam3.1_multiplex_fp16.safetensors`](https://huggingface.co/Comfy-Org/sam3.1/resolve/main/checkpoints/sam3.1_multiplex_fp16.safetensors). | Impact Pack V9.1 or later and ComfyUI v0.21.0 or later. No extra package: it runs on ComfyUI's own SAM3. |

* A SAM3 model works in `SAMDetector (combined)`, `SAMDetector (segmented)`, the `Simple Detector` nodes and the `sam_model_opt` of the detailers. Since V9.1 a SAM2 model works in `SAMDetector (segmented)` too.
* SAM3 returns one mask for a prompt, without a score. The `threshold` of a SAM detector does not apply to it.
* The SAM editor (`Open in SAM Detector`) takes SAM models only.

`device_mode` says where a SAM or SAM2 model is kept between detections.
* `AUTO`: the model moves to the GPU for a detection and back to the CPU afterwards.
* `Prefer GPU`: the model moves to the GPU once and stays there. When the VRAM is short it stays on the CPU, with a line in the log.
* `CPU`: the model runs on the CPU.
* Since V9.1.1 the three modes work for a SAM2 model as well. Before, `Prefer GPU` failed in `SAMLoader (Impact)` and `CPU` ran on the GPU.
* `device_mode` does not apply to a SAM3 model. ComfyUI's model management places it.

The workflow below gives the two faces that the `BBOX Detector` finds to a SAM3 model. `SAMDetector (combined)` returns one mask with both faces, and the `batch_masks` of `SAMDetector (segmented)` holds a mask for each face. To use another model, select it in `SAMLoader (Impact)`. The rest of the workflow stays as it is.

[![SAM3 in the SAM detectors](../workflow/sam3-detectors.png)](../workflow/sam3-detectors.png)

* The example image is [sam-two-people.png](../images/sam-two-people.png).


## Simple Detector

The `Simple Detector` first performs primary detection using `BBOX_DETECTOR` and then generates `SEGS` using the intersected mask from the detection results of `SEGM_DETECTOR` or `SAMDetector`.

* `sam_model_opt` and `segm_detector_opt` are both optional inputs, and if neither of them is provided, the detection results from BBOX are returned as is. When both inputs are provided, `sam_model_opt` takes precedence, and the `segm_detector_opt` input is ignored.

* `SAM` has the disadvantage of requiring direct specification of the target for segmentation, but it generates more precise silhouettes compared to `SEGM`. The workflow below is an example of compensate `BBOX` with `SAM` and `SEGM`. In the case of `person SEGM`, it creates a silhouette that includes the entire `face BBOX`, resulting in a silhouette that is too rough and doesn't show significant improvement.

![workflow3](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/detectors-simple.png)

### Inputs of the Simple Detector

* `bbox_threshold`, `bbox_dilation`, `crop_factor` and `drop_size` are the `threshold`, `dilation`, `crop_factor` and `drop_size` of the BBOX detection, as in `BBOX Detector (SEGS)`.
* `sub_threshold` and `sub_dilation` are the `threshold` and `dilation` of the second step, SAM or the SEGM detection.
* `sub_bbox_expansion` widens the box that SAM gets by this many pixels on every side. It applies to SAM only.
* The mask of each SEG is the part of the SAM or SEGM mask that lies inside the mask of its BBOX detection. Raise `bbox_dilation` when the silhouette reaches out of the box.
* `post_dilation` grows the final mask of each SEG. A negative value shrinks it.
* There is no `labels` input. Filter the result with `SEGS Filter (label)`.
* `Simple Detector (SEGS)` takes one image, as `BBOX Detector (SEGS)` does.

### Simple Detector (SEGS/pipe)

`Simple Detector (SEGS/pipe)` (`ImpactSimpleDetectorSEGSPipe`) takes `bbox_detector`, `segm_detector_opt`, `sam_model_opt` and `detailer_hook` from a `DETAILER_PIPE`, such as the one of `ToDetailerPipe` or the `detailer_pipe` output of `FaceDetailer`. The other inputs and the rules are those of `Simple Detector (SEGS)`, and the `detailer_hook` of the pipe applies to the detection as in `BBOX Detector (SEGS)`.


## Simple Detector For AnimateDiff

`Simple Detector For AnimateDiff` is a detector designed for video processing, such as AnimateDiff, based on the Simple Detector.

* In the node menu it is `Simple Detector for Video (SEGS)` (`ImpactSimpleDetectorSEGS_for_AD`). It takes the frames as a batch in `image_frames`, runs the detection of the Simple Detector on each frame, and returns one `SEGS` for all frames. It has no `post_dilation`.
* The basic configuration is similar to the Simple Detector, but additional features such as `masking_mode` and `segs_pivot` are provided.
* `masking_mode` configures how masks are composed:
  * `Pivot SEGS`: Utilizes only one mask from the segs specified by `segs_pivot`. When used in conjunction with the Combined mask, it employs a unified mask that encompasses all frames.
  * `Combine neighboring frames`: Constructs each frame's mask by combining three masks, including the current frame and its adjacent frames. This method occasionally compensates for detection failures in specific frames.
  * `Don't combine`: Uses the original mask for each frame without combining them.
  * With `Combine neighboring frames` and `Don't combine`, the mask of each SEG is a batch with a mask for every frame.
* `segs_pivot` sets the mask image that serves as the basis for identifying SEGS.
  * `Combined mask`: Combines the masks of all frames into one frame before identifying separate masks for each SEGS.
  * `1st frame mask`: Identifies separate masks for each SEGS based on the mask of the first frame.
  * Each option has its advantages and disadvantages. For instance, when `1st frame mask` is selected, if the target object moves rapidly, the SEGS created based on the mask of the first frame might extend beyond its initial boundaries in later frames. Conversely, when using the `Combined mask`, if moving objects overlap, they may be recognized as a single object.

  ![node](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/simple-detector-for-ad.jpg)


## SAM2 Video Detector / SAM3 Video Detector

`SAM2 Video Detector (SEGS)` and `SAM3 Video Detector (SEGS)` track objects through the frames of a video.

* The `bbox_detector` finds the objects in the first frame. The SAM model then follows each of them through all frames.
  * When the first frame has no object, the last frame is tried, and the objects are followed backwards.
* The result is a `SEGS` with one SEG for each object. The mask of a SEG is a batch that holds the mask of that object in every frame.
* `SAM2 Video Detector (SEGS)` needs a SAM2 model, and `SAM3 Video Detector (SEGS)` (V9.1 or later) needs a SAM3 model. Each node reports the other kind of model as an error.
* `sam2_threshold` of the SAM2 node is the value from which a pixel belongs to the mask. The SAM3 node has no such input: SAM3 returns masks.

In the workflow below, the `BBOX Detector` model finds two faces in the first of 25 frames. `SEGS to Mask Batch` shows the result: 50 masks, the 25 frames of the first face and then the 25 frames of the second.

[![SAM3 Video Detector (SEGS)](../workflow/sam3-video-detector.png)](../workflow/sam3-video-detector.png)

* The example video is [sam-video-example.mp4](../images/sam-video-example.mp4). `Load Video` and `Get Video Components` are nodes of ComfyUI. A batch of frames from any other node works the same way.


## MASK to SEGS

`MASK to SEGS` makes `SEGS` from a mask, so that a mask from any source can be given to the Detailer.

* `combined` off: every separate area of the mask becomes a SEG of its own.
* `combined` on: the whole mask becomes one SEG. Its bbox is the box around all areas.
* `bbox_fill`: the mask of each SEG is replaced by its bbox. With `combined` on, that is the box around all areas.
* `crop_factor`: the size of the crop region around the bbox, as in the detectors.
* `MASK to SEGS for Video` does the same for a batch of masks.

[![MASK to SEGS with combined and bbox_fill](../workflow/mask-to-segs-combined.png)](../workflow/mask-to-segs-combined.png)

* Since V9.1.1 the bbox of a `combined` SEG ends where the mask ends. Before, it was one pixel short on the right and at the bottom, and `bbox_fill` with `combined` on did not fill the box of the mask. A workflow that uses `combined` gets results that differ by these pixels.


## Classifying SEGS

`SEGS Classify` (`ImpactSEGSClassify`) splits a `SEGS` in two by what an image classifier sees in each SEG, such as female and male faces. The classifier is a model from Hugging Face that `HF Transformers Classifier Provider` (`ImpactHFTransformersClassifierProvider`) loads.

### HF Transformers Classifier Provider

* `preset_repo_id`: the Hugging Face repository of an image classification model, such as `rizvandwiki/gender-classification-2`, a model for gender classification. `Manual repo id` takes the repository written in `manual_repo_id`.
* The node downloads the model from Hugging Face the first time it runs, and reads it from the Hugging Face cache after that. The environment variable `HF_HOME` sets where the cache is.
* `device_mode`: `CPU` runs the model on the CPU. `AUTO` and `Prefer GPU` do the same: the model runs on the device of ComfyUI, normally the GPU.

### SEGS Classify

* The classifier looks at the crop region of each SEG, not only at its bbox. The `crop_factor` of the detector decides how much of the surroundings it sees.
* `preset_expr` holds the condition. `Manual expr` takes the condition written in `manual_expr`.
* A condition is one comparison of a label with a number or with another label, such as `female > 0.5` or `#Female > #Male`. The operators are `>`, `<`, `>=`, `<=` and `=`.
  * A label stands for the score the model gives the SEG for that label. The labels are those of the model: see the `config.json` of its repository, or the `detected_labels` output.
  * `#Female` stands for whichever of `female`, `Female`, `Human Female`, `woman`, `women` and `girl` the model returns, and `#Male` for `male`, `Male`, `Human Male`, `man`, `men` and `boy`. One condition then works with models that name their labels differently.
  * Text after the comparison is ignored, so `and` and `or` do not work. Write a number as `0.5`, not `.5`.
  * A label with a space in its name cannot be written in a condition. `#Female` and `#Male` cover `Human Female` and `Human Male`.
* `filtered_SEGS` holds the SEGs for which the condition holds, and `remained_SEGS` all others.
  * A SEG also goes to `remained_SEGS` when a label of the condition is not among the labels the model returned for it.
  * A condition that is not a comparison sends every SEG to `remained_SEGS`, without an error.
* `ref_image_opt`: the image the SEGS were detected in. SEGS from `ONNXDetectorProvider` or `MASK to SEGS` carry no picture of their own, and the classifier then reads their crop region from this image. Without it, such SEGs go to `remained_SEGS`.
* `detected_labels`: the labels the model returned for the SEGs, as a list in no fixed order. `rizvandwiki/gender-classification-2` returns `female` and `male`.

[![SEGS Classify with a gender classifier](../workflow/segs-classify.png)](../workflow/segs-classify.png)

* `BBOX Detector (SEGS)` finds the faces. `HF Transformers Classifier Provider` loads `rizvandwiki/gender-classification-2`, which is downloaded on the first run.
* `SEGS Classify` with `#Female > #Male` gives the faces that the model finds more female than male to `filtered_SEGS`, and the other faces to `remained_SEGS`. A `SEGSPreview` shows each part.
* `String List to String` joins the `detected_labels` into one text, and `Preview as Text` shows it.
* The example image is [sam-two-people.png](../images/sam-two-people.png).

The workflow [gender-senstivie-detailer.png](../workflow/gender-senstivie-detailer.png) refines the faces of a man and a woman with a prompt for each. `Simple Detector (SEGS)` finds the faces, `SEGS Classify` splits them with `#Female > 0.3`, and two Detailers take `remained_SEGS` and `filtered_SEGS`. Its classifier `NTQAI/pedestrian_gender_recognition` is downloaded on the first run as well.
