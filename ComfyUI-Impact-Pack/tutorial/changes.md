# Changes from V8.31 to V9.1.1

What changed in the Impact Pack from V8.31 to V9.1.1, with the tutorial that describes each change.

## New

| Change | Since | Tutorial |
| --- | --- | --- |
| `Detailer Advanced Options Hook`: an image for each cycle, VAE tiling for encode and decode, crop outputs at the sampled size | V8.31 | [Detailer Advanced Options](detailer_advanced_options.md), [Detailer Hooks](detailer_hooks.md) |
| `Decompose (SEGS list)` and `Assemble (SEGS list)` | V8.31 | [Decompose (SEGS list) / Assemble (SEGS list)](segs_list.md) |
| `alpha_as_mask` of `Preview Bridge (Image)` | V8.31 | [PreviewBridge](previewbridge.md#rgba-images-alpha_as_mask) |
| Simplified Chinese translation of the nodes | V8.31 | [Language of the nodes](locale.md) |
| SAM models from `.safetensors` files | V9.0.2 | [Detectors](detectors.md#sam-sam2-and-sam3-models) |
| SAM3 models in `SAMLoader (Impact)` and the SAM detectors, `SAM3 Video Detector (SEGS)` | V9.1 | [Detectors](detectors.md#sam-sam2-and-sam3-models), [video detectors](detectors.md#sam2-video-detector--sam3-video-detector) |
| SAM2 models in `SAMDetector (segmented)` | V9.1 | [Detectors](detectors.md#sam-sam2-and-sam3-models) |

## Changed

| Change | Since | Tutorial |
| --- | --- | --- |
| The tiled sampler of `TiledKSamplerProvider` and `PixelTiledKSampleUpscalerProvider` is included. The `ComfyUI_TiledKSampler` extension is not needed. | V9.0 | [Advanced Iterative Upscale](TwoSamplersUpscale.md) |
| 51 nodes are defined on the V3 schema of ComfyUI. Names, inputs, outputs and results are the same. | V9.0 | [Language of the nodes](locale.md) |
| `sam2` is installed from `requirements-sam2.txt`, not with `requirements.txt`. | V9.1 | [Detectors](detectors.md#sam-sam2-and-sam3-models) |

## Fixed

| Fix | Since | Tutorial |
| --- | --- | --- |
| A Detailer patch came back larger than the area it replaced at sizes the VAE cuts. The same for `Upscaler (SEGS)`. | V8.31 | [Detailers](detailers.md) |
| `Conditional Stop Iteration` did not end Run (Instant) and Run (On Change). | V8.31 | [Conditional Stop Iteration](stop_iteration.md) |
| `Preview Bridge (Image)` wrote a restored mask into an RGBA input. | V8.31 | [PreviewBridge](previewbridge.md#rgba-images-alpha_as_mask) |
| Descriptions and tooltips that named an option that does not exist, and Korean labels on the wrong socket. | V8.31, V9.0 | [Language of the nodes](locale.md) |
| `TwoSamplersForMask` left its mask on the latent it was given. | V9.0 | [TwoSamplersForMask](TwoSamplers.md) |
| `GITSScheduler Func Provider` failed on ComfyUI 0.3.63 or later with `'GITSScheduler' object has no attribute 'get_sigmas'`. | V9.0 | |
| `Concat Conditionings` failed with one conditioning linked. It now returns that conditioning. | V9.0 | |
| `String List to String` changed the separator string it was given for the other nodes that read it. | V9.0 | |
| `FaceDetailer` left its default prompt on a CLIPSeg detector when the detection failed or was interrupted. | V9.0 | |
| A new installation logged an error on every start because `impact-pack.ini` was missing. The pack now creates the file. | V9.0.1 | [ImpactWildcard](ImpactWildcard.md) |
| `Open in MaskEditor` and `Open in SAM Detector` did nothing on a Preview Bridge without an image. They now show a notice. | V9.0.2 | [PreviewBridge](previewbridge.md#a-preview-bridge-without-an-image) |
| `MASK to SEGS` with `combined` gave a bbox that was one pixel short, and `bbox_fill` did not fill the box of the mask. | V9.1.1 | [Detectors](detectors.md#mask-to-segs) |
| The `[ALL]` prefix of a `[LAB]` wildcard was joined to the prompt of the label with nothing between them. | V9.1.1 | [ImpactWildcard](ImpactWildcard.md#special-syntax-for-detailer-wildcard) |
| The Regional Samplers and `TwoAdvancedSamplersForMask` returned noise with a sampler that draws its own noise, such as `euler_ancestral`. | V9.1.1 | [Regional Sampler](regional_sampler.md#samplers-that-draw-their-own-noise) |
| The `device_mode` of `SAMLoader (Impact)` did not work for a SAM2 model. | V9.1.1 | [Detectors](detectors.md#sam-sam2-and-sam3-models) |
| A `POST /prompt` without a `prompt`, such as a prompt posted as the body itself, logged `Error on prompt - several features will not work` with a traceback. ComfyUI answers such a request with `400 no_prompt` as before. | V9.1.1 | |
