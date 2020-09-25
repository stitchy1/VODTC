# Video Object Detection, Tracking and Counting

Pytorch implementation of an Optical Flow network and its combination with YOLO, compressed flownet with SElayer plugged-in, applied for realtime video object detection, tracking and counting.

Inference using fp16 (half-precision) is also supported.

For more help, type <br />
    
    python3 count.py --help
    
### Python requirements 
Currently, the code supports python3.x
* numpy 
* PyTorch == 1.3.0
* torchvision == 0.4.1
* scipy 
* scikit-image
* tensorboardX
* colorama, tqdm, setproctitle 
* opencv
* paho-mqtt


## Installation 

#### get source files
    cd Optical-Flow
    tar -xvf networks.tar
    tar -xvf git.tar

#### install custom layers
    cd Optical-Flow
    bash install.sh

## models available
Below are the different flownet2 and YOLOv3 neural network architectures that are provided. <br />
A batchnorm version for each network is also available.

 - **FlowNet2S [154MB]**
 - **FlowNet2 [650MB]**
 - **efficientnetS [19MB]** # compressed model of flownet2
 - **yolov3 [248MB]**
 - **yolov3-tiny [35MB]**

## Results

|                           <sub>model</sub>                           |      <sub>speed 640×448</br></sub>     |      <sub>min pixel</br></sub>      |  <sub>max pixel</br></sub>  |  <sub>noise</br></sub>  |  <sub>mAP on COCO</br></sub>  |
|:----------------------------------------------------------------------:|:--------------------------------------------:|:--------------------------------------------:|:--------------------------------:|:--------------------------------:|:--------------------------------:|
| <sub>[FlowNet2S]</sub> |       <sub>30.3ms(33FPS)</sub>       |       <sub>4</sub>       |               <sub>**200**</sub>              |               <sub>3±2</sub>             |               -             |
| <sub>[FlowNet2]</sub> | <sub>142.9ms(7FPS)</sub> | <sub>**1**</sub> | <sub>**200**</sub> | <sub>**0.015±0.005**</sub>  |               -           |
| <sub>[efficientnetS]</sub> | <sub>**20.1ms(50FPS)**</sub> | <sub>4</sub> | <sub>100</sub> | <sub>0.45±0.05</sub>  |               -             |
|<sub>[YOLOv3]</sub> | <sub>45.5ms(22FPS)</sub> | - | - | - |               <sub>**55.3**</sub>             |
|<sub>[YOLOv3-tiny]</sub> | <sub>**22.7ms(44FPS)**</sub> | - | - | - |               <sub>33.1</sub>             |

**Note:** 
- Speed are tested on NVIDIA GTX 1060, i7-8750
- width and height of video should be multiple of 64

## Inference
#### realtime.py for testing optical flow
    python3 realtime.py

#### track.py for object detection and tracking
    python3 track.py

#### track.py for object detection and counting
    python3 count.py
    
#### Example of counting with flownet2 and yolov3 in demo
    nohup python3 count.py >/dev/null 2>&1 & \
    --video_url videos/watone_pp.mp4 \
    --interval 5 --frame_hop 2 \
    --pre_class 0 2 --iou_thresh 0.3 --max_err 100 \
    --width 640 --height 448 \
    --center_position_x 0.5 --center_position_y 0.5 \
    --empty_count 1000
    --conf_thres 0.4 --nms_thres 0.3 \
    --model_def config/yolov3.cfg \
    --weights_path weights/yolov3.weights \
    --show_video 0 --gpu 1 --multiprocess 1 --mqtt 0
    
#### in-out direction and counting position can be modified in count.py
#### if not show_video:
        count_position_in[0] = 0.6
        count_position_out[0] = 0.6
        count_position_in[2] = 0.5
        count_position_out[2] = 0.5
        direction_in[0] = 'r'
        direction_out[0] = 'l'
        direction_in[2] = 'd'
        direction_out[2] = 'u'
#### if show_video:
        you can simply draw lines on the screen
        type "i" or "o" as in-line and out-line, then drag the mouse to draw lines
        you can redraw lines multiple times before typing "Enter"
        type "Enter" to finish all lines drawing
        type "Esc" to delete all lines
    
#### modify the rstp stream url in count.py
    url = 'xxx'
    
#### C0C0 80 classes table
        0             1           2              3          4         5         6           7           8              9
    0   person        bicycle     car            motorbike  airplane  bus       train       truck       boat           traffic light
    10  fire hydrant  stop sign   parking meter  bench      bird      cat       dog         horse       sheep          cow
    20  elephant      bear        zebra          giraffe    backpack  umbrella  handbag     tie         suitcase       frisbee
    30  skis          snowboard   sports ball    kite       ballbat   glove     skateboard  surfboard   tennis racket  bottle
    40  wine glass    cup         fork           knife      spoon     bowl      banana      apple       sandwich       orange
    50  broccoli      carrot      hot dog        pizza      donut     cake      chair       sofa        potted plant   bed
    60  dining table  toilet      TV monitor     laptop     mouse     remote    keyboard    cellphone   microwave      oven
    70  toaster       sink        refrigerator   book       clock     vase      scissors    teddy bear  hair drier     toothbrush
    
## Demo
<video preload="none">
    <source source="./demo/optflow_demo_1.mp4" type="video/mp4">
</video>
<video preload="none">
    <source source="./demo/optflow_demo_2.mp4" type="video/mp4">
</video>

## Todo
- [x] edge processing
- [x] occlusion processing
- [x] inertia estimation
- [x] multi-class counting
- [x] multi-direction counting
- [x] multi-threading
- [x] MT deployment - PyTorch 1.3.0
- [ ] on firefly with npu
- [ ] LFFD detectors
- [x] drawing line on the screen
