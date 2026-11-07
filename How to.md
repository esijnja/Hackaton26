How to

## Link USB devices to WSL

1. Open your PowerShell terminal.
2. type:
   ```
   usbipd list
   ```
3. note the `BUSID` of the USB devices `USB-Enhanced-SERIAL CH343`.
4. Type the following command to attach the USB device to WSL:
   ```
   usbipd attach --wsl --busid <BUSID>

1. Open your WSL terminal.
2. Type the following command to list all available USB devices:
   ```
   lsusb
   ```
1. Access the USB device by typing the following command:
   ```
   sudo chmod 666 /dev/ttyACM0
   ```
   ```
   sudo chmod 666 /dev/ttyACM0
   ```
   ``` 
   ls /dev/video*
   ```

   ```
   sudo chmod 666 /dev/video0
   ```
   ```
   sudo chmod 666 /dev/video1
   ```

1. Find which is which 

``` 
   lerobot-find-port
```

```
   lerobot-find-cameras 
```

2. write down leader and follower address

```
   leader: /dev/ttyACM0
   follower: /dev/ttyACM1
```

## Calibrate the leader and follower

Follower:

``` bash
lerobot-calibrate --robot.type=so101_follower --robot.port=/dev/ttyACM1 --robot.id=hackaton_follower_arm 
```

Leader:

> Doesn't work, `so101_leader` is not a valid robot type. Skip the step and go to Teleoperate the leader and follower.

``` bash
lerobot-calibrate --robot.type=so101_leader --robot.port=/dev/ttyACM1 --robot.id=hackaton_leader_arm 
```

## Teleoperate the leader and follower

``` bash
lerobot-teleoperate --robot.type=so101_follower --robot.port=/dev/ttyACM1 --robot.id=hackaton_follower_arm --teleop.type=so101_leader --teleop.port=/dev/ttyACM0 --teleop.id=hackaton_leader_arm
```

Teleoperation with camera:

``` bash 
lerobot-teleoperate \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 0, width: 640, height: 360, fps: 30}, wrist: {type: opencv, index_or_path: 2, width: 640, height: 480, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --display_data=true
```

## Recording

``` bash
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 0, width: 640, height: 360, fps: 30}, wrist: {type: opencv, index_or_path: 2, width: 640, height: 480, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --dataset.num_episodes=30 \
    --dataset.single_task="put the orange brick in a yellow form" \
    --dataset.streaming_encoding=true \
    --dataset.root=${HOME}/hackaton26/recordings_data/so101_dataset_01 \
    --display_data=true
```

With only one camera :

``` bash
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 0, width: 640, height: 360, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --dataset.num_episodes=5 \
    --dataset.single_task="put the orange brick in a yellow form" \
    --dataset.streaming_encoding=true \
    --dataset.root=${HOME}/hackaton26/recordings_data/so101_dataset_01 \
    --display_data=true
```

With only one camera and repoid:

``` bash
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 0, width: 640, height: 360, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --dataset.repo_id=${HF_USER}/hackaton26_test \
    --dataset.num_episodes=5 \
    --dataset.single_task="put the orange brick in a yellow form" \
    --dataset.streaming_encoding=true \
    --display_data=false
```

## Training

``` bash
lerobot-train \
    --dataset.repo_id=${HF_USER}/hackaton26_test_20261107_182356 \
    --policy.type=act \
    --output_dir=outputs/train/act_so101_test \
    --job_name=act_so101_test \
    --policy.device=cuda \
    --trackio.enable=true \
    --policy.repo_id=${HF_USER}/policy_test \
    --steps=20000
```






Camera on stand
```
 v4l2-ctl --all -d /dev/video0
```
```
Driver Info:
        Driver name      : uvcvideo
        Card type        : web camera: UVC Camera
        Bus info         : usb-vhci_hcd.0-1
        Driver version   : 6.6.87
        Capabilities     : 0x84a00001
                Video Capture
                Metadata Capture
                Streaming
                Extended Pix Format
                Device Capabilities
        Device Caps      : 0x04200001
                Video Capture
                Streaming
                Extended Pix Format
Priority: 2
Video input : 0 (Camera 1: ok)
Format Video Capture:
        Width/Height      : 640/360
        Pixel Format      : 'YUYV' (YUYV 4:2:2)
        Field             : None
        Bytes per Line    : 1280
        Size Image        : 460800
        Colorspace        : sRGB
        Transfer Function : Default (maps to sRGB)
        YCbCr/HSV Encoding: Default (maps to ITU-R 601)
        Quantization      : Default (maps to Limited Range)
        Flags             :
Crop Capability Video Capture:
        Bounds      : Left 0, Top 0, Width 640, Height 360
        Default     : Left 0, Top 0, Width 640, Height 360
        Pixel Aspect: 1/1
Selection Video Capture: crop_default, Left 0, Top 0, Width 640, Height 360, Flags:
Selection Video Capture: crop_bounds, Left 0, Top 0, Width 640, Height 360, Flags:
Streaming Parameters Video Capture:
        Capabilities     : timeperframe
        Frames per second: 30.000 (30/1)
        Read buffers     : 0

User Controls

                     brightness 0x00980900 (int)    : min=0 max=100 step=5 default=50 value=50
                       contrast 0x00980901 (int)    : min=0 max=100 step=5 default=50 value=50
                     saturation 0x00980902 (int)    : min=0 max=100 step=5 default=50 value=50
                            hue 0x00980903 (int)    : min=0 max=100 step=5 default=50 value=50
        white_balance_automatic 0x0098090c (bool)   : default=0 value=0
           power_line_frequency 0x00980918 (menu)   : min=0 max=2 default=2 value=2 (60 Hz)
                                0: Disabled
                                1: 50 Hz
                                2: 60 Hz
                  hue_automatic 0x00980919 (bool)   : default=0 value=0
      white_balance_temperature 0x0098091a (int)    : min=1500 max=15000 step=60 default=5000 value=15000

Camera Controls

         exposure_time_absolute 0x009a0902 (int)    : min=10 max=2000 step=10 default=100 value=2000
```

```
v4l2-ctl --list-formats-ext -d /dev/video0
```
```
ioctl: VIDIOC_ENUM_FMT
        Type: Video Capture

        [0]: 'MJPG' (Motion-JPEG, compressed)
                Size: Discrete 1920x1080
                        Interval: Discrete 0.033s (30.000 fps)
                Size: Discrete 1280x720
                        Interval: Discrete 0.033s (30.000 fps)
                Size: Discrete 640x360
                        Interval: Discrete 0.033s (30.000 fps)
        [1]: 'YUYV' (YUYV 4:2:2)
                Size: Discrete 640x360
                        Interval: Discrete 0.033s (30.000 fps)
        [2]: 'H264' (H.264, compressed)
                Size: Discrete 1920x1080
                        Interval: Discrete 0.033s (30.000 fps)
                Size: Discrete 1280x720
                        Interval: Discrete 0.033s (30.000 fps)
                Size: Discrete 640x360
                        Interval: Discrete 0.033s (30.000 fps)
```

```
v4l2-ctl -d /dev/video0 --set-fmt-video=width=640,height=360,pixelformat=YUYV
```