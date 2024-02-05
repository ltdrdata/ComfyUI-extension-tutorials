# Detailers

The Detailer enlarges images and internally utilizes KSampler to inpaint the images. Consequently, it shares common options with KSampler and additionally possesses the following options:

* `guide_size`: This is the reference size for the Detailer. Target images in SEGS smaller than `guide_size` are enlarged to match `guide_size`, while images already larger than `guide_size` are skipped, as they are deemed not requiring detailing. Enlarge the shorter side of the target image to match the `guide_size`.
* `guide_size_for`: Determines what the `guide_size` is based on. When set to `bbox`, it uses the bbox detected by the detector as the reference. When set to `crop_region`, it uses the recognized crop region based on the detected bbox as the reference. (Refer to the [detectors](detectors.md) section for more details on `bbox` and `crop_region`.)
 - When using bbox, be cautious as the size of the enlarged image based on the crop_factor can be several times larger than the guide_size.
* `max_size`: This is a safety measure that restricts the longer side of the target image to be smaller than `max_size`. It addresses issues where the bbox may become excessively large, particularly if it has an elongated shape.
* `force_inpaint`: Prevents skipping the detailing process based on `guide_size` and applies inpainting regardless. This is useful when the objective is inpainting rather than detailing.SEGS smaller than the guide_size are not reduced to match the guide_size; instead, they are inpainted at their original size.

* `noise_mask`: This determines whether to apply a noise mask during the inpainting process. While a lower denoise value may sometimes yield more natural results with the noise mask turned off, it's generally recommended to have it activated.
* `inpaint_model`: When using an inpaint model, this option needs to be enabled to ensure proper inpainting at denoise values below 1.0.
* `noise_mask_feather`: This determines whether to feather the mask applied to the inpainting process. This option does not guarantee a more natural image; in fact, it may create artifacts along the edges.
* `refiner_ratio`: When using SDXL, this setting determines the proportion of the refiner step to apply out of the total steps.
* `cycle`: This setting determines the number of iterations for applying sampling in the Detailer. When used in conjunction with the Detailer hook, this option allows for the addition of intermittent noise and can also be used to gradually decrease the denoise size, initially establishing the basic structure and then refining it.


# WIP... 

## SEGSDetailer

* `batch_size`:

## Detailer (SEGS)

## FaceDetailer


# Detailers for AnimateDiff

## SEGSDetailer For AnimateDiff

## Detailer For AnimateDiff
