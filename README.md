# ComfyUI-DDColor

Node to use DDColor (https://github.com/piddnad/DDColor) in ComfyUI for automatic image and video colorization.

![image](https://github.com/kijai/ComfyUI-DDColor/assets/40791699/6c1bd9d1-8b8a-4c03-9768-806adf8b1920)

## Installation

1. Clone this repository into your ComfyUI `custom_nodes` folder
2. Install requirements: `pip install -r requirements.txt`
3. Restart ComfyUI

## Models

The models are selected from the dropdown, and automatically downloaded to the `/checkpoints` folder using huggingface_hub. Alternatively, download them manually from: https://huggingface.co/piddnad/DDColor-models/tree/main

| Model | Description | Best For |
|-------|-------------|----------|
| `ddcolor_paper_tiny.pth` | Fast, lightweight model (ConvNeXt-Tiny encoder) | Quick previews, lower VRAM |
| `ddcolor_paper.pth` | Balanced quality/speed (ConvNeXt-Large encoder) | General use |
| `ddcolor_modelscope.pth` | ModelScope variant | Specific use cases |
| `ddcolor_artistic.pth` | Artistic colorization style | Creative/artistic outputs |

More info: https://github.com/piddnad/DDColor/blob/master/MODEL_ZOO.md

## Node Parameters

- **image**: Input image (works with both grayscale and color images)
- **model_input_size**: Processing resolution (32-4096, default: 512). Higher values = better quality but slower and more VRAM. Recommended values: 256, 512, 1024
- **checkpoint**: Model selection dropdown

## Example Workflows

Example workflow JSON files are provided in the `example_workflows/` folder:

### 1. Simple Image Colorization (`simple_image_colorization.json`)
Basic workflow for colorizing single images:
- Load Image → DDColor_Colorize → Save Image

### 2. Video Colorization (`video_colorization_workflow.json`)
Colorize grayscale/B&W videos with audio preservation:
- Load Video → DDColor_Colorize → Preview + Export Video

### 3. Timecode Removal + Colorization (`timecode_removal_and_colorization.json`)
Advanced workflow combining timecode/text removal with colorization:
- Load Video → OCR Text Detection → Mask Expansion → ProPainter Inpainting → DDColor Colorization → Export

**Required nodes for the advanced workflow:**
- [ComfyUI-VideoHelperSuite](https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite) - Video loading/export
- [ComfyUI_ProPainter_Nodes](https://github.com/Fannovel16/ComfyUI_ProPainter_Nodes) - Video inpainting
- [llm_polymath](https://github.com/AppleBotzz/llm_polymath) - OCR text detection (polymath_text_mask)

## Usage Tips

### For Video Colorization
1. Use **VHS_LoadVideoFFmpegPath** to load your video
2. Connect the IMAGE output to **DDColor_Colorize**
3. Adjust `model_input_size` based on your video resolution:
   - 480p video: 256-512
   - 720p video: 512
   - 1080p video: 512-1024
4. Use **VHS_VideoCombine** to export with original audio

### For Batch Processing
The node automatically handles batches - feed multiple frames and it will colorize each one.

### Performance Optimization
- Use `ddcolor_paper_tiny.pth` for faster processing
- Lower `model_input_size` for speed (256 is fast, 1024 is slow but high quality)
- Process videos in segments if running out of VRAM

## Integrating with Other Workflows

DDColor works well with other ComfyUI nodes:

```
Load Video → [Optional: Upscale] → [Optional: Denoise] → DDColor_Colorize → [Optional: Post-process] → Export
```

Example integrations:
- **Upscaling**: Use ESRGAN/RealESRGAN before colorization for better results
- **Denoising**: Apply video denoising before colorization for cleaner output
- **Film Restoration**: Combine with stabilization, scratch removal, and grain management
- **Timecode Removal**: Use OCR detection + inpainting before colorization (see example workflow)

## Troubleshooting

- **Out of memory**: Reduce `model_input_size` or use the tiny model
- **Slow processing**: Use smaller input size or tiny model
- **Color bleeding**: Try larger input size or different model
- **Model not found**: Check that models downloaded to `checkpoints/` folder
