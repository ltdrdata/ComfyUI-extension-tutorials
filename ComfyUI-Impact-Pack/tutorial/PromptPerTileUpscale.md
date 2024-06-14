# Prompt per Tile Upscale (Highres img2img / I2I)

This workflow is for upscaling a base image by using tiles. The difference to well-known upscaling methods like Ultimate SD Upscale or Multi Diffusion is that we are going to give each tile its individual prompt which helps to avoid hallucinations and improves the quality of the upscale.

## Requirements

This tutorial requires the use of an external ComfyUI custom node: https://github.com/pythongosssss/ComfyUI-WD14-Tagger

Please install before running the workflow, otherwise it won't work.

**Remark**: Using control net tile is helpful as well but it is not used in this tutorial and it is not required.

## Workflow

![prompt-per-tile](https://github.com/jkrauss82/ComfyUI-extension-tutorials/assets/3026023/28695cb0-85bc-4d22-8dfd-a235755ccdf2)

The idea is to automatically create a wildcard prompt for the SEGS detailer. The purpose is to have a single prompt per tile/SEG which guides the denoising process for each tile.

Detailers in the Impact Pack support a special syntax in the wildcard input which is based on assigning labels to SEGs. You can read more about this [here](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/d6559e05557134f22c1136b2d38106016447e84c/ComfyUI-Impact-Pack/tutorial/ImpactWildcard.md#special-syntax-for-detailer-wildcard).

This strategy helps to be able to either increase the denoising strength for added details or protect against hallucinations at the same denoise level.

The [WD14 tagger](https://github.com/pythongosssss/ComfyUI-WD14-Tagger) from pythongosssss works nicely for assigning tags on the fly for each tile.

Let's go through the workflow step by step. The headlines below correspond to the groups in the workflow and explain what each group does.

### Base Image

Nothing fancy here, just generating two different subjects to have something to work with.

### Upscale and Create Tiles

We upscale the image to the desired size (you can adapt this of course) and create the tiles which will be run through the detailer later.

The size of the tiles is set to a value where it produces an image dimension close to the default of SD1.5 (768px). In case you want to use SDXL for the upscale (or another model like Stable Cascade or SD3) it is recommended to adapt the tile size so it matches the model's capabilities (consider the overlap px to reduce the number of required tiles).

### Tag Base Image

We tag the base image to have some sort of boundary for the tags assigned to each tile. This is to prevent the WD14 tagger to go beyond what is seen in the original image when looking at each tile. The output of the WD14 tagger is fed into a node which converts the list of strings to a single string to match the expected input of the wilcard prompt to string list node.

### Tag Tiles

Each tag is run through the WD14 tagger and get its individual tags. Again, we convert the list of strings to a single string to match the required input of the downstream node receiving it.

### Assign Prompt to Tiles

This step converts the received string into the format recognized by the detailer using labels. We also have converted the input "restrict_to_tags" to a widget (meaning we do not have a string input field on the node but have a connector which receives input from another node) so we can feed the restricted tags right into the node.

We also apply some desired style features to the input field `postfix_all`. This is so we can control for what the general style of the image is. We also add some custom tags to be exluded so we do not confuse the model while it is working on each tile.

### Add Details

The denoising takes place in this last step. Notice we are using a rather high denoising level of .5 which is more than is normally recommended for Ultimate SD Upscale or Multi Diffusion and we are not even using a control net here!

As each tile is receiving its individual prompt the model is able to better generate details and avoid producing undesired artifacts.

For best results for realistic generations a denoising value of .35 is recommended together with dpmpp 2m sde and a cfg around 2-3. For anime/comic more testing is needed.

Performance can be increased even more when using control net tile, a recommended strength value is from .4 - .75 depending on the generation.
