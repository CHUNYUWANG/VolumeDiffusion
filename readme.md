# VolumeDiffusion

## Overview

This is the official repo of the paper [VolumeDiffusion: Flexible Text-to-3D Generation with Efficient Volumetric Encoder](https://arxiv.org/abs/23xx.xxxxx).

### TL;DR

VolumeDiffusion is a **fast** and **scalable** text-to-3D generation method that gives you a 3D object within seconds/minutes.

### Result

https://github.com/tzco/VolumeDiffusion/assets/97946330/87875b52-3b0c-4364-a5a4-b949d107359f

### Method

<img src='assets/method.png'>

Framework of VolumeDiffusion. It comprises the volume encoding stage and the diffusion modeling stage.

The encoder unprojects multi-view images into a feature volume and do refinements.

The diffusion model learns to predict ground-truths given noised volumes and text conditions.

### Citation

```
@Article{VolumeDiffusion2023,
  author  = {Zhicong Tang, Shuyang Gu, Chunyu Wang, Ting Zhang, Jianmin Bao, Dong Chen and Baining Guo},
  journal = {arXiv:23xx.xxxxx},
  title   = {VolumeDiffusion: Flexible Text-to-3D Generation with Efficient Volumetric Encoder},
  year    = {2023},
}
```

## Installation

Run `sh install.sh` and start enjoying your generation!

We recommand and have tested the code with the docker image `pytorch/pytorch:2.1.0-cuda12.1-cudnn8-devel`.

## Inference

Download the [Volume Encoder](https://facevcstandard.blob.core.windows.net/t-zhitang/release/VolumeDiffusion/encoder.pth?sv=2023-01-03&st=2023-12-15T08%3A39%3A34Z&se=2099-12-16T08%3A39%3A00Z&sr=b&sp=r&sig=hzx4TL0DCMfL4p5%2BevF5OIgo5Plfj9Eevixz00QCPyU%3D) and [Diffusion Model](https://facevcstandard.blob.core.windows.net/t-zhitang/release/VolumeDiffusion/diffusion.pth?sv=2023-01-03&st=2023-12-15T08%3A38%3A44Z&se=2099-12-16T08%3A38%3A00Z&sr=b&sp=r&sig=oxuqYK6FSRiecxeSl1R5SbUW%2Bwiw0HQQNo6175YIn4k%3D) checkpoints and put them right here.

We use [DeepFloyd/IF-I-XL-v1.0](https://huggingface.co/DeepFloyd/IF-I-XL-v1.0) for refinement. Ensure you have the access and login with `huggingface-cli login --token your_huggingface_token`.

Then you can generate objects with

```
python inference.py --prompt "a yellow hat with a bunny ear on top" --image_channel 4
```

Also, you can use different prompts for diffusion generation and refinement. This is useful when generating complicated object with multiple concepts and attributes:

```
python inference.py --prompt "a yellow hat with a bunny ear on top" --prompt_refine "a yellow hat with a white bunny ear on top" --image_channel 4
```

## Training

You can train with your custom dataset. We also provide `assets/example_data.zip` as an example of data format.

To train a volume encoder:

```
python train_encoder.py path/to/object_list path/to/save --data_root path/to/dataset --test_list path/to/test_object_list
```

To train a diffusion model:

```
python train_diffusion.py path/to/object_list path/to/save --data_root path/to/dataset --test_list path/to/test_object_list --encoder_ckpt path/to/trained_volume_encoder.pth --encoder_mean pre_calculated_mean --encoder_std pre_calculated_std
```

We recommend pre-calculating the `mean` and `std` of the outputs of the trained volume encoder on the dataset (or part of the dataset). This encourages the inputs close to the standard normal distribution and benefits the training of the diffusion model. Or you can directly set `mean=0` and `std=20`.

## More results

### Generations

<img src='assets/results_1.png'>

<img src='assets/results_2.png'>

<img src='assets/results_3.png'>

<img src='assets/results_4.png'>

<img src='assets/results_5.png'>

<img src='assets/results_6.png'>

### Diversity

<img src='assets/results_7.png'>

### Flexibility

<img src='assets/results_8.png'>

## Acknowledgments

This code borrows heavily from [stable-dreamfusion](https://github.com/ashawkey/stable-dreamfusion).

We use [threestudio](https://github.com/threestudio-project/threestudio) and do two minor modifications for the refinement stage.

We use [dpm-solver](https://github.com/LuChengTHU/dpm-solver) as the solver of diffusion model inference.

The codes of diffusion and UNet model are borrowed from [glide-text2im](https://github.com/openai/glide-text2im).

The codes of EMA are borrowed from [pytorch_ema](https://github.com/fadel/pytorch_ema).
