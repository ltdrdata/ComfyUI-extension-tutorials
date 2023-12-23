## Batching with Detailer

All Detailer nodes, except for `FaceDetailer` and `Detailer For AnimateDiff`, do not allow image batch inputs. Because `SEGS` is already representing multiple parts within a single image, turning images into batches can lead to confusion. Therefore, we recommend handling this explicitly through List conversion.
* FaceDetailer internally handles SEGS only, so it has allowed batches since V4.50.

This tutorial explains how to convert Batch to List and List to Batch.

### Rebatch Latents

* Batch processing can only be applied to the latent space and cannot be applied to the pixel image targeted by the detailer.

* You can use the **Rebatch Latents** node to remove separate latent images from their batches.

* Once the latent image is passed through the **Rebatch Latents** node, no batch processing can be done. Putting the node directly before **VAE Decode** will allow your primary samplers to run with batched latents and will only unbatch them before VAE decoding.

* Use a **batch_size** of 1 to obtain unbatched latents.

![workflow](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/batching-detailer.png)


### Image Batch to Image List

* If you want to maintain as many batches as possible during the latent stage, you can convert the output, which is turned into an image through VAE Decode, into a format that FaceDetailer can handle using ImageBatchToImageList, just before the input to FaceDetailer.

![workflow2](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/imagebatch_to_imagelist.png)


## Image List to Image Batch

* When you need to convert post-processed images, which have been converted into a list, back into a batch for handling, you can use the `Image List to Image Batch` node to convert them back into batch images and work with them using tools like the detailer.

![workflow3](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/imagelist_to_imagebatch.jpg)