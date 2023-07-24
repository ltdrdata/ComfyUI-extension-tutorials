## Batching with Detailer

* Batch processing can only be applied to the latent space and cannot be applied to the pixel image targeted by the detailer.

* You can use the **Rebatch Latents** node to remove separate latent images from their batches.

* Once the latent image is passed through the **Rebatch Latents** node, no batch processing can be done. Putting the node directly before **VAE Decode** will allow your primary samplers to run with batched latents and will only unbatch them before VAE decoding.

* Use a **batch_size** of 1 to obtain unbatched latents.

![workflow](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/batching-detailer.png)