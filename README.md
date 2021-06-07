# yolov5_deepsort_tensorrt

## Introduction

This repo uses **YOLOv5** and **DeepSORT** to implement object tracking algorithm. Also using **TensorRTX** to convert model to engine, and deploying all code on the NVIDIA Xavier with **TensorRT** further.

**NVIDIA Jetson Xavier NX**  and the *X86* architecture works all be ok. 





## Environments

- Ubuntu20.04 or 18.04 with CUDA 10.0 and cuDNN 7.6.5
- NVIDIA NX
- TensorRT 7.0.0.1
- PyTorch 1.7.1_cu11.0 and TorchVision 0.8.2+cu110
- OpenCV-Python 4.2
- pycuda 2021.1



## Speed

The speeds of DeepSort depend on the target number in the picture.

| Backbone          | Without TensorRT      | With TensorRT          |
| :---------------- | --------------------- | ---------------------- |
| YOLOV5            | 14ms / 71FPS / 1239M  | 10ms /  100FPS / 2801M |
| YOLOV5 + DeepSort | 23ms / 43FPS /  1276M | 16ms / 62FPS / 2842M   |



## Inference

1. clone this repo

   ```shell
   git clone https://github.com/cong/yolov5_deepsort_tensorrt.git
   ```

2. Run

   ```
   python demo.py
   ```

   ![test.gif](https://pic1.zhimg.com/80/v2-d7975d2f02d2cc3bf9baf40acbe43a2a_720w.gif)

## Convert

Convert PyTorch yolov5 weights to TensorRT engine.

**Notice: this repo uses YOLOv5 version 4.0 , so TensorRTX should uses version yolov5-v4.0 !**

1. generate `***.wts` from PyTorch with `***.pt`.

   ```shell
   git clone -b v5.0 https://github.com/ultralytics/yolov5.git
   git clone https://github.com/wang-xinyu/tensorrtx.git
   # download https://github.com/ultralytics/yolov5/releases/download/v5.0/yolov5s.pt
   cp {tensorrtx}/yolov5/gen_wts.py {ultralytics}/yolov5
   cd {ultralytics}/yolov5
   python gen_wts.py yolov5s.pt
   # a file 'yolov5s.wts' will be generated.
   ```

2. build tensorrtx / yolov5 and generate `***.engine`

   ```shell
   cd {tensorrtx}/yolov5/
   # update CLASS_NUM in yololayer.h if your model is trained on custom dataset
   mkdir build
   cd build
   cp {ultralytics}/yolov5/yolov5s.wts {tensorrtx}/yolov5/build
   cmake ..
   make
   # serialize model to plan file
   sudo ./yolov5 -s [.wts] [.engine] [s/m/l/x/s6/m6/l6/x6 or c/c6 gd gw]
   # deserialize and run inference, the images in [image folder] will be processed.
   sudo ./yolov5 -d [.engine] [image folder]
   # For example yolov5s
   sudo ./yolov5 -s yolov5s.wts yolov5s.engine s
   sudo ./yolov5 -d yolov5s.engine ../samples
   # For example Custom model with depth_multiple=0.17, width_multiple=0.25 in yolov5.yaml
   sudo ./yolov5 -s yolov5_custom.wts yolov5.engine c 0.17 0.25
   sudo ./yolov5 -d yolov5.engine ../samples
   ```


3. Once the images generated, as follows. _zidane.jpg and _bus.jpg, convert completed!

## Customize

1. Training your own model.
2. Convert your own model to engine(TensorRTX's version must same as YOLOV5's version).
3. Replace the `***.engine` and `libmyplugins.so` file.

## Optional setting

- If you feel that it is helpful to you, please give me a star. Thx!  :)
- For more information you can visit the [Blog](http://wangcong.net).
