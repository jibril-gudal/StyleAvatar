# StyleAvatar

**StyleAvatar: Real-time Photo-realistic Portrait Avatar from a Single Video**

[Lizhen Wang](https://lizhenwangt.github.io/), Xiaochen Zhao, [Jingxiang Sun](https://mrtornado24.github.io/), Yuxiang Zhang, [Hongwen Zhang](https://hongwenzhang.github.io/), [Tao Yu](https://ytrock.com/), [Yebin Liu](http://www.liuyebin.com/)

ACM SIGGRAPH 2023 Conference Proceedings

Tsinghua University & NNKOSMOS

[[Arxiv]](https://arxiv.org/abs/2305.00942) [[Paper]](https://www.liuyebin.com/styleavatar/assets/StyleAvatar.pdf) [[Project Page]](https://www.liuyebin.com/styleavatar/styleavatar.html) [[Demo Video]](https://www.liuyebin.com/styleavatar/assets/Styleavatar.mp4)

<div align=center><img src="./docs/teaser.jpg"></div>

### Abstract

>Face reenactment methods attempt to restore and re-animate portrait videos as realistically as possible. Existing methods face a dilemma in quality versus controllability: 2D GAN-based methods achieve higher image quality but suffer in fine-grained control of facial attributes compared with 3D counterparts. In this work, we propose StyleAvatar, a real-time photo-realistic portrait avatar reconstruction method using StyleGAN-based networks, which can generate high-fidelity portrait avatars with faithful expression control. We expand the capabilities of StyleGAN by introducing a compositional representation and a sliding window augmentation method, which enable faster convergence and improve translation generalization. Specifically, we divide the portrait scenes into three parts for adaptive adjustments: facial region, non-facial foreground region, and the background. Besides, our network leverages the best of UNet, StyleGAN and time coding for video learning, which enables high-quality video generation. Furthermore, a sliding window augmentation method together with a pre-training strategy are proposed to improve translation generalization and training performance, respectively. The proposed network can converge within two hours while ensuring high image quality and a forward rendering time of only 20 milliseconds. Furthermore, we propose a real-time live system, which further pushes research into applications. Results and experiments demonstrate the superiority of our method in terms of image quality, full portrait video generation, and real-time re-animation compared to existing facial reenactment methods.

<div align=center><img src="./docs/results.jpg" width = 70%></div>
<p align="center">Fig.1 Facial re-enactment results of StyleAvatar.</p>

<div align=center><img src="./docs/styleavatar.gif" width = 70%></div>
<p align="center">Fig.2 Facial re-enactment results of StyleAvatar.</p>

<div align=center><img src="./docs/pipeline.jpg"></div>
<p align="center">Fig.3 The pipeline of our method.</p>

## Change Log

2023.05.05 We will release code and pre-trained model soon.

2023.07.06 Release the styleunet-related python code and pretrained models.

2023.07.12 Release the preprocessing code and a training video.

2023.07.26 Release the full styleavatar python code.

2023.07.31 Update the styleavatar python code and upload the pretrained models for styleavatar.

## Python Requirements
- Python 3.8
- PyTorch 2.0.1
- torchvision 0.15.2
- CUDA 11.7
- opencv-python
- numpy
- tqdm
- ninja
- mediapipe

or

```
conda env create -f styleavatar.yaml
```

You need to compile the ops provided by [stylegan2-pytorch](https://github.com/rosinality/stylegan2-pytorch) using ninja:

```
cd styleunet/networks/stylegan_ops
python3 setup.py install
```

## Preprocess (Updated for styleavatar, please use the latest code)

We provide a python code and an exe file for the preprocessing of a single portrait video. We will crop the video, then render the tracked FaceVerse model with texture and uv vertex colors. 

**Note: you need to use the same method for preprocessing and testing.**

1. Python: Jittor-based tracking of faceverse model v3 (full head model, including two eyeballs) using [Jittor](https://github.com/Jittor/Jittor).

>Python code: see FaceVerse v3 for more details: https://github.com/LizhenWangT/FaceVerse/tree/main/faceversev3_jittor. **`--crop_size` should be 1024 for styleunet and 1536 for full styleavatar.** Other items wil not affect the results.

>When testing with another video, you also need to generate the images using the code above. use `--id_folder path-to-processed_data` should be the training folder containing `id.txt` and `exp.txt`. If you are testing cross person re-enactment cases and **only if the source actor's expression in the first frame is neutral expression**, use `--first_frame_is_neutral` to improve the results.

>Details: As shown in Line 129-132 of `FaceVerse/faceversev3_jittor/tracking_offline_cuda.py`, you need to change the first 150 dims (shape parameters of another person) of the jittor tensor `coeffs` in Line 123 to the values (shape parameters of the source actor) in `id.txt` generated by `FaceVerse/faceversev3_jittor/tracking_offline_cuda.py`. If you are testing cross person re-enactment cases and **only if the source actor's expression in the first frame is neutral expression**, as shown in Line 133-134, add the first expression in `exp.txt` to the exp param (id and exp are coupling with each other, so this operation can improve the cross-person results). 

**Note: We borrow some code and a checkpoint from [RobustVideoMatting](https://github.com/PeterL1n/RobustVideoMatting), we thank the authors of RVM for their great work.**

2. Exe (Built on sm_86 RTX 30XX GPU with CUDA 11.3, CUDNN 8.6.0 and TensorRT 8.5.3.1): Windows exe version can be downloaded in [Google Drive](https://drive.google.com/file/d/1AcciXcmrtrGEZ40XCwjG3dkBHq7T_zHq/view?usp=sharing) or [Baidu Netdisk](https://pan.baidu.com/s/1XRQ72viwSXJTsWS1wKKn2Q?pwd=4i9y) password: 4i9y, which is much faster but cannot be used on RTX 40xx GPU. If you miss some **cudnn dll files**, please download them in [Google Drive](https://drive.google.com/file/d/1lGYgwZ3_vLAlbEoNIzmR4H5WwwPNQcIa/view?usp=sharing) or [Baidu Netdisk](https://pan.baidu.com/s/1nVfBu3wRQVgWDMCvyvFwSQ?pwd=bsm6) password: bsm6.

>Modify the file `info.json` before you run your own model: Change the input video and save dir path to your path. `"mode": 0` for preprocessing. `skip_frames` is used to skip the first several frames without portrait regions. **`crop_size` should be 1024 for styleunet and 1536 for full styleavatar.** For cross-person testing, you should change the `change_id` to `true` and give the input id_path.

We also provide a training video presented in our paper: [Google Drive](https://drive.google.com/file/d/1DlH2rReDMuq1kGbDSxjBul9EJ6YLfQRh/view?usp=sharing)
or [Baidu Netdisk](https://pan.baidu.com/s/1Ge5Fi97_C3gdATeWLT4qDg?pwd=pojq) password: pojq

## Full StyleAvatar

### Pretrain Model

StyleAvatar pretrained models, please put the download models to `styleavatar/pretrained/xxx.pt`, an TensorRT engine file has been uploaded in the windows exe link:

```
model 0 (trained on lizhen's video, python version): https://drive.google.com/file/d/128QYYkfJ3dQ9bO_5qDhnGkGjbynlDJ3j/view?usp=sharing
model 1 (trained on lizhen's video, exe version): https://drive.google.com/file/d/1f_iVravaL4oi9TC1kIld1d7pQg1VZw_B/view?usp=sharing
```
or Baidu Netdisk: https://pan.baidu.com/s/1PzDV6fiBdfPqM4aiKnWo9w?pwd=62cn password: 62cn

Reenactment example of the pretrained model (python):

<div align=center><img src="./docs/styleavatar_offline.gif" width = 100%></div>


### Training and testing

Train your styleavatar model or test the pretrained model or transfer the model to onnx then to tensorrt (TensorRT 8.5.3.1 with CUDA 11.3 on NVIDIA RTX 30xx)

The training can start with the pretrained *model 0* or *model 1* above.
```
cd styleavatar
# for single GPU
python train.py --batch 3 --ckpt pretrained/xxxx.pt path-to-dataset
```

Testing or use the pretrained models.
```
cd styleavatar
python test.py --render_dir path-to-dataset/render --uv_dir path-to-dataset/uv --ckpt pretrained/xxx.pt --save_dir output/xxxx
```

Training with multi-GPUs or transfer to onnx and tensorrt.
```
cd styleavatar
# train, for multi-GPUs
CUDA_VISIBLE_DEVICES=0,1 python -m torch.distributed.launch --nproc_per_node=2 --master_port='1234' train.py --batch 3 --ckpt pretrained/xxxx.pt path-to-dataset

# torch2onnx, --for_cpp will add BGR2RGB, BHWC2BCHW, (0,255)-to-(-1,1) into the model 
python torch2onnx.py --test_img path-to-dataset/render/000000.png --uv_img path-to-dataset/uv/000000.png --ckpt logs/checkpoint/xxxxxx.pt --save_name xxx --for_cpp

# onnx2trt on Windows (unstable for fp16, try again if failed)
tensorrt/trtexec.exe --onnx=pretrained/xxx.onnx --saveEngine=pretrained/xxx_16.engine --fp16
```

Windows exe testing (the same exe in preprocessing), only if your engine is converted by CUDA 11.3 and TensorRT 8.5.3.1 on a RTX 30XX GPU:

```
"mode": 0 for preprocessing; 1 for testing. 
"crop_size": should be 1024 for styleunet and 1536 for full styleavatar.
"skip_frames": skip the first several frames without portrait regions. 
"engine_path": tensorrt model path
"change_id": only for mode 0, true for generating FaceVerse images using an exsisting id.txt
"id_path": path to id.txt
"save_results": slower when true
"input_video": "0" for web camera (you can use a simple USB camera), "path-to-video" for real video input
```

## StyleUNet

We propose styleunet for high-resolution image-to-image translation tasks, which also refers to the ablation of "single-styleunet" in our paper. We test this network for some image-to-image translation tasks:


Mode 0 (face inpainting trained on [FFHQ](https://github.com/NVlabs/ffhq-dataset))

Mode 1 (face superresolution trained on [FFHQ](https://github.com/NVlabs/ffhq-dataset))

Mode 2 (face retouching trained on [FFHQR](https://github.com/skylab-tech/ffhqr-dataset))

Mode 3 (3dmm to portrait image trained on a single video) refers to the ablation of "single-styleunet" in our paper, which can transfer a 3dmm rendering to a real portrait image.

| Mode | Input | Output |
| :--- | :---: | :---: |
| <div style="width: 50px"> 0 | <img src="styleunet/input/inpainting/mode0_test1.png" width="300px" height="300px"> | <img src="styleunet/output/inpainting/mode0_test1.png" width="300px" height="300px"> |
| <div style="width: 50px"> 0 | <img src="styleunet/input/inpainting/mode0_test2.png" width="300px" height="300px"> | <img src="styleunet/output/inpainting/mode0_test2.png" width="300px" height="300px"> |
| <div style="width: 50px"> 1 | <img src="styleunet/input/superresolution/mode1_test1.png" width="300px" height="300px"> | <img src="styleunet/output/superresolution/mode1_test1.png" width="300px" height="300px"> |
| <div style="width: 50px"> 1 | <img src="styleunet/input/superresolution/mode1_test2.png" width="300px" height="300px"> | <img src="styleunet/output/superresolution/mode1_test2.png" width="300px" height="300px"> |
| <div style="width: 50px"> 2 | <img src="styleunet/input/retouching/mode2_test3.png" width="300px" height="300px"> | <img src="styleunet/output/retouching/mode2_test3.png" width="300px" height="300px"> |
| <div style="width: 50px"> 2 | <img src="styleunet/input/retouching/mode2_test2.png" width="300px" height="300px"> | <img src="styleunet/output/retouching/mode2_test2.png" width="300px" height="300px"> |
| <div style="width: 50px"> 3 | <img src="styleunet/input/tdmm/mode3_test3.png" width="300px" height="300px"> | <img src="styleunet/output/tdmm/mode3_test3.png" width="300px" height="300px"> |
| <div style="width: 50px"> 3 | <img src="styleunet/input/tdmm/mode3_test2.png" width="300px" height="300px"> | <img src="styleunet/output/tdmm/mode3_test2.png" width="300px" height="300px"> |


### Pretrain Model

Styleunet pretrained models, please put the download models to `styleunet/pretrained/xxx.pt`:

```
model 0 (face-inpainting): https://drive.google.com/file/d/1XCdNiKx0qCJW_BryERJLLJ2ivppW80Cu/view?usp=sharing
model 1 (face-superresolution): https://drive.google.com/file/d/10V4swYfUvcpHMw76hZL-ggtTuIAraLGQ/view?usp=sharing
model 1 with discriminator (face-superresolution): https://drive.google.com/file/d/1hvbXHzAxs9MJfCj8rVNj4iqsrhA5MGUs/view?usp=sharing
model 2 (face-retouching): https://drive.google.com/file/d/1XhVcrQx_pzcfAw7aHsB410i379HWHSsr/view?usp=sharing
model 3 of lizhen (3dmm-to-portrait image): https://drive.google.com/file/d/1kFljZ5uUvcTan6dZG0_m2aq9v7DJzpiP/view?usp=sharing
model 3 of lizhen full model for pretraining (3dmm-to-portrait image): https://drive.google.com/file/d/1wN1bCqFGP40K3u0Gxf16pLu7iUdNgGOP/view?usp=sharing
```

or Baidu Netdisk: https://pan.baidu.com/s/1JAS6omTZnt5MaxATGP-wCA?pwd=tp16 password: tp16

### Training and testing

Train your styleunet model or test the pretrained model or transfer the model to onnx then to tensorrt

The training can start with the pretrained *model 1 with discriminator* above.
```
cd styleunet
# for single GPU
# train, mode 0 for face inpainting; 1 for face superresolution; 2 for face retouching; 3 for 3dmm-to-portriat image
python train.py --batch 3 --ckpt pretrained/xxxx.pt --mode 0 --augment --augment_p 0.01 path-to-dataset
python train.py --batch 3 --ckpt pretrained/xxxx.pt --mode 1 --augment --augment_p 0.01 path-to-dataset
python train.py --batch 3 --ckpt pretrained/xxxx.pt --mode 2 --augment --augment_p 0.01 path-to-dataset
python train.py --batch 3 --ckpt pretrained/xxxx.pt --mode 3 path-to-dataset
```

Testing or use of the pretrained models.
```
cd styleunet
# test, --skin_whiten 0-1 if you want, --use_alignment if your input image is not a 1024x1024 aligned portriat image, --iter 1~3 for iterations of face retouching (only for mode 2)
python test.py --input_dir input/inpainting --ckpt pretrained/inpainting_g_ema.pt --save_dir output/inpainting --mode 0
python test.py --input_dir input/superresolution --ckpt pretrained/superresolution_g_ema.pt --save_dir output/superresolution --mode 1
python test.py --input_dir input/retouching --ckpt pretrained/retouching_g_ema.pt --save_dir output/retouching --mode 2 --skin_whiten 0.5 --iter 2 --use_alignment
python test.py --input_dir input/tdmm --ckpt pretrained/tdmm_lizhen.pt --save_dir output/tdmm --mode 3
```

Training with multi-GPUs or transfer to onnx and tensorrt.
```
cd styleunet
# train, for multi-GPUs
CUDA_VISIBLE_DEVICES=0,1 python -m torch.distributed.launch --nproc_per_node=2 --master_port='1234' train.py --batch 3 --mode 0 --ckpt pretrained/xxxx.pt --augment --augment_p 0.2 path-to-dataset

# torch2onnx, --for_cpp will add BGR2RGB, BHWC2BCHW, (0,255)-to-(-1,1) into the model 
python torch2onnx.py --test_img path-to-dataset/render/000000.png --ckpt logs/checkpoint/tdmm_xxxxxx.pt --save_name xxx --mode 3 --for_cpp

# onnx2trt on Windows
tensorrt/trtexec.exe --onnx=pretrained/xxx.onnx --saveEngine=pretrained/xxx_16.engine --fp16
```

## Citation

If you use our code for your research, please consider citing:
```
@inproceedings{wang2023styleavatar,
  title={StyleAvatar: Real-time Photo-realistic Portrait Avatar from a Single Video},
  author={Wang, Lizhen and Zhao, Xiaochen and Sun, Jingxiang and Zhang, Yuxiang and Zhang, Hongwen and Yu, Tao and Liu, Yebin},
  booktitle={ACM SIGGRAPH 2023 Conference Proceedings},
  pages={},
  year={2023}
}
```

## Acknowledgement & License
The code is partially borrowed from [stylegan2-pytorch](https://github.com/rosinality/stylegan2-pytorch) and [RobustVideoMatting](https://github.com/PeterL1n/RobustVideoMatting). And many thanks to the volunteers participated in data collection. Our License can be found in [LICENSE](./LICENSE). We also use Eigen, TensorRT, CUDA, OpenGL(glfw & glad), cnpy, GML, Json, OpenCV, mediapipe for the cpp code. Thanks to the authors of these open source libraries.

